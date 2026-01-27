---
title: "The one with heavy dependencies in a Lambda function"
seoDescription: "Learn how to overcome the AWS Lambda deployment size limit by using AWS EFS to mount a shared file system and load packages larger than the 250 MB"
datePublished: Wed Jun 07 2023 10:07:06 GMT+0000 (Coordinated Universal Time)
cuid: cliljpmrh001909l7ds06axyb
slug: the-one-with-heavy-dependencies-in-a-lambda-function
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/snNHKZ-mGfE/upload/cccef6d253bfc42a19ebfba4484293cd.jpeg
tags: aws, python, aws-lambda, amazon-efs, aws-lambda-layer

---

Recently, after finishing developing the Lambda function I've been talking about in [previous](https://davidmontesdeoca.dev/the-one-with-requests-to-localhost-from-aws-sam) [articles](https://davidmontesdeoca.dev/the-one-with-access-denied-to-aws-in-production) and creating the corresponding merge request, the [CI pipeline](https://docs.gitlab.com/ee/ci/) in GitLab that automatically deploys changes to the *staging* environment started failing.

## A bit of context

Before I tell you why it started failing, I would like to give you some context about the Lambda function itself and show you some relevant parts of our infrastructure as code ([IaC](https://about.gitlab.com/topics/gitops/infrastructure-as-code/)).

Let me start by showing you the structure of the directory containing the code:

```bash
$ tree -a
.
├── .python-version
├── app.py
├── app_test.py
└── requirements.txt
```

The dependencies are included in the `requirements.txt` file, as shown below:

```txt
boto3
graypy
numpy
pandas
requests
scikit-learn
```

> Note: **numpy** is a dependency of both **pandas** and **scikit-learn**, so it's not necessary to include it in that file, but I prefer to make it explicit to show that we are using it in our code.

The *build* stage of the CI pipeline is configured as follows:

```yaml
build:
  image: python:3.10-slim
  stage: build
  variables:
    FOLDER: "recommender_lambda"
  script:
    - |-
      cd ${FOLDER}
      apt update && apt install -y zip gcc
      pip install --upgrade pip
      pip install -r requirements.txt -t vendor
      zip -r ../${FOLDER}.zip .
  artifacts:
    paths:
      - ./${FOLDER}.zip
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

In addition to the Lambda function and the tests, the dependencies are installed in the `vendor` directory and included in the generated *zip* file.

The *deploy* stage of the CI pipeline is configured as follows:

```yaml
deploy:
  stage: deploy
  variables:
    FOLDER: "recommender_lambda"
    ENV: "staging"
  script:
    - aws lambda update-function-code --function-name ${FOLDER}-${ENV} --zip-file fileb://./${FOLDER}.zip
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

Here we are using [AWS CLI](https://aws.amazon.com/cli/) to [update the Lambda function](https://docs.aws.amazon.com/cli/latest/reference/lambda/update-function-code.html) with the contents of the *zip* file generated in the previous stage.

## The infamous error

So the error that the CI pipeline returned when trying to deploy the code to *staging* environment was:

> An error occurred (RequestEntityTooLargeException) when calling the UpdateFunctionCode operation: **Request must be smaller than 69905067 bytes** for the UpdateFunctionCode operation

The error message says that the request must be **smaller than 70 MB**.

I have worked several times with AWS Lambda but I had never faced this error before, so the first thing I did was to research this limit and I found [a useful answer](https://stackoverflow.com/a/50742008/1477964) on StackOverflow. There you can find a link to the [official AWS Lambda documentation](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html#function-configuration-deployment-and-execution), where the limits for uploaded files to deploy functions are specified:

| Resource | Quota |
| --- | --- |
| [Deployment package (.zip file archive)](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-package.html) size | 50 MB (zipped, for direct upload) |
|  | 250 MB (unzipped) |
|  | This quota applies to all the files you upload, including layers and custom runtimes. |
|  | 3 MB (console editor) |

However, those limits don't match the one specified in the error message, which let's remember was 70 MB. So why that 20 MB difference?

The same StackOverflow answer includes a quote taken from a blog post, no longer available, theorizing about that:

> The 20 MB addition presumably is there to account for request overhead involved with the AWS API (e.g. base64 encoding of the zip file data).

In my case, the generated *zip* file weighed **106 MB compressed** and **370 MB uncompressed**.

Clearly way over the allowed limits. I had to look for an alternative.

## Layers

According to the [official documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html), Lambda layers provide a convenient way to package libraries and other dependencies that you can use with your Lambda functions.

Using layers has some advantages:

* Reduces the size of uploaded deployment archives.
    
* Makes it faster to deploy your code.
    
* Reduces cold start times.
    
* Minimizes the overall memory footprint required by the Lambda function.
    
* Can be shared and reused across multiple Lambda functions or even across different AWS accounts.
    

My first approach was creating a separate Lambda layer for each dependency. That way, if I had to update only one of them, I could do it independently.

This Lambda function is developed in [Python](https://docs.aws.amazon.com/lambda/latest/dg/python-package.html), so I had to create a `python` directory in the root of the *zip* file, as indicated in the [official documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html#configuration-layers-path).

Example for `requests` package:

```bash
$ pip install requests -t python
$ zip -r requests-layer.zip python
```

Inside the *requests-layer.zip* file you would find the following directories structure:

```bash
python
├── bin
├── certifi
├── certifi-2023.5.7.dist-info
├── charset_normalizer
├── charset_normalizer-3.1.0.dist-info
├── idna
├── idna-3.4.dist-info
├── requests
├── requests-2.31.0.dist-info
├── urllib3
└── urllib3-2.0.2.dist-info
```

You can follow the [official documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html) or this [great article](https://www.linkedin.com/pulse/add-external-python-libraries-aws-lambda-using-layers-gabe-olokun/) to configure Lambda layers in AWS.

However, this approach didn't work for me due to [another limitation of AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/invocation-layers.html#invocation-layers-using) that I wasn't aware of:

![Cannot reference more than 5 layers](https://user-images.githubusercontent.com/685978/243182959-73330d1a-02cb-44ad-ae82-992892418d5a.png align="left")

You can add up to **5 layers** to a Lambda function.

I realized then that `numpy` didn't have to be included as a layer, since, as I said before, it is a dependency of `pandas` and `scikit-learn`. But still, being aware of the limitation of the number of layers per Lambda function, I preferred to opt for a hybrid approach instead.

*Lighter* dependencies remained defined in the `requirements.txt` file:

```txt
boto3
graypy
requests
```

> Note: Before going any further, I deleted the layers of those packages in AWS, as I no longer needed them.

*Heavier* dependencies remained as layers (`pandas` and `scikit-learn`).

I pushed those changes to the repository in GitLab and after finishing deploying to the *staging* environment, I found that it still didn't work as expected, as I couldn't add all the layers to the Lambda function, due to the well-known 250 MB limitation we talked about earlier.

At that point I wasn't going to give up, so I did some more research and found [this article](https://medium.com/@shimo164/lambda-layer-to-use-numpy-and-pandas-in-aws-lambda-function-8a0e040faa18), with a somewhat different approach to what I had read before.

There it is suggested to download the [whl files](https://realpython.com/python-wheels/) directly from [Python Package Index](https://pypi.org/).

And that's what I did for `pandas`, `numpy` and `scikit-learn`. I created a layer for each of those packages, uploaded them, and this time I was able to correctly add all the layers to the Lambda function.

Finally some progress, but not everything was going to be that easy.

I ran the Lambda function and it started returning the following error:

> \[ERROR\] Runtime.ImportModuleError: Unable to import module 'main': No module named 'requests'.

Remember that `requests` is one of the dependencies defined in the `requirements.txt` file. So why isn't the function finding it?

As much as I searched I couldn't find anything that could be related to my use case. Already a bit discouraged, I accessed the Lambda function through the AWS user interface and downloaded the entire configuration. There I found a reference to an environment variable, `PYTHONPATH`. And that's exactly what I needed.

Although that variable wasn't explicitly defined, its default value is [the path to runtime libraries](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html#configuration-envvars-runtime): `/opt/python`.

As *lighter* dependencies are included within the same directory as the Lambda function, inside `vendor` directory, I defined the following value:

```bash
PYTHONPATH=/var/task/vendor
```

I applied those changes, ran the Lambda function again and a new error appeared related to *heavier* dependencies:

> \[ERROR\] Runtime.ImportModuleError: Unable to import module 'main': No module named 'pandas'

For a moment I thought it was impossible to access all those dependencies at the same time until I remembered how we define the `PATH` environment variable on Unix systems and tried to do something similar:

```bash
PYTHONPATH=/var/task/vendor:/opt/python
```

I applied changes again, ran the Lambda function and this time it kind of worked. At least the code started running, whereas previously it only reached the point where all the *import* statements are at the beginning of the file.

The new error was:

> \[ERROR\] Runtime.ImportModuleError: Unable to import module 'main': No module named 'scipy'

[SciPy](https://scipy.org/) is a dependency of `scikit-learn` and, not only that, it is its heaviest dependency. By far.

This time I found that [AWS provides an optimized layer with this dependency](https://stackoverflow.com/a/58350528/1477964) for some Python versions: `AWSLambda-Python38-SciPy1x`. The latest one is Python 3.8, at least the last time I checked it. Since it wasn't available for Python 3.10, I followed the same approach as before and downloaded the [SciPy whl file](https://pypi.org/project/scipy/#files).

I added the layer with that dependency and ran the Lambda function again. And yet another similar error appeared, another dependency not found.

At that very moment, I realized that I had to look for another alternative, but not before deleting all the previously created layers.

## Shared file system

For the millionth time, I ended up finding a [possible alternative](https://stackoverflow.com/a/62777816/1477964) on StackOverflow.

AWS Lambda functions can mount [EFS](https://aws.amazon.com/efs/), so it's possible to load libraries or packages that are larger than the 250 MB package deployment size limit.

In [this article](https://aws.amazon.com/blogs/compute/using-amazon-efs-for-aws-lambda-in-your-serverless-applications/), the people at AWS say there is an important difference between using packages in EFS compared with Lambda layers. When you use Lambda layers to include packages, these are downloaded to an immutable code package. Any changes to the underlying layer don't affect existing functions published using that layer.

Since EFS is a dynamic binding, any changes or upgrades to packages are available immediately to the Lambda function when the execution environment is prepared. This means you can output a build process to an EFS mount, and immediately consume any new versions of the build from a Lambda function.

You can configure EFS by following the instructions in the [official documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-filesystem.html) or the AWS blog article I linked above.

Bear in mind the [quotas and limits](https://docs.aws.amazon.com/efs/latest/ug/limits.html) set for EFS.

The EFS property **local mount path** is the location where the file system is mounted on the Lambda function, starting with `/mnt/`. In my case, I set the value to `/mnt/python_modules`.

Once the shared file system is configured, you can create and upload the *zip* file that will contain all the dependencies:

```bash
$ mkdir dependencies
$ pip install boto3 graypy requests pandas scikit-learn -t dependencies
$ cd dependencies
$ zip -r dependencies.zip .
```

> Note: here the dependencies are included in the root of the *zip* file.

Next, I had to make a small adjustment to the configuration of the *build* stage of the CI pipeline:

```yaml
build:
  image: python:3.10-slim
  stage: build
  variables:
    FOLDER: "recommender_lambda"
  script:
    - |-
      cd ${FOLDER}
      apt update && apt install -y zip gcc
      zip -r ../${FOLDER}.zip .
  artifacts:
    paths:
      - ./${FOLDER}.zip
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

I simply deleted the lines related to the installation of dependencies locally from the `requirements.txt` file.

And finally, the Lambda function must know where to find the dependencies, which will be the same value set for the **local mount path** on EFS:

```bash
PYTHONPATH=/mnt/python_modules
```

I applied the changes again and this time the Lambda function successfully ran from start to finish.

Hooray!

## Conclusion

After a lot of going back and forth, I eventually found EFS as a solution to overcome the 250 MB package deployment size limit set for Lambda functions.

Although there are other alternatives to EFS, such as [container images](https://aws.amazon.com/blogs/aws/new-for-aws-lambda-container-image-support/) that allow you to package and deploy Lambda functions of up to **10 GB** in size, the team is happy with the chosen solution. At least for the time being.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
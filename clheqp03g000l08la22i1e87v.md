---
title: "The one with requests to localhost from AWS SAM"
seoDescription: "Learn how to make requests to localhost from an AWS Lambda function using AWS SAM. Find solutions to connection errors when accessing Docker network."
datePublished: Mon May 08 2023 11:08:28 GMT+0000 (Coordinated Universal Time)
cuid: clheqp03g000l08la22i1e87v
slug: the-one-with-requests-to-localhost-from-aws-sam
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/jOqJbvo1P9g/upload/55361318df08f7b1f5a538710b533f1d.jpeg
tags: linux, docker, aws, aws-lambda, aws-sam

---

[Previously](https://blog.davidmp.es/the-one-with-access-denied-to-aws-in-production) I mentioned the recommender system I have been working on lately. A key part of that system is an [AWS Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) that generates recommendations on demand.

While in staging and production environments said Lambda function is invoked every time a job is enqueued in a specific queue in [AWS SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html), in my local machine I can invoke it using [AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html).

Now, this is a story all about how successfully make requests to _localhost_ from a function Lambda using SAM.

Although you will find a few basic commands throughout this post, how SAM works is beyond the scope of this post, so I highly recommend that you take a look at the [official documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli.html) in case you haven't used it before.

First, let me show you the tree structure of the directory where the Lambda function and the SAM application are located:

```bash
$ tree -aL 3
.
├── recommender_lambda
│   ├── .python-version
│   ├── app.py
│   └── requirements.txt
└── sam_recommender_lambda
    ├── events
    │   └── event.json
    ├── recommender_lambda
    │   ├── .python-version
    │   ├── app.py
    │   └── requirements.txt
    ├── samconfig.toml
    └── template.yaml
```

Let's start by preparing the SAM application so that it can be invoked, or even deployed if that's what you want:

```bash
$ cd sam_recommender_lambda
$ sam build
Starting Build use cache
Manifest is not changed for (RecommenderFunction), running incremental build
Building codeuri: /home/david/projects/recommender_lambda/sam_recommender_lambda/recommender_lambda runtime: python3.10 metadata: {} architecture: x86_64 functions: RecommenderFunction
Running PythonPipBuilder:CopySource
Running PythonPipBuilder:CopySource

Build Succeeded

Built Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yaml

Commands you can use next
=========================
[*] Validate SAM template: sam validate
[*] Invoke Function: sam local invoke
[*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
[*] Deploy: sam deploy --guided
```

Next, let's invoke it locally using a custom event:

```bash
$ sam local invoke "RecommenderFunction" -e events/event.json
Invoking app.lambda_handler (python3.10)
Local image is up-to-date
Using local image: public.ecr.aws/lambda/python:3.10-rapid-x86_64.

Mounting /home/david/projects/recommender_lambda/sam_recommender_lambda/.aws-sam/build/RecommenderFunction as /var/task:ro,delegated, inside runtime container
START RequestId: dd6b54d8-1769-4dee-be07-7fff0e59dd03 Version: $LATEST
[ERROR] ConnectionError: HTTPConnectionPool(host='localhost', port=3000): Max retries exceeded with url: /webhooks/recommender (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7fc17f772bf0>: Failed to establish a new connection: [Errno 111] Connection refused'))
END RequestId: dd6b54d8-1769-4dee-be07-7fff0e59dd03
REPORT RequestId: dd6b54d8-1769-4dee-be07-7fff0e59dd03  Init Duration: 0.16 ms  Duration: 12835.90 ms Billed Duration: 12836 ms Memory Size: 1024 MB  Max Memory Used: 1024 MB
```

From all the output received, the relevant messages are the ones that follow:

> ConnectionError: HTTPConnectionPool(host='localhost, port=3000): Max retries exceeded with url: /webhooks/recommender

> Failed to establish a new connection: Connection refused

You can see that there is a connection to `localhost:3000` that can't be established.

That happens because the function is invoked with an event that includes a webhook URL, somehow similar to the following:

```json
{
  "Records": [
    {
      "body": "{\"webhook_url\":\"http://localhost:3000/webhooks/recommender\", ...}"
    }
  ]
}
```

When recommendations have been generated, a POST request is sent to given webhook to notify that they can be imported into our database.

But the main application of our project is running in `localhost:3000`, so why is the connection being refused?

The answer is simple. When accessing `localhost` (or `127.0.0.1`) from within a Lambda function running in SAM, you're actually accessing the Docker network, not your local machine. This valuable information is taken from [this reply in a GitHub issue](https://github.com/aws/aws-sam-cli/issues/260#issuecomment-357508720).

Some possible solutions are mentioned there too but they didn't work for me in **Ubuntu 22.04**, such as `host.docker.internal`.

However, one solution did work for me as expected, mentioned in [this other reply in the same GitHub issue](https://github.com/aws/aws-sam-cli/issues/260#issuecomment-749765208).

It states that Linux users must use the IP address of the **docker0** interface (`172.17.0.1` by default). However, as no more context is provided, I decided to do a little research to know more about it.

[This great article](https://collabnix.com/a-beginners-guide-to-docker-networking#what-is-docker0-in-terms-of-docker-networking) about Docker networking explains that **docker0** is the default [bridge network](https://docs.docker.com/network/bridge/).

To know what other networks are available in Docker, run the following command in the terminal:

```bash
$ docker network ls
NETWORK ID     NAME     DRIVER    SCOPE
b2cb16f9c58f   bridge   bridge    local
34a0a562f949   host     host      local
3c97e640ac60   none     null      local
```

In case you want to know more about those drivers, check out the [official documentation](https://docs.docker.com/network/#network-drivers).

The next step is finding the IP address of that **bridge** driver to check if it matches the IP address suggested in the GitHub issue.

As usual, how to get that desired value is explained in some [great](https://linuxhandbook.com/get-container-ip/) [articles](https://www.freecodecamp.org/news/how-to-get-a-docker-container-ip-address-explained-with-examples/) and in some [StackOverflow answers](https://stackoverflow.com/a/55224655/1477964).

You can get it either directly from Docker:

```bash
$ docker network inspect bridge -f "{{(index .IPAM.Config 0).Gateway}}"
172.17.0.1
```

Or you can get it directly with the [ip command](https://ubuntu.com/blog/if-youre-still-using-ifconfig-youre-living-in-the-past):

```bash
$ ip address show docker0 | grep -Po 'inet \K[\d.]+'
172.17.0.1
```

There it is! You are now sure that in Linux the IP address of the **docker0** interface is `172.17.0.1`, as long as you haven't changed the default configuration.

The only thing left to do is to change the hostname of the webhook URL in the custom event used for invoking the Lambda function:

```json
{
  "Records": [
    {
      "body": "{\"webhook_url\":\"http://172.17.0.1:3000/webhooks/recommender\", ...}"
    }
  ]
}
```

Let's invoke it locally using that custom event again:

```bash
$ sam local invoke "RecommenderFunction" -e events/event.json
Invoking app.lambda_handler (python3.10)
Local image is up-to-date
Using local image: public.ecr.aws/lambda/python:3.10-rapid-x86_64.

Mounting /home/david/projects/recommender_lambda/sam_recommender_lambda/.aws-sam/build/RecommenderFunction as /var/task:ro,delegated, inside runtime container
START RequestId: f9e701df-37e6-4502-92dd-6a0ea993d703 Version: $LATEST
[INFO]  2023-05-01T16:44:38.948Z  f9e701df-37e6-4502-92dd-6a0ea993d703  Webhook request made: 202
END RequestId: f9e701df-37e6-4502-92dd-6a0ea993d703
REPORT RequestId: f9e701df-37e6-4502-92dd-6a0ea993d703  Init Duration: 0.16 ms  Duration: 12929.60 ms Billed Duration: 12930 ms Memory Size: 1024 MB  Max Memory Used: 1024 MB
null%
```

Yeah! The request is now successfully sent from the Lambda function with SAM to the main application of the project. The server responded with a [202 status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/202).

> Webhook request made: 202

I hope this story has been helpful to you. Having some basic knowledge of both AWS SAM and Docker networks have proved to be pretty important if you want to easily test AWS Lambdas locally.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
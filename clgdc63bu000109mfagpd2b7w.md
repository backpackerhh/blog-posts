---
title: "The one with access denied to AWS in production"
seoDescription: "Discover how a misconfiguration in stored AWS credentials in Rails for the production environment can lead to unexpected errors and impact UX."
datePublished: Wed Apr 12 2023 06:54:23 GMT+0000 (Coordinated Universal Time)
cuid: clgdc63bu000109mfagpd2b7w
slug: the-one-with-access-denied-to-aws-in-production
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Q1p7bh3SHj8/upload/4749b27b82a63a12d17c9e211816b7b1.jpeg
tags: aws, ruby, web-development, ruby-on-rails, yaml

---

In the project I'm currently working on, we use Rails for our main application and the AWS SDK for Ruby to seamlessly integrate with their services, which require credentials in the form of `access_key_id` and `secret_access_key`.

In Rails is recommended to store those credentials in [an encrypted YAML file](https://guides.rubyonrails.org/v6.0/security.html#custom-credentials), that can be stored in version control, as long as the *master key* is kept safe, and only can be edited by running the following command in the terminal:

```bash
$ EDITOR="vim" bundle exec rails credentials:edit
```

<mark>(you can replace </mark> **<mark>vim</mark>** <mark>with your editor of choice)</mark>

Until recently, we only had a few AWS credentials stored for accessing S3. As usually happens in long-run projects, each of those credentials was added at different times and likely by different people:

```yaml
defaults: &defaults
  # other keys omitted
  s3:
    access_key_id: '<access_key_id>'
    secret_access_key: '<secret_access_key>'
  # other keys omitted
  s3_ivs:
    access_key_id: '<access_key_id>'
    secret_access_key: '<secret_access_key>'

development:
  <<: *defaults

test:
  <<: *defaults

staging:
  <<: *defaults

production:
  <<: *defaults
```

As you can see, we have specific credentials per environment. It's worth mentioning that, at that point, all configurations related to S3 used the same credentials in every environment.

We avoid lots of duplication taking advantage of [YAML aliases, anchors and merge keys](https://www.educative.io/blog/advanced-yaml-syntax-cheatsheet).

Of course, the day came when we needed new S3 and SQS credentials for a new recommender system.

But before adding new credentials, I took a moment to reorganize the existing ones:

```yaml
defaults: &defaults
  # other keys omitted
  aws:
    s3:
      private:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
      ivs:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'

development:
  <<: *defaults

test:
  <<: *defaults

staging:
  <<: *defaults

production:
  <<: *defaults
```

As you can see, **high cohesion** is not only desirable for our code.

Of course, all references to S3 credentials in the code were properly updated.

It's really simple adding new credentials for other S3 configurations (in the previous example, `private` and `ivs` are configurations) and other AWS services:

```yaml
defaults: &defaults
  # other keys omitted
  aws:
    s3:
      private:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
      ivs:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
    sqs:
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'

development:
  <<: *defaults

test:
  <<: *defaults

staging:
  <<: *defaults

production:
  <<: *defaults
  aws:
    s3:
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
    sqs:
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
```

Unlike before, here we have specific credentials for the production environment. And that is a crucial detail in this whole story.

Once I finished developing the new functionality, the next steps were:

* My teammates reviewed and approved my pull request.
    
* Our whole suite of tests successfully passed in our CI pipelines.
    
* The new functionality was approved and marked as ready for production after successfully passing the QA process in staging.
    

After that, I started a production deployment with those changes, feeling completely confident that everything was in order.

However, as you might have guessed from the title of this article, something went wrong.

As soon as those changes became available in our production clusters, we started noticing errors related to S3 in our monitoring system:

> Notice: unable to sign request without credentials set

In some cases, those errors were unnoticed by our end users, as some external resources were unreachable, but in other cases our API was returning a 500 response.

Immediately I jumped into a call with the [BOFH](https://en.wikipedia.org/wiki/Bastard_Operator_From_Hell) and we decided to roll back the last deployment until we could figure out what was causing all those errors.

For 15 minutes about 1.6K requests failed. It didn't have a huge impact in our platform but still was a bad UX for people that tried to access the contents for which they have paid, both through our mobile app and the website.

So we started to investigate the issue and it was pretty clear from the beginning that the cause was related to the specific configuration for production environment, having in mind that we didn't receive a single error for the rest of environments were the new functionality was checked.

Maybe you spotted the problem at first glance, but it took us a while to figure it out. Just in case, let me refresh your memory about the configuration for that environment:

```yaml
production:
  <<: *defaults
  aws:
    s3:
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
    sqs:
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
```

I executed following snippet in the Rails console of my development environment:

```ruby
> pp Rails.application.credentials.production.fetch(:aws); nil

{:s3=>
  {:recommendations=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"}},
 :sqs=>
  {:recommendations=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"}}}
=> nil
```

That's it! AWS credentials were being overwritten for production.

The solution was pretty easy once the problem was detected:

```yaml
defaults: &defaults
  # other keys omitted
  aws:
    s3: &default_s3_credentials
      private:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
      ivs:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
    sqs: &default_sqs_credentials
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'

development:
  <<: *defaults

test:
  <<: *defaults

staging:
  <<: *defaults

production:
  <<: *defaults
  aws:
    s3:
      <<: *default_s3_credentials
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
    sqs:
      <<: *default_sqs_credentials
      recommendations:
        access_key_id: '<access_key_id>'
        secret_access_key: '<secret_access_key>'
```

I simply had to add a couple of new aliases and merge keys for S3 and SQS credentials, so they could be used whenever required.

Then I executed the same snippet in the Rails console:

```ruby
> pp Rails.application.credentials.production.fetch(:aws); nil

{:s3=>
  {:private=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"},
   :ivs=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"},
   :recommendations=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"}},
 :sqs=>
  {:recommendations=>
    {:access_key_id=>"<access_key_id>",
     :secret_access_key=>"<secret_access_key>"}}}
=> nil
```

Certainly, it's one of those mistakes that could have been easily avoided. Let's take a closer look at the contributing factors and see how it could have been prevented.

First, there was my obvious oversight when defining the new configuration, despite using YAML regularly. I added the new values without double-checking that the existing ones were still correctly defined.

At least a manual check executing the same snippet than we already executed above would have been enough.

Second, in the pull request we can see the files that have changed, but in the case of the credentials file we can't see what has changed because its contents are encrypted. Being that way, my teammates couldn't see that what I had changed was wrong.

Third, we do not have [e2e tests](https://martinfowler.com/articles/practical-test-pyramid.html#End-to-endTests) properly checking the integration with AWS, that could be executed during the CI pipeline or right before starting a production deployment. Surely we have room for improvement there to detect that kind of problems.

And last but not least, a well-known tip about something I'll have to remember the hard way:

> **Do not refactor nor apply important changes at the same time you are adding new functionality.**

Some benefits of doing so would be that there are fewer chances of breaking something and that our teammates have to review fewer changes.

I hope this story has been helpful to you and will serve you as a reminder to avoid making the same mistake.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
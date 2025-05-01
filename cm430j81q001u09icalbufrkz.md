---
title: "The one about how things work in an American fintech"
seoDescription: "Explore the intricacies of working in an American fintech company, including onboarding, team dynamics, tech stack, and development processes"
datePublished: Fri Nov 29 2024 17:23:54 GMT+0000 (Coordinated Universal Time)
cuid: cm430j81q001u09icalbufrkz
slug: the-one-about-how-things-work-in-an-american-fintech
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739389093975/346f6441-52ea-41d0-9be0-5ae4c24563cd.png
tags: software-development, agile, software-engineering, fintech, fintech-software-development

---

This is my first time working for any American company. In many ways things work differently here than with other companies I have previously worked for, so I will tell you here about the ins and outs of this American fintech I am working for as a contractor.

## Before the start

Before I started I had to go through a **background screening** process with their provider, [Orange Tree](https://www.orangetreescreening.com/). That process took several weeks but finally they gave the green light.

A couple of days before starting, I received an email in my personal email account with details for configuring the laptop received a few days before. Of course, it was a [macOS laptop](https://blog.davidmp.es/the-one-about-working-with-macos-being-a-linux-user). If you are a contractor, you do not get a new laptop.

## Onboarding

The first day was very simple. I joined a call with the IT people along with other people starting at the company that very same day as well so they can install the **software that is able to remotely control our computers** if needed. According to the IT people, you need to explicitly give permissions before they are able to do anything in the computer.

After that, I met with the engineering manager to tell me about how the project works, how it is organized, what are the responsibilities of the team and how the onboarding would be, among other stuff.

Then I was assigned a board with a series of tasks that I had to complete during the onboarding, like meeting people from different teams, roles and hierarchy levels in the company; reading documentation, completing the first task, doing a code review or deploying to production.

That first day I was already present at the daily meeting so I could meet my new teammates.

The next day I joined my **onboarding buddy** to set up the various applications we work on and to tell me what the day-to-day work is like. We spent a big part of my first few weeks doing **pair programming**.

The onboarding was completed in just over a month.

## The company

The headquarters of the company are in Boston.

The company has around 1,300 employees around the world, although most of the engineering team is based in Spain.

They recently announced that around 3% of the workforce would be affected by lay offs, with the engineering team not being affected at all right now.

Communication within the company is always in English, unless everyone present at a meeting speaks Spanish (or any other language).

At the company level there are some recurring meetings:

- **All Company**: a 1-hour to 1.5-hour meeting where the CEO and other senior managers of the company tell how the business is doing and upcoming objectives. It is held once a month.
- **All Hands Engineering**: very similar to *All Company* meeting, but only for members of the engineering area. Here they mainly talk about changes already implemented in the different teams and upcoming projects or changes that we are about to implement. It is held once a month.

Everybody is required to log however many hours they are required to work each week, included contractors like me.

## The project

In the engineering area there are multiple teams, each with governance over multiple applications.

However, this is not a strict ownership, because any developer can add or modify code in any of the repositories they have access to, as long as the owner team is tagged in the MR so that they can review it as well.

Such a review is usually optional, unless any of the changed files are included in the [CODEOWNERS file](https://docs.gitlab.com/ee/user/project/codeowners/).

Unfortunately, there is **no standard way to do anything in the engineering team**. In the documentation I had to read as part of the onboarding I found a style guide with details on how to write code, but in practice I can confirm that that style guide is not being followed by any of the existing teams.

That means that it is very likely that when we change code owned by other team we will have to do things differently than we would do it in our own code. My house, my rules!

In a daily basis, we work with money in one way or another, so **security** is critical. The company must comply with certain regulations, standards and laws, including anti-money laundering (AML), fraud prevention, and know your customer (KYC) procedures.

Periodically we must complete courses related to these topics to stay up to date.

Therefore, access to production applications, databases or console is totally restricted for developers.

**Observability** in cases like this is essential, so we make extensive use of logs with tools like [Honeycomb](https://www.honeycomb.io/) and [Sumo Logic](https://www.sumologic.com/), generating those logs through an internal gem that provides an [OpenTelemetry](https://opentelemetry.io/) wrapper.

The **error tracking** of all applications is done with [Sentry](https://sentry.io/welcome/).

Sometimes, due to an error or simply because we need to apply a change or enable a feature flag in production, we have to open a support ticket on [Jira](https://www.atlassian.com/software/jira) so that the support team can take care of it, usually by executing a Rake task.

Access to all applications is handled by [Okta](https://www.okta.com/), usually allowing to authenticate with biometrics (e.g. fingerprint). For certain applications, 2-factor authentication ([2FA](https://2fas.com/)) is required, especially for those that are only accessible through a [VPN](https://en.wikipedia.org/wiki/Virtual_private_network).

At the code level, security is also taken into account, since the CI pipeline executes a step in each repository that checks for vulnerabilities either in our own code or in third-party code. At the moment it only shows warnings with all the vulnerabilities found, but soon it will cause our pipelines to fail.

In addition to the applications already mentioned, we work in a daily basis with the following applications:

- [Google Suite](https://workspace.google.com/) (GMail, Drive, Meet, Presentations, Spreadsheets, Docs).
- [Confluence](https://www.atlassian.com/software/confluence) → Documentation.
- [Jira](https://www.atlassian.com/software/jira) → Project tracking and support
- [Slack](https://slack.com/) → Instant messaging.
- [GitLab](https://about.gitlab.com/) → Coding and CI/CD.
- [Postman](https://www.postman.com/) → API development.

The [SRE](https://aws.amazon.com/what-is/sre) team has created a series of internal tools that integrate into our daily work in a very simple way:

- A tool similar to [Kubernetes](https://kubernetes.io/) that allows us to automate deployments, scaling and manage applications with containers.

  They have everything perfectly organized to generate CI/CD pipelines with [IaC](https://aws.amazon.com/what-is/iac/), in each of the environments we have available to test our applications, before promoting the code to production.

- A CLI tool similar to [AWS CLI](https://aws.amazon.com/cli/) that allows to execute commands in those pre-production environments, simply by specifying the environment, the application and the command to be executed.
- A tool that allows to manage the creation, modification and deletion of records of any of the entities of our domain in pre-production environments. It works as a test factory, generating any type of dependency that may be necessary along the way. This tool replaces a Slack bot that has been used until now and that will be deprecated soon.

At the engineering team level there are some recurring meetings:

- **Dev Learning**: a 15-minute to 1-hour meeting where one of our colleagues talks about a technical topic. It is held every Friday.
- **Game Days**: a 1.5-hour meeting where first every team shares their [post-mortems](https://en.wikipedia.org/wiki/Postmortem_documentation) since the previous meeting, and later we are presented with a series of exercises prepared by other members of the engineering team for us to solve, organized in groups and with time constraints. The challenges usually must be solved without using the tools mentioned above. It is held once a month.

We have dozens of internal gems hosted on [Buildkite Package Registries](https://www.buildkite.com/platform/package-registries/) (formerly [Packagecloud](https://packagecloud.io/)). Some examples of those gems, in addition to the OpenTelemetry wrapper mentioned above, allow us to integrate with banks we work with, such as Citibank or Bank of America. Among other things, it allows us to test new changes directly against their sandbox before releasing a new version of the gem.

## My team

It consists of an engineering manager (EM), a project manager (PM), a QA engineer and 4 software engineers, including myself. Everybody is based in Spain, except for one teammate who lives in Canada.

We have a flexible schedule. The only requirements are getting the job done and attending team meetings.

The team belongs to the **Transactional** vertical and is in charge of managing funds collection and payouts to many countries and in many currencies.

It is pretty common that we add new **payment corridors**, which is simply adding support for payments to specific countries in one or more currencies if we do not already have it in place.

Probably the **automatic matching** of received transactions and expected payments, either debits or credits, is the most important function we have, thus relieving the manual work of the operators who deal with all those transactions.

Some of the clients we work with are Citibank, Bank of America, J.P. Morgan, Deutsche Bank or Cross River Bank.

Most of our applications are pure backend applications, although we also have one [SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA) developed with **React**. At the moment, only one of my teammates works with that application in case adding a new feature or fixing a bug is needed.

Regarding our backend applications, they are **microservices** developed in **Ruby** with [Sinatra](https://sinatrarb.com/). As far as I know, most people in this company are not fans of [Rails](https://rubyonrails.org/).

The gems we develop are of course developed in pure Ruby as well.

We have an event-driven architecture ([EDA](https://aws.amazon.com/what-is/eda/)) in place, where the communication between microservices is usually asynchronous with events published to and consumed from [RabbitMQ](https://www.rabbitmq.com/).

Depending on the application, we also expose and consume data from [REST APIs](https://blog.postman.com/rest-api-examples/) endpoints whose response is formatted in [JSON](https://www.json.org/json-en.html).

Each application has its own technology stack, but it is common to work with [Docker](https://www.docker.com/), [docker compose](https://docs.docker.com/compose/), [AWS](https://aws.amazon.com/), [Dry gems](https://dry-rb.org/), [MySQL](https://www.mysql.com/), [Elasticsearch](https://www.elastic.co/elasticsearch) and [Redis](https://redis.io/), among others.

As I mentioned previously at the company level, the team did not follow a style guide for writing code when I joined. I am currently in the process of [adding RuboCop as a linter](https://blog.davidmp.es/the-one-about-linting-in-a-legacy-ruby-project) to all applications of our governance.

In the same way, the way applications are tested is quite diverse, so I am trying to define a style guide for that as well. In the newest application we have developed I was already able to implement a new testing approach successfully, but I am aware that it will not be that easy to do it for the rest, among other things, because I find some resistance to change from the team.

Either way, what is common is that we use [RSpec](https://rspec.info/) as a testing framework and [FactoryBot](https://github.com/thoughtbot/factory_bot) as a tool to create database records in our test suite. I also introduced [rspec-swag](https://github.com/graceful-potato/rspec-swag), a [rswag](https://github.com/rswag/rswag) fork that can be used with any Rack-compatible framework, to test our APIs and automatically generate the documentation of our endpoints with [Swagger](https://swagger.io/tools/swagger-editor/), in compliance with the [OpenAPI](https://swagger.io/specification/) specification.

We follow an [agile methodology](https://www.atlassian.com/agile), more similar to [Kanban](https://www.atlassian.com/agile/kanban) than to [Scrum](https://www.atlassian.com/agile/scrum), to develop our applications.

In the **sprint board** we have different rows, which represent each of the team's priorities. These priorities vary in each sprint, which lasts 2 weeks.

Regarding the development cycle, our sprint board has the following columns:

- **To Do**: A task is created and is ready to be refined.
- **Ready for Dev** (Selected for Development): A task is already refined and has been included in the current sprint.
- **In Dev**: A developer is currently working on a task or the MR is already created and they are checking that it works as expected in one of the pre-production environments.
- **Ready for QA**: The developer finished working on a task and the CI pipeline must pass before moving on. The developer checked in one of the pre-production environments that everything works as expected and documented all their tests, with details about the commands executed or screenshots if necessary, so the QA engineer can validate them and add their own tests. At this point having the MR approved is not a requirement.
- **In QA Review**: The QA engineer is validating the tests made by the developer and adding their owns tests.
- **QA Complete**: The QA engineer successfully finished testing the task and it is now ready to be deployed. At this point the MR must be approved by at least 2 repository owners before being merged.
- **Deployed**: The MR was merged and the code deployed to production. The developer could wait until they confirm no errors are raised related to the changes deployed or a Rake task finishes its execution.
- **Closed**: There is nothing left to do regarding the task.

And the workflow is as follows:

![American fintech development lifecycle workflow](https://github.com/user-attachments/assets/95eb636e-e23e-41fd-886b-fc6b6fd9b112)

Each developer is responsible for moving their tasks around the sprint board, except when the task is in one of the QA-related columns.

At the team level there are some recurring meetings:

- **Daily**: a 15-minute meeting where each member of the team explains what they have been working on since the previous day and what they will continue working on. In case anything is blocking us, we mention it here as well. Some members of the support team usually join us a couple of days each week. At the end of their turn, each person nominates the next person who should speak.
- **Refinement**: a 30-minute to 1-hour meeting from a product point of view, where any doubts the team may have about certain tasks are solved. The "definition of done" is defined here, but without going into technical details. Tasks are rarely estimated, except when we want to know from the beginning if all of us think the task should be divided into multiple tasks or not. We use the Fibonacci sequence, between 3 and 13 points, where 3 points represents 1 day of work approximately. It is held once in each sprint.
- **Split**: a 1-hour meeting from a technical point of view, where we solve any doubts the team may have about how to implement certain changes and we write it down in the task itself. If necessary, we create a [spike](https://agilemania.com/what-is-a-spike-in-agile) and discuss it again in a later meeting. This is held once in each sprint.
- **Code Review**: a 1-hour meeting where we review the concerns board, where each team member can add their concerns to solve technical debt. In each task we can define what we are concerned about and the solution we propose to solve it. This is held once in each sprint.
- **Sprint Planning**: a 30-minute meeting where the PM checks the unfinished tasks from the previous sprint and prioritizes the tasks to be included in the next sprint. The previous team's velocity is not taken into account, but the team's opinion is taken into account when it comes to include a task in the sprint or not. It is held on the first Monday of each sprint.
- **Retrospective**: a 1.5-hour meeting where each team member can add a list of things they think went well and what things we can improve since the previous retrospective, although we usually only talk about what did not go so well. Then everyone chooses the 3 topics that interest them the most and we spend the rest of the session trying to find possible solutions to the 3 most upvoted topics. A person is assigned to follow up on each of those topics. The meeting is lead by a facilitator from another team. Normally this is held once a month, although it sometimes takes longer.
- **Game Days**: a 30-minute meeting where the whole team spends time together, bonding with each other. We usually play online Pictionary or games like that. It is held on the second Monday of each sprint.
- **1 to 1**: a 30-minute meeting with each permanent full-time worker or a 15-minute meeting with each contractor with the EM to talk and periodically check on how things are going on both sides.
- **Coffee Chat**: a 15-minute meeting before the daily meeting on Fridays where we usually talk about our plans for the weekend.

As you can see, we do not have *sprint review* or *sprint retrospective*.

Finally, on rotating basis, each week a developer of the team is the **minion**.

The highest priority of that person during that week is to help resolve support tickets that have been assigned to the team. If the solution is simple, they can create a task and implement the changes directly. If the solution is a bit more complex, they can create a new task and share it with the rest of the team to give more visibility. If it is urgent, that task is added directly to the sprint board.

In addition, the minion shares their screen with the sprint board on the daily meeting and takes notes in technical meetings.

The minion also has a dedicated row on the sprint board with tasks related to support tickets or technical debt tasks that are created from a concern.

## Conclusion

There was a lot more to tell than I had initially thought. In many ways I am sure this American fintech works very similarly to any other company you know or have worked for. Still, I think it is interesting to share how they work and how they are organized, but keeping the company name anonymous, for obvious reasons.

I hope to talk about my experience working with them soon. So stay tuned if you are interested.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
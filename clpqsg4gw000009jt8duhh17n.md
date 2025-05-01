---
title: "The one where you automatically add your latest posts to your GitHub profile"
seoDescription: "Discover how to keep your GitHub profile automatically up to date with your latest blog posts and your recent activity"
datePublished: Mon Dec 04 2023 10:48:26 GMT+0000 (Coordinated Universal Time)
cuid: clpqsg4gw000009jt8duhh17n
slug: the-one-where-you-automatically-add-your-latest-posts-to-your-github-profile
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1699528730003/9fa4084e-9934-4eab-b422-d9abb7a11d4b.png
tags: github, apis, graphql, hashnode, github-actions-1

---

A few weeks ago I was taking a look at the list of posts featured on Hashnode when suddenly I saw one that immediately caught my eye:

[![Featured post about using Hashnode's Public API](https://user-images.githubusercontent.com/685978/280487202-32fe76a4-c4f6-4dc7-a7ad-3425f1c09213.png)](https://engineering.hashnode.com/what-can-i-do-with-hashnodes-public-api)

In that post Sandro, member of the Hashnode Engineering team, lists up to 6 examples of what you can build taking advantage of their public GraphQL API.

I'm gonna talk here about the last example, because I really liked the idea of automatically adding my latest blog posts to my GitHub profile.

[Jannik Wempe's profile](https://github.com/JannikWempe) is shown as an example of what we can achieve with the provided API and [GitHub Actions](https://docs.github.com/en/actions).

No code is included in the post regarding this functionality, so I decided to investigate a bit further to implement it in my own profile.

## GitHub profile

I started by reading the [linked post](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme) by the people from GitHub, about how to manage the content of your profile.

Basically it says that you can include whatever information you want, as long as you meet prerequisites needed for it to be displayed:

* You've created a repository with a name that matches your GitHub username.
* The repository is public.
* The repository contains a file named `README.md` in its root.
* The `README.md` file contains any content.

Follow the instructions included in that post to properly create the required repository and once it's created you can see a banner in the sidebar with following message:

[![backpackerhh repository message](https://github-production-user-asset-6210df.s3.amazonaws.com/685978/280497784-10e72fa6-80b5-4d62-abea-98886b4ea129.png)](https://github.com/backpackerhh/backpackerhh)

Jannik uses a few **GitHub Actions** to include his latest blog posts and his recent activity in [his profile](https://github.com/JannikWempe/JannikWempe). So did I.

I'll tell you the steps I followed to make all of this work.

### Recent activity

After cloning your new repository, add `.github/workflows/update-recept-activity.yml` file with following content:

```yaml
name: Update Recent Activity

on:
  schedule:
    # 8:00 and 20:00 every day
    - cron: '0 8,20 * * *'
  workflow_dispatch:

jobs:
  generate-feed:
    runs-on: ubuntu-latest
    name: Update Activity

    steps:
      - uses: actions/checkout@v4

      # Pin the version to the latest stable version.
      # If you want to live on edge, use 'master' branch.
      - uses: recaptime-dev/github-activity-readme@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

A lot of stuff is going on here, so let me explain it:

* The workflow is [scheduled](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule) to run every day at 8:00 and 20:00.
* The workflow is enabled to be [triggered manually](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch).
* The workflow is able to access to the repository using [Checkout](https://github.com/marketplace/actions/checkout) action.
* The workflow updates the `README.md` file with the recent GitHub activity using [GitHub Activity in Readme](https://github.com/marketplace/actions/github-readme-activity-section-updater) action.

At the start of each workflow job, GitHub automatically creates a unique `GITHUB_TOKEN` secret that you can use to authenticate on behalf of GitHub Actions. Check more details [here](https://docs.github.com/en/actions/security-guides/automatic-token-authentication).

Next you can see some screenshots for this workflow:

#### Manual dispatch enabled in the UI

![backpackerhh repository recent activity workflow manual dispatch enabled](https://user-images.githubusercontent.com/685978/280675346-52c32363-0feb-4a3f-923a-f3a151a92060.png)

#### Job manually triggered

![backpackerhh repository workflow manually triggered](https://user-images.githubusercontent.com/685978/280675349-43778779-9ad3-43a1-8957-b00960f06eec.png)

#### Job triggered via schedule

![backpackerhh repository workflow triggered via schedule](https://user-images.githubusercontent.com/685978/280675347-27628749-e694-4397-82f1-68006a1a17c2.png)

#### Job successfully executed

![backpackerhh repository workflow successful execution](https://user-images.githubusercontent.com/685978/280675354-40c34536-3565-4563-9353-c62dcf8eb6a5.png)

### Latest blog posts

Add `.github/workflows/update-latest-blog-posts.yml` file with following content:

```yaml
name: Update Latest Blog Posts

on:
  workflow_dispatch:
  # for trigger via webhooks
  repository_dispatch:
    types: [trigger]

jobs:
  update-posts:
    runs-on: ubuntu-latest
    name: Update Posts

    steps:
      - uses: actions/checkout@v4

      - uses: backpackerhh/github-latest-hashnode-posts@main
        with:
          HASHNODE_PUBLICATION_ID: ${{ secrets.HASHNODE_PUBLICATION_ID }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Once again a lot of stuff is going on here, so let me explain it:

* The workflow is enabled to be [triggered manually](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch).
* The workflow is enabled to be [triggered with a webhook event](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#repository_dispatch), specifiying `trigger` as event type.
* The workflow is able to access to the repository using [Checkout](https://github.com/marketplace/actions/checkout) action.
* The workflow updates the `README.md` file with my latest Hashnode blog posts using [backpackerhh/github-latest-hashnode-posts](https://github.com/marketplace/actions/github-readme-activity-section-updater) action. I'll talk more about it later.

Again, `GITHUB_TOKEN` is used to authenticate on behalf of GitHub Actions. A [custom repository secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) containing the Hashnode publication ID (`HASHNODE_PUBLICATION_ID`) is used as well.

That particular value doesn't have to be secret, so you could directly put it in the YAML file if it suits you better.

Either way, you can add a new repository secret in following URL:

`https://github.com/<username>/<username>/settings/secrets/actions/new`

Next you can see some screenshots for this workflow:

#### Manual dispatch enabled in the UI

![backpackerhh repository latest blog posts workflow manual dispatch enabled](https://user-images.githubusercontent.com/685978/280675340-22a3e7ed-ea70-40ee-b886-cb6b61d8896d.png)

#### Job manually triggered

![backpackerhh repository job manually triggered](https://user-images.githubusercontent.com/685978/280696908-319ae44c-dc14-454c-bec7-4f14618c5aac.png)

#### Job triggered via repository dispatch

![backpackerhh repository job triggered via repository dispatch](https://user-images.githubusercontent.com/685978/280696916-5ac1d018-70ee-4feb-a903-0ad832aa9105.png)

#### Job successfully executed

![backpackerhh repository job successfully executed](https://user-images.githubusercontent.com/685978/280696918-4de3d79a-29ad-4c1b-8a68-fb65521dabc1.png)

### Add content dynamically

For all of the above to work, following content must be initially added to the `README.md` file:

```md
## Latest Blog Posts

<!-- HASHNODE_POSTS:START -->
<!-- HASHNODE_POSTS:END -->

## Recent GitHub Activity

<!--START_SECTION:activity-->
<!--END_SECTION:activity-->
```

Those pairs of comments are used as placeholders so the GitHub actions can dynamically include the content between them.

Notice that a commit is created every time the content changes.

### Settings

You can configure actions permissions in following URL:

`https://github.com/<username>/<username>/settings/actions`

You have to enable "Allow all actions and reusable workflows" permission (default) and grant "Read and write permissions" for workflows.

## Custom GitHub action

This [new repository](https://github.com/backpackerhh/github-latest-hashnode-posts) is simply a fork from the [custom action](https://github.com/JannikWempe/github-latest-hashnode-posts) created by Jannik.

I preferred to create a fork instead of using his action because I wanted to customize some things.

Next, I'll talk about the more relevant files present in the repository.

### package.json

In case you don't know anything about this file, check the [official documentation](https://docs.npmjs.com/cli/configuring-npm/package-json) for a detailed explanation about every option defined in it.

In summary, this file allows a package to make it easy for others to manage and install. Besides, you can list the packages your project depends on and the scripts that are available to be executed.

Force **npm** as package manager:

```bash
$ npm run preinstall

> github-latest-hashnode-posts@0.0.1 preinstall
> npx only-allow npm

```

Compile the action code:

```bash
$ npm run prepare

> github-latest-hashnode-posts@0.0.1 prepare
> ncc build src/index.js -o dist --source-map --license licenses.txt

ncc: Version 0.36.1
ncc: Compiling file index.js into CJS
   9kB  dist/licenses.txt
  40kB  dist/sourcemap-register.js
 936kB  dist/index.js
1092kB  dist/index.js.map
1092kB  dist/index.js.map
2077kB  [2108ms] - ncc 0.36.1
```

This step is really important, because the content within `dist` directory is the actual code that will run the GitHub action.

### action.yml

Here is where the configuration and the metadata of the GitHub action is actually defined. For more details about all available options, check the [official documentation](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions).

For this [specific action](https://github.com/backpackerhh/github-latest-hashnode-posts/blob/main/action.yml), I'd like to highlight a couple of them:

* `inputs`: A list of input parameters that the action accepts, such as `GITHUB_TOKEN`, `HASHNODE_PUBLICATION_ID` or `MAX_POSTS`.
* `runs`: Describes how the action should be executed, including the runtime environment (`node20`) and the entry point (`dist/index.js`).

### src/index.js

In [this file](https://github.com/backpackerhh/github-latest-hashnode-posts/blob/main/src/index.js) is where all the action happens (pun intended).

A request to the **Hashnode GraphQL API** is made with the inputs previously defined in `action.yml` file:

```gql
query LatestPosts($id: ObjectId!, $first: Int!) {
  publication(id: $id) {
    id
    posts(first: $first) {
      edges {
        node {
          id
          title
          brief
          publishedAt
          url
          coverImage {
            url
          }
        }
      }
    }
  }
}
```

Where `id` is the Hashnode publication ID and `first` is the number of posts that should be retrieved.

Hashnode provides a [playground](https://gql.hashnode.com/) in case you need to customize your query. For instance, regarding what Jannik had, I added the `url` of the post to the query so in my profile each post includes a link in the title and the cover.

![Hashnode API Playground](https://user-images.githubusercontent.com/685978/280497786-535a702a-e174-45a7-be5a-de4557a85a89.png)

A function gives then expected format to all the posts retrieved from the API and replaces the content of the `README.md` file with them.

As a reminder, if everything goes well a commit is created with those changes.

## The missing piece

There is a big difference regarding how the two workflows mentioned above run to update my profile. While I'm ok running one workflow twice a day to check any recent activity, the other one should only run when an event related to a blog post is triggered, either because it was published, updated or deleted.

Knowing that I'd need to automate that process somehow, the first thing that came to my mind was the webhooks provided by Hashnode:

![Hashnode form to create a new webhook](https://user-images.githubusercontent.com/685978/280991571-8e556be9-354e-496d-89f0-38ba581b7880.png)

However, I'd need to provide a URL to some kind of [serverless function](https://www.splunk.com/en_us/blog/learn/serverless-functions.html) that would validate the secret defined in Hashnode (optional, but recommended) and trigger a request to the desired repository in GitHub.

There are a lot of services out there that would allow to do it very easily, such as [AWS Lambda](https://aws.amazon.com/lambda/), [Vercel](https://vercel.com/docs/functions/serverless-functions) or [Netlify](https://www.netlify.com/platform/core/functions/), to name a few, but it feels kind of overkill to set up something like that for what I want to achieve.

> Bear in mind that I didn't check that approach, so I might be overlooking something else.

For that reason, I thought that it could be easier to just trigger an action from another repository.

Hashnode provides an integration with GitHub, so all my posts are backed up automatically to [this repository](https://github.com/backpackerhh/blog-posts):

![GitHub integration in Hashnode](https://user-images.githubusercontent.com/685978/280991581-8c5649a3-b7b7-473d-a7e1-893a119b45aa.png)

After some research, I found [this article](https://www.amaysim.technology/blog/using-github-actions-to-trigger-actions-across-repos) which proved to be an invaluable help.

Let's see how it's done.

Add `.github/workflows/dispatcher.yml` file to the repository where blog posts are backed up with following content:

```yaml
name: Dispatcher

on:
  push:
    branches: [main]

jobs:
  dispatch_event:
    name: Dispatch event
    runs-on: ubuntu-latest
    timeout-minutes: 2
    steps:
      - name: Dispatch
        run: |
            curl -L \
              -X POST \
              -H "Accept: application/vnd.github+json" \
              -H "Authorization: Bearer ${{ secrets.DISPATCH_TOKEN }}" \
              https://api.github.com/repos/backpackerhh/backpackerhh/dispatches \
              -d '{"event_type":"trigger"}'
```

Yet once again a lot of stuff is going on here, so let me explain it:

* The workflow is enabled to be [triggered when a push to main branch occurs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#push).
* The workflow makes a POST request to the GitHub endpoint to [create a repository dispatch event](https://docs.github.com/en/rest/repos/repos#create-a-repository-dispatch-event):
  * The value of `event_type` is the one defined in the [GitHub action](https://github.com/backpackerhh/backpackerhh/blob/main/.github/workflows/update-latest-blog-posts.yml#L7) of the target repository.
  * The `client_payload` parameter is available for any extra information that your workflow might need.

Again, a [custom repository secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions) is required to be used as authorization token (`DISPATCH_TOKEN`).

You can add a new repository secret in following URL:

`https://github.com/<username>/<blog_posts_repository>/settings/secrets/actions/new`

The value of that secret must be the value of a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) (PAT) associated to your account.

If you prefer to create a classic PAT follow next steps:

1. Go to https://github.com/settings/tokens/new
2. Fill in following fields:
   * `Note` -> Blog posts
   * `Expiration` -> No expiration (**at your own peril**)
   * `Select scopes` -> workflow (will mark _repo_ automatically)
3. Click on `Generate token` button

If you prefer to create a fine-grained PAT follow next steps:

1. Go to https://github.com/settings/personal-access-tokens/new
2. Fill in following fields:
   * `Token name` -> Blog posts
   * `Expiration` -> 90 days (custom is around 100 days)
   * `Repository access` -> Only select repositories -> select `<username>/<username>`
   * `Permissions` -> Repository permissions
     * Contents -> Access: Read and write
     * Metadata -> Access: Read access (automatically selected)
3. Click on `Generate token` button

Although is less secure, for this particular use case I've created a classic PAT without expiration. I prefer to simply generate a token and just forget about it.

Next you can see some screenshots for this workflow:

#### Job triggered via push to main branch

![blog posts repository job triggered via push to main branch](https://user-images.githubusercontent.com/685978/281049523-315f89aa-93fe-403d-9d49-62a76eccf6f4.png)

#### Job successfully executed

![blog posts repository job successfully executed](https://user-images.githubusercontent.com/685978/281049530-8a846f07-5bc4-421b-808f-0f37c36306ef.png)

It's worth mentioning that you could use an already available GitHub action instead, such as [repository-dispatch](https://github.com/marketplace/actions/repository-dispatch).

## Workflow

Next, you can see how the process of updating my GitHub profile with the latest blog posts works:

![Workflow diagram](https://user-images.githubusercontent.com/685978/281372117-02f421ce-8290-475c-af55-bd05733133b7.png)

And the result of running that workflow would be something as follows:

![GitHub profile after workflow runs](https://user-images.githubusercontent.com/685978/281372176-6ecc8692-777a-4e62-9bad-157994e56dda.png)

## Conclusion

There was a lot of trial and error to get everything to work correctly, especially due to permissions issues with workflows and PATs, but it's not a really complex process.

To be honest, I had never thought about having a GitHub profile with something more than pinned repositories, but seeing the potential of this kind of workflows, from now on I will keep thinking about how to improve it even more.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
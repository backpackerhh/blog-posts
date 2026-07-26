---
title: "The one where backups keep your latest posts on your GitHub profile up to date"
seoDescription: "Learn to automate your GitHub profile README with your latest blog posts using GitHub Actions and local Markdown backups. No external APIs required!"
datePublished: 2026-07-26T09:03:30.720Z
cuid: cms1km7wg00000akjb6nyaa5s
slug: the-one-where-backups-keep-your-latest-posts-on-your-github-profile-up-to-date
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/1feed15e-6ec4-4edf-9b50-c867f630befd.png
tags: github, markdown, hashnode, github-actions, automation-tools

---

A while ago I wrote [a post](/the-one-where-you-automatically-add-your-latest-posts-to-your-github-profile) about automatically adding my latest blog posts to my GitHub profile. The idea was simple: a GitHub Action would query [Hashnode's public GraphQL API](https://gql.hashnode.com/), grab my most recent posts, and rewrite a section of the `README.md` with them. Whenever I published something new, a webhook chain would kick in and my profile would stay up to date without me lifting a finger.

It worked great. Until it did not.

## The free lunch ended

If you visit https://gql.hashnode.com today, you no longer land on the friendly GraphQL playground I used back then. Instead, you get redirected to [this changelog entry](https://hashnode.com/changelog/2026-05-13-graphql-api-paid-access).

The gist of it is this:

> Every API request, queries and mutations, now requires a Pro plan on your publication.

So it is not just write operations behind the paywall. Even **reading** my own posts through the API now requires a paid subscription. Hashnode justifies the change as an abuse-prevention measure — apparently scrapers and spammers were mirroring content and flooding feeds — which is a perfectly reasonable motivation. But the practical consequence for me was that my nice little automation suddenly stopped working.

I had two options:

*   Pay for Pro just to keep a `README.md` table updated (overkill, to put it mildly).
    
*   Find another source of truth for my posts.
    

Thankfully, that source of truth was hiding in plain sight.

## The backups saved the day

If you read the [original post](/the-one-where-you-automatically-add-your-latest-posts-to-your-github-profile#the-missing-piece), you might remember that Hashnode offers a **GitHub integration** that automatically backs up every post to a [repository of your choice](https://github.com/backpackerhh/blog-posts).

Back then I only used that repository as the *trigger* for my automation — a push to it fired a webhook that told my profile to go and re-query the API. The posts themselves still came from Hashnode.

Then I realized **I do not need the API at all**. Every post is already in that repository, as a Markdown file with all the metadata I need in its YAML frontmatter.

Here is what one of those backup files looks like at the top:

```yaml
title: "The one about choosing where htmx logic belongs"
seoDescription: "Compare view-level events and server response headers to handle htmx failures. Explore the trade-offs and decide where your logic truly belongs."
datePublished: 2026-06-22T21:09:14.210Z
cuid: cmqppkjkw00000bj7cakd4owq
slug: the-one-about-choosing-where-htmx-logic-belongs
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/5bc78409-7883-4e5b-9ec4-afc3251424e8.png
tags: http, javascript, html, security, hypermedia, htmx
```

Everything I was fetching over the network — `title`, `slug`, `datePublished`, `cover` — is right there. No API, no token, no Pro plan. Just files on disk.

## The new GitHub action

So I created a replacement for the old action, [backpackerhh/github-latest-blog-posts](https://github.com/backpackerhh/github-latest-blog-posts), whose most important design decision is captured in the following:

> This action does not call any external HTTP API. The consumer workflow is responsible for checking out the blog-posts repository and passing its path to this action.

This is a fundamental shift in responsibility. The old action *fetched* the data. The new one *reads* it. The workflow checks out the backup repository, and the action just parses the Markdown files it finds locally.

The metadata and configuration of the action live in [action.yml](https://github.com/backpackerhh/github-latest-blog-posts/blob/main/action.yml), and its **inputs** tell the whole story of how the philosophy changed:

| Input | Required | Default |
| --- | --- | --- |
| `POSTS_DIR` | Yes | — |
| `BLOG_BASE_URL` | No | `https://davidmontesdeoca.dev` |
| `README_FILE` | No | `./README.md` |
| `OPENING_COMMENT` | No | `<!-- HASHNODE_POSTS:START -->` |
| `CLOSING_COMMENT` | No | `<!-- HASHNODE_POSTS:END -->` |
| `MAX_POSTS` | No | `5` |
| `COMMIT_MESSAGE` | No | `chore(docs): update recent blog posts` |

Notice what is **gone**: there is no `HASHNODE_PUBLICATION_ID` and no `GITHUB_TOKEN`. The only required input is now `POSTS_DIR` — the path to the local directory where the Markdown posts live.

A couple of things worth explaining:

*   `BLOG_BASE_URL` is used to reconstruct each post's public URL. Since the backups only store the `slug`, the action builds the link as `${BLOG_BASE_URL}/${slug}`. This means my profile links point to my own domain rather than to Hashnode, which I actually prefer.
    
*   `OPENING_COMMENT` and `CLOSING_COMMENT` keep the same `HASHNODE_POSTS` markers I already had in my `README.md`, so I did not have to touch the placeholders at all. Backwards-compatible by happy accident.
    

The **runs** section describes the execution environment. It uses `node24` as the runtime and `dist/index.js` as the entry point — same compiled-code approach as the old action, where the contents of the `dist` directory are what actually run.

As for how it actually reads the posts, the logic is refreshingly boring, and I mean that as a compliment.

The action:

1.  Scans `POSTS_DIR` for Markdown files.
    
2.  Parses the YAML frontmatter of each file, expecting `title`, `seoDescription`, `datePublished`, `slug`, and `cover`.
    
3.  Skips (with a warning) any file missing the required fields.
    
4.  Sorts the posts by `datePublished`.
    
5.  Takes the newest `MAX_POSTS` and formats them into a table between the comment markers in the `README.md`.
    

If everything goes well, a commit is created with the changes — exactly the behavior I had before. From the outside, my profile updates the same way. Under the hood, not a single byte crosses the network to Hashnode.

## Wiring it together

Here is the updated [workflow](https://github.com/backpackerhh/backpackerhh/blob/main/.github/workflows/update-latest-blog-posts.yml) in my profile repository:

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
      - name: Checkout profile repo
        uses: actions/checkout@v7

      - name: Checkout blog-posts repo
        uses: actions/checkout@v7
        with:
          repository: backpackerhh/blog-posts
          path: .blog-posts
          ref: main

      - name: Update README with latest posts
        uses: backpackerhh/github-latest-blog-posts@main
        with:
          POSTS_DIR: .blog-posts
```

Once again, a fair amount is going on here, so let me explain it:

*   The workflow can still be [triggered manually](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#workflow_dispatch) via `workflow_dispatch`.
    
*   It can still be [triggered by a webhook event](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#repository_dispatch), listening for the `trigger` event type — the exact same trigger my dispatcher sends.
    
*   The first [Checkout](https://github.com/marketplace/actions/checkout) step grabs my profile repository, as before.
    
*   The **second Checkout step is the key change**. It checks out `backpackerhh/blog-posts` into a `.blog-posts` directory. This is how the data gets onto the runner without any API call.
    
*   The final step runs the new action, pointing `POSTS_DIR` at that `.blog-posts` directory.
    

The best part of this migration is that [dispatcher.yml](https://github.com/backpackerhh/blog-posts/blob/main/.github/workflows/dispatcher.yml) in my blog-posts repository is untouched:

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

The chain of events is identical to what I described in the [previous post](/the-one-where-you-automatically-add-your-latest-posts-to-your-github-profile#the-missing-piece):

1.  I publish, update, or delete a post on Hashnode.
    
2.  Hashnode's GitHub integration pushes the change to `backpackerhh/blog-posts`.
    
3.  That push to `main` triggers the dispatcher.
    
4.  The dispatcher sends a `repository_dispatch` event with type `trigger` to my profile repository.
    
5.  My profile's workflow wakes up, checks out the backups, and rewrites the `README.md`.
    

The only link in the chain that changed is **step 5**, and even there, only the *source* of the data changed — from a remote API to a local checkout.

## Fewer moving parts, fewer things to break

Looking back, this migration turned out to be a small blessing in disguise. Comparing the two setups side by side:

|  | Old setup | New setup |
| --- | --- | --- |
| **Data source** | Hashnode GraphQL API | Local Markdown backups |
| **Network call** | Yes (per run) | None |
| **Hashnode Pro required** | Yes (now) | No |
| **Secrets needed by the action** | `HASHNODE_PUBLICATION_ID`, `GITHUB_TOKEN` | None |
| **Post URLs point to** | Hashnode | My own domain |
| **Failure surface** | API downtime, rate limits, auth, schema changes | Frontmatter parsing |

The new approach is not only cheaper — it is **more resilient**. There is no API to go down, no rate limit to hit, no token to expire, and no GraphQL schema that might change under me. The backup repository is the single source of truth, and it is the same data Hashnode itself would have served me. If Hashnode ever changes the shape of that frontmatter, that is the only thing I would need to adapt.

## Conclusion

When Hashnode put its API behind a Pro plan, my first reaction was mild annoyance at having something that worked perfectly well suddenly break. But the fix ended up leaving me with a setup that has fewer dependencies, no secrets in the critical path, and no external service to depend on at runtime.

The trigger mechanism I built back then survived intact — I just swapped the data source from a remote API to the local backups that were already being created for me. My original solution reached out over the network for data that was, in fact, already sitting on disk, and it took a paywall to make me notice the redundancy.

Sometimes the constraint that feels like a setback is the exact push you need toward the simpler design you should have had all along.

Thank you for reading and see you in the next one!
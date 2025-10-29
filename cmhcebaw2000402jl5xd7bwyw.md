---
title: "The one about a new project working for an American fintech"
seoDescription: "Discover insights into developing a new fintech tool, handling frontend challenges, and leading a project migration with Sinatra and htmx integration"
datePublished: Wed Oct 29 2025 19:35:45 GMT+0000 (Coordinated Universal Time)
cuid: cmhcebaw2000402jl5xd7bwyw
slug: the-one-about-a-new-project-working-for-an-american-fintech
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1761157290326/87cd9574-3b97-4c30-a212-6a499d74e73b.png
tags: ai, ruby, frontend, backend, frontend-development, fintech, backend-development, htmx, contractor, fintech-software-development, ai-tools, ai-agents, mcp, sinatrarb, claude-code

---

At the beginning of this year I talked about the [layoffs](https://blog.davidmp.es/the-one-about-layoffs-in-an-american-fintech) in the American fintech I work for as a contractor. That story is key to what comes next.

For a long time, the company was focused entirely on education. They finally **decided to diversify, betting on the travel and hospitality industry**. This new strategy kicked off a project at the start of Q3: building a new tool that allows the company's operators to manage the beneficiary network on behalf of clients.

To add some context, there is already a tool for managing beneficiaries still in use. The problem is, no one on the tech team is happy with it. The general consensus is that, looking back, poor design decisions were made.

In addition to that backend, there is an old SPA, developed with React, that collects the beneficiary data. These beneficiaries get an email notifying them that a client is missing some data to send a payment, which includes a link to a form. *Sounds like a scam, right?*

The new tool consists of **two brand-new applications**: a REST API, developed with Sinatra, and an SPA, developed with React.

The backend application is being developed by the same team that has had the governance of the old beneficiaries application, in the *Transactional* area of the company.

Some members of other teams were selected to temporarily join that team, including myself, to gain context about the project and, in time, we will become part of the **new beneficiaries team** to take over the governance of all the relevant applications.

The new frontend application, however, is being developed by a team from Romania. They are not part of the *Transactional* area, a decision made due to a lack of internal staff and the very tight deadline we already had.

The idea was to get an MVP ready by the end of the quarter for a demo with a major German client, a big name in luxury resorts and 5-star hotels. Landing them would be a huge boost, though it was not a *sine qua non* condition for the project to continue.

From the beginning, the head of the *Transactional* area was uncomfortable with the fact that a team outside of the area was developing that application, so he soon started pulling strings to see what the options were for starting what became known as "**the migration**."

According to what I was told, the main reason they chose me for this new team was my willingness to work with frontend applications. In this company, almost everything is backend, and the little frontend that exists tends to be internal-use applications that do not get much love.

I was asked to do an analysis of how long it would take to do the [migration with AI](https://blog.davidmp.es/the-one-about-shortcuts-in-cursor-with-karabiner-elements) for the two options on the table:

* Being an SPA, move the logic into another existing SPA, even if the applications were unrelated.
* Create a new [microfrontend](https://martinfowler.com/articles/micro-frontends.html) with **Sinatra + htmx** that would be embedded in the *transactional registry* application, where other applications are already embedded.

Initially, I had no doubt that the SPA option was the best one, especially if speed was the main goal. Of course, some adjustments would be needed. The two applications had been developed by different teams and the code was organized differently. For example, one managed state locally with `useState` while the other used Redux for managing the state of the whole application.

However, what they forgot to tell me was that a critical decision had already been made the previous year on future frontend development in the *Transactional* area: the preferred option is to create microfrontends with Sinatra + htmx.

The main reason being a general **lack of frontend expertise** among the area's developers. With the high rotation of team members in the same area of the company, they believed that sticking with React would just create friction for future changes or bug fixes.

Obviously, knowing that changed everything. **The final goal was to build the microfrontend with Sinatra + htmx in any case**.

Before making an estimate, I created a _proof of concept_ UI with [Claude Code](https://www.claude.com/product/claude-code) to generate a paginated list of beneficiaries, following the design of the current frontend application and the same approach as other microfrontends.

The experience was satisfying and frustrating in equal parts. Replicating the design and code from other applications was relatively easy (after iterating a few times), but it usually meant giving the AI agent too much context. As a result, it was very easy for it to end up doing too much or too little.

In the end, I made a rather **naive estimate of 4 weeks to complete the migration**. Even so, the head of the *Transactional* area thought it was too much time. The manager of the team later told me he had thought that with AI, it would be a matter of a couple of days.

Of course, I only consider that estimate naive from my current perspective, now that I have much more context on what actually needs to be done.

Shortly after that, I met with my manager for a couple of sessions where we did a much more precise estimation. We divided all the work into tasks with a very clear and reduced scope, and we added a conservative estimate of how long we might take for each one.

As a result of those two sessions, we **estimated it would take a single person approximately 8 weeks**. Of course, this is not a job where eight people could complete the work in one week. Many tasks simply cannot be parallelized.

With this new estimate, my manager got the buy-in we needed: if new features were strictly necessary, they would be added to the new React SPA, but during Q4, our team would focus entirely on the migration to the Ruby microfrontend.

In the meantime, I took on the task of replicating the existing functionality for collecting beneficiary information from the old SPA for the new beneficiaries application. This turned out to be a not-so-simple task as initially thought. I will talk about that in a future post.

Before the end of Q3, **they confirmed that I was going to lead the migration project**, which I have already been working on for a few weeks.

The project team consists of an engineering manager, a product manager, a designer, and three software engineers, including myself. The other two engineers will be more focused on backend tasks.

I started by defining the foundational tasks that will set the groundwork for our work in the coming months, while the product manager will take charge of defining the rest of the upcoming tasks.

I want to highlight that, as part of the detailed estimations we did, we factored in time to properly define the rules Claude Code would use, which are mostly already defined in a dedicated repository available company wide. We also dedicated time to learning how to get the most out of the AI with good prompting, though that is a continuous work in progress.

I have also been spending time deepening my knowledge of htmx and Tailwind, which I have little experience with so far.

At the moment, I am creating a **design system** in Ruby. I am basing it on components from our existing microfrontends and from the design in Figma, using Claude Code and [MCPs](https://modelcontextprotocol.io/docs/getting-started/intro) like [chrome-devtools](https://github.com/ChromeDevTools/chrome-devtools-mcp) and [Figma](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server) for assistance.

We have plans to create a gem that will allow us to reuse the components created for this design system in the various microfrontends that already exist.

I do not expect to talk about the project in the coming months, at least until we have finished it. Then, I will write a retrospective post sharing what went well, and what could have gone better.

I would also like to share the technical details of how we solved the obstacles that we will surely find along the way.

I appreciate the trust placed in me, even as a contractor. I believe it is the result of a job well done.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]

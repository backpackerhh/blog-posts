---
title: "The one about my experience working for an American fintech"
seoDescription: "Insights from my first 6 months as a contractor in an American fintech: team changes, technical challenges, security constraints, and lessons learned."
datePublished: Wed Feb 26 2025 17:00:47 GMT+0000 (Coordinated Universal Time)
cuid: cm7m5wb7i000109l57yylah2b
slug: the-one-about-my-experience-working-for-an-american-fintech-6-months
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1758564072564/140f54bf-40bc-419c-966c-e665288ab29e.png
tags: software-development, agile-development, agile, software-engineering, fintech, fintech-software-development

---

In a [previous post](https://davidmontesdeoca.dev/the-one-about-how-things-work-in-an-american-fintech), I talked about how things work in the American fintech I am currently working for as a contractor.

I initially planned to write this post after my first three months, but I postponed it when we learned that all teams in the *Transactional* area would be reorganized at the start of the year.

This change took me by surprise. I knew that my team had been formed at the beginning of 2024 with members from other teams, but I did not realize this was something the company regularly does at the start of each year. The goal is to **prevent knowledge from becoming siloed within specific teams**.

This time, the teams were restructured based on the **new architecture design**, which was announced at the end of last year in an All Hands Engineering meeting. The goal is to complete it by 2027.

Given all this, I decided to wait until I had first impressions of the new team before writing about my experience. It has now been six months since I started working on this project and one month since the new team was formed.

Let me start from the beginning.

## My first team

All the details about how that team worked can be found in my [previous post](https://davidmontesdeoca.dev/the-one-about-how-things-work-in-an-american-fintech#heading-my-team).

Although it might sound like a cliché, I really felt like everyone on the team welcomed me with open arms.

For the first few weeks, I met every morning with my **onboarding buddy**, who explained how each application in the project works. We also spent part of the day **pair programming** on tasks she was working on.

Normally, I would spend the rest of the day completing tasks from the board that I had been assigned as part of the onboarding. I also spent several weeks [configuring the macOS laptop](https://davidmontesdeoca.dev/the-one-about-working-with-macos-being-a-linux-user) they provided me.

During that time, **I had no autonomy and often felt lost**. I had no prior knowledge of the domain, and the project itself is quite complex, requiring many moving parts to come together. Still, my colleagues were incredibly patient and helped me as much as they could, especially when it came to testing tasks before moving them to "Ready for QA".

From the beginning, I noticed the team had been working together for several months because everything ran smoothly. Tasks arrived at refinements with enough detail, making it easy to decide what needed to be done.

I have to say that I integrated perfectly into their workflow. At first, I could barely participate in technical meetings, not due to a lack of technical knowledge, but because I lacked context and did not know the scope of certain changes. Over time, I gradually became more active in these meetings.

After applying several refactors and adding a couple of new features, I gained the trust of my teammates, who started to recognize how I could contribute and I quickly became a reference within the team when working with Ruby and applying best practices, as most of them had more experience with other programming languages. I always felt comfortable expressing my opinions and sharing ideas with them, even when we did not always agree. We would always reach a consensus for the good of the project. For example, I managed to [add RuboCop](https://davidmontesdeoca.dev/the-one-about-linting-in-a-legacy-ruby-project) to all projects under our governance.

For the most part of my career, I have been working with Rails, but I never had the chance to work with Sinatra before. I am enjoying **learning a simpler and lighter framework**, although sometimes it lacks certain useful features, such as many methods available in the [Time class](https://api.rubyonrails.org/classes/Time.html).

That being said, I have often missed having a well-defined company-wide standard so that all teams could follow certain guidelines ensuring we work similarly. I understand this is hard to achieve, but I think it would be great to work on a project this large and always know where to find things, how to name them, what to test, and how to do it. In short, I **miss consistency**.

Regarding security, this project is unlike any othe project I have worked on before. For example, in AWS, we only have read permissions, and we do not have access to the database console in production, which makes perfect sense. Because of this, we **rely heavily on observability**. Any data changes in production are done through Rake tasks, which must be requested via a support ticket.

We must also be extra careful during what they call "peak times", such as Black Friday and Christmas, because breaking something in production could mean certain transactions do not reach their destination on time, potentially involving millions of euros, dollars, or any other currency.

In fact, a couple of months ago, I developed a feature that had to be deployed to production and executed right before Christmas. Of course, **things did not go as planned**. As a result of running thousands of background jobs, response times for one of the most critical tables in one of the most essential applications skyrocketed. I plan to talk about it in a future post.

Thankfully, the issue only affected that table, but I felt terrible about it. **The engineering manager (EM) was very supportive**, telling me that these things happen and that we must learn from them to prevent them to happen again in the future. His words were a huge relief.

As for the rest of the teammates, I have a very good relationship with the two developers I worked with the most, who also live in Madrid. Before Christmas we met in person when another teammate came to town for a conference and I also attended the company's Christmas dinner, where I met several colleagues from other teams.

## My new team

We started working together in mid-January.

The new team consists of an EM, a project manager (PM), a QA engineer, a tech lead and 5 software engineers, including myself.

I repeat in the team with the EM, the QA engineer and my onboarding buddy.

We follow the same agile ceremonies as before, but one major difference is that we **do not have sprints**. This is an unusual way of working for me, especially since we follow agile principles in almost every other aspect.

Our governance over the different applications in the project is different now, with a couple of exceptions, such as the payouts application that I have mentioned in other posts.

All team members currently live in Spain, so we usually have our daily meetings in the morning and scheduled meetings still happen in the afternoon, such as refinements.

Interestingly, almost half the team works as contractors through SNGULAR.

A role we did not have in my previous team is the tech lead. He has been at the company the longest and ensures everyone understands the overview and implications of new features to guarantee everything fits together correctly.

During the first few days, he organized several workshops to explain the so-called "transactional registry", which is a crucial component of the new architecture.

We also had several **mob programming sessions** where we started developing together the first functionality in that application.

I will not go into much detail about what that service does, but I should mention that its governance belongs to another team. As I mentioned in [another post](https://davidmontesdeoca.dev/the-one-about-how-things-work-in-an-american-fintech#heading-the-project), that ownership is not strict, as any developer can make changes to any application. Out of courtesy, the team with governance over each application is tagged.

Even though that other team shares with us the EM and the PM, and the tech lead often collaborates with them, we have encountered in a month several occasions where both teams have worked on similar functionality, because of a **lack of proper communication**.

For example, I recently worked on a feature that made certain attributes of an event optional, including them in the payload only if those attributes in the original event published in another application had led to a change in the corresponding object of our application. Normally, such change would involve changing the version of the event, but since no one was consuming the event yet, we decided in the refinement not to change it. However, by the time the feature was developed, reviewed, and tested by QA team, a couple of weeks had passed. When I deployed the changes to production, we suddenly started to see errors in a third application related to that event I had modified.

How was that possible if no one was consuming that event? In the time between our refinement and the deployment to production, another team had created a new event handler in that third application. Fortunately, it was only logging some information, so there was no real impact. This is an example of the lack of communication I mentioned earlier.

We clearly have **room for improvement in terms of task planning**. However, given that we are a newly formed team, it is understandable that these things happen.

That said, our timeline is tight. Ideally, we should **complete several key components of the new architecture in Q1**. The team was formed with that goal in mind.

The EM mentioned that it is likely the team will stay the same during Q2, but beyond that, no one knows what teams there will be or who will be part of each team.

For sure there is a good atmosphere in the team, but we have different points of view on key aspects of our work. This goes beyond just how we write and organize code.

Most of us prefer tasks to be as detailed as possible before refinement, while the tech lead prefers the opposite. I assume we will end up somewhere in between, especially since some of us lack context on the applications we are working with, making it difficult to move forward without enough details.

Our **refinements are taking much longer than usual**. We have scheduled a weekly 1-hour refinement, but we have often extended it for several hours or even continue the next day.

In some cases, a refinement has even resulted in **re-refining some tasks already started** because we totally changed the way we want to approach it. That situation is not sustainable over time and, of course, is an issue that we discussed in our first retrospective meeting.

Everybody in the team respect and follow the tech lead's vision, but right now it is creating a bottleneck, especially regarding how we want to implement certain details of the code. When in doubt, he is obviously the go-to guy.

In the same way we must find our *sweetspot*, being able to identify where we can be more or less demanding, thus generating some **technical debt** if necessary.

I have to admit that on a day-to-day basis **we do not really feel pressure at all**, but being a new team, it is noticeable that we all want to deliver and sometimes we have not paid enough attention to the whole software quality process and because of that **we have had some unnecessary and relatively important bugs in production**.

During a 1-to-1 meeting, the EM told me that if I ever feel any kind of pressure, I should let him know so we can handle it properly.

However I have noticed that even though we all discuss the progress in our tasks during the daily meeting, the EM sometimes asks me in private about the progress in certain tasks. In some cases, he even have moved a task to a different column on the board before I do.

Even though he tries not to rush us, I get the feeling that he is under pressure from his own manager to ensure we deliver the expected features on time.

On top of that, security restrictions cause additional delays in delivery. For example, to take a new application to production, we must first complete an initial use case and review it with the security team. Once they validate that first use case, the new application becomes available for deployment, and we can then proceed normally with new use cases.

Finally, as for me, I have to admit that **my attention is somewhat divided** because I am still working on a task related to the production incident I mentioned earlier. As I said before, I hope to talk about it soon.

---

These first six months have flown by, and I have to say that not everything has been easy for me. However, the teams I have been part of have done their best to make me feel like one of them, even though I am a contractor.

This is by far **the most complex project I have worked on**. Many times, I have felt lost, but I never hesitated to ask for help, and the team was always there when I needed it.

It is a system where multiple applications interact, meaning **a lot of things can go wrong**. Moreover, this company operates in a completely different way than what I was used to in other projects, as production has far more security constraints than usual. And it makes sense, after all, we are constantly dealing with money, one way or another.

Just when I was starting to get into the rhythm of my first team, to better understand our processes and how our governance applications were connected, we had to split up.

I was really sad when I found out we would not be working together anymore. When I came back from the Christmas break, the team no longer existed. And unfortunately, it seems pretty likely that the same thing will happen again in the next few months.

On the other hand, I am happy because it is a **technical challenge** that is allowing me to learn a lot about the fintech world. That was one of my goals when I started looking for a new job last year.

Right now, it is a matter of being patient, as the beginning is always difficult for everyone. Hopefully, we will find our rhythm soon, and everything will start falling into place.

Thank you for reading, and see you in the next one!

---

%%[buy-me-a-coffee]
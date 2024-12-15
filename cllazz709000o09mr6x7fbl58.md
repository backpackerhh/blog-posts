---
title: "The one about my experience at Domestika"
seoDescription: "My experience at Domestika from beginning to end"
datePublished: Mon Aug 14 2023 14:56:05 GMT+0000 (Coordinated Universal Time)
cuid: cllazz709000o09mr6x7fbl58
slug: the-one-about-my-experience-at-domestika
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1691757835573/637a4895-4b27-4c72-8046-2c12d1abf605.png
tags: jobs, layoff, layoffs, domestika, layoff-plans

---

> Este post también está [disponible en español](https://blog.davidmontesdeoca.es/el-de-mi-experiencia-en-domestika).

For a few days now, I no longer officially work for [Domestika](https://domestika.org/). I can say without any doubt that, in some aspects, it has been my best working experience. So far. However, I leave with a bittersweet feeling and with the certainty that it was the best thing that could happen to me at this moment, since the last few months have been quite difficult and left a bad taste in my mouth.

Next, I'm going to share my experience in the company from the beginning to the end. In no way do I intend to make them look bad nor good, but to tell things from my point of view.

## Beginnings

I joined the company in mid-November 2020. At that time, there were two teams in the web department, _Growth_ and _Learning_, the latter of which I became a part of.

After a few weeks during which there were several changes within the team, some colleagues left and others arrived, for a while the team consisted of a tech lead, three project managers (yes, three... and there was even a fourth at one point!), three backend developers, and one frontend developer.

I was hired as a backend developer, but occasionally I also took frontend tasks to alleviate some of the workload on that side.

Very soon I could see that if Domestika stood out for something it was for the **human quality** of the people who were part of the company. I had the feeling that it was something that was taken care of very carefully.

For instance, during the onboarding process, every new team member was assigned an _onboarding buddy_ to help them with the process and a task board (set up the project, read the documentation, etc.), which included attending a _daily stand-up_ of every team you would likely collaborate with over time and introducing yourself. In my case, I attended a daily stand-up of _Growth_, _Mobile_, _Infrastructure_, and _Design_ teams. The goal was to get to know them, have them get to know you, and get an idea of how they worked.

We also had a Slack channel where you could optionally join, and every now and then, you would be randomly matched with another person present in that channel for a [donut session](https://www.donut.com/). You would then schedule a date and time to spend 30 minutes getting to know the other person, ideally without discussing work topics.

On a technical level, I was impressed to find at Domestika things like:

-   Every pull request (PR) had to be reviewed and approved by at least one team member.

-   Every PR had to include appropriate tests for the added or modified functionality.

-   The test suite automatically run for each created PR in our _Continuous Integration_ (CI) with Jenkins (later with GitLab).

-   All team members (including PMs) performed QAs for other team members' tasks based on defined criteria.

-   In staging the deployment approach was _Continuous Deployment_ (CD), while in production was _Continuous Delivery_ (CD).

-   We followed _Scrum_ with 2-week sprints and all the standard ceremonies (daily stand-up, sprint planning, sprint review, sprint retrospective).

-   If any task during the weekly _refinements_ raised doubts about how to complete it, a _spike_ would be scheduled before any estimation.

-   One day in each sprint was entirely dedicated to what we called _Bug Monday_ to fix some production-related bugs or address technical debt.

-   In the event of an important error occurring in production, we wrote _post-mortems_ to give visibility to the rest of the company.

Although some might find these practices commonplace in their day-to-day work, my experience is that it's less common than one might expect nowadays. Some of my colleagues confirmed later that their experience was exactly the same.

In addition to all that, as long as we were available for the most important meetings, we had a fully flexible schedule. It's so crucial for me that I'll never accept a job without that flexibility as long as I can afford it.

We also had the opportunity to attend one hour of English classes per week, which must not count as part of the working hours. It was a measure I never really understood and, honestly, never followed.

During that initial period with _Learning_ team, I'd highlight the project where we added new languages to the Domestika platform (French and Italian in that case). The team coordinated wonderfully to get it done, and in just a few weeks, everything was ready. We improved existing automated processes and added features to make adding new languages in the future easier.

Undoubtedly, it was a prosperous time for the company. They continued hiring people to join the teams, which led to changes.

## A change of course

The Domestika platform is developed in [Ruby on Rails](https://rubyonrails.org/). It's a project that began over a decade ago and has passed through the hands of dozens of developers. I'm sure each of us has always aimed to create the best possible code, but the reality is that approaching the project for the first time can be quite daunting.

In early 2021, the ambitious decision to decouple the Domestika platform was made. The backend would expose a bunch of [REST API](https://developer.mozilla.org/en-US/docs/Glossary/REST) endpoints to be consumed by the frontend through a [Single-Page Application (SPA)](https://developer.mozilla.org/en-US/docs/Glossary/SPA). For that purpose, _Decoupling_ team was formed. That team would gradually decouple the website, page by page, while the other teams would continue adding new features and maintaining the remaining, still-coupled parts of the website.

_Architecture_ team was formed too, primarily responsible for making decisions on how to approach the decoupling of the website, defining style guides, improving webpage loading times, enhancing test suite execution times, and more.

It's noteworthy that, as we were initially told, the intention was for the other developers who weren't integral members of _Decoupling_ team to rotate every _n_ sprints to collaborate on the decoupling of the website. However, that rotation plan was never actually put into practice.

In later stages, sometimes we would gather with _Decoupling_ and _Architecture_ teams so we could be aligned regarding entirely new functionalities that would be developed with the new API + SPA approach.

Additionally, _Platform_ team was formed to manage the administration panel of the platform.

Regarding existing teams, _Growth_ team at some point split into three teams, and _Learning_ team split (or was expanded, as per the words of the _Head of Engineering_) into two teams.

On one hand, _Learning Experience_ team, whose flagship project, Domestika Live, was in development for nearly a year but never saw the light of day. It's a shame because the effort put into it was huge, and they were never given the chance to see their creation in action. I'm unaware of the reasons behind that decision. After that, the team disbanded, and the remaining members who stayed with the company joined other teams.

On the other hand, _Teaching & Community_ team, which I became a part of.

There was also a significant change for me at that point. The company was struggling to find frontend developers with the required skills and due to my consistent interest in taking on frontend tasks, I was offered the opportunity to switch from a backend developer who occasionally worked on frontend tasks to a frontend developer who occasionally would work on backend tasks.

At first, I wasn't sure about the role change, but the _architect lead_ convinced me by showing how the decoupled frontend would work and how the entire platform would move in that direction. But actually I had to deal with a frontend in the Rails monolith where chaos reigned. I was never sure where to put anything, mainly in the absence of a clearly defined documentation and/or style guide. That meant having to constantly ask some colleagues with more context of that part of the project for help.

After the role change, _Teaching & Community_ team consisted of a tech lead, a project manager, a backend developer, and a frontend developer.

Personally, I consider that period my best time at the company, despite facing challenges like redesigning Domestika's project editor for nearly two months. We had a great atmosphere within the team and were very agile in getting work done.

Even the change of tech lead (the previous one moved to lead _Architecture_ team) didn't affect our atmosphere or pace.

And the fall of 2021 brought us a gift in the form of a project.

## A customized video player

By the end of the summer of 2021, a security issue was detected related with Wistia, one of our video providers, that was inadvertently exposing credentials that allowed users to download videos and overwrite subtitles.

Once that issue was fixed, the decision to create a custom **video player** for Domestika was made. That way, we would have complete control over the player's appearance, audios, subtitles, and thumbnails, while the providers would continue supplying the videos only. Managing video encoding, different resolutions, and other related stuff were deemed unmanageable at that early stage.

We were informed about the new project, and almost overnight, _Video Player_ team was formed to start working immediately. That team consisted of members from _Decoupling,_ _Architecture,_ and _Teaching & Community_ teams.

That new project led me to a role change again. With the idea of having an equal number of backend and frontend developers in the team, I switched back to being a backend developer. Initially, I was frustrated that they had made that decision without consulting me before, especially since I knew that the project would be entirely decoupled on the frontend side, and I'd have liked the opportunity to work that way, especially after spending some time working on the frontend of the monolith.

I expressed my concerns and the boss told me that I could still take on some frontend tasks if that's what I wanted. And that's how it started. However, the work we had to do in the backend side turned out to be much more engaging and demanding than I had anticipated. As a result, my focus was almost entirely on the backend for the 6 months or so we worked on the project.

During that period of time, I had the opportunity to lead the backend architecture of the new video player. Among many other things, I was in charge of critical tasks such as generating the **m3u8** files that the player itself consumed to play the videos and the orchestration needed to serve those files statically.

As of today, if I were asked during a selection process which project or piece of code I am most proud of, I'd undoubtedly mention what I did on that project.

In my opinion the sprints were not as well-planned as before, but in exchange, we had greater flexibility to pivot if necessary.

On a personal note, when I was about to reach my first year at the company, I started asking for a salary raise. Unfortunately, there was never transparency regarding salary matters at Domestika. Nobody was aware of the company's salary raises policy, whether raises were given collectively at a certain time of the year, upon reaching the anniversary at the company or any other option. Nevertheless, they gave me the requested raise.

In early 2022, it was announced that [Domestika had become a unicorn](https://techcrunch.com/2022/01/27/domestika-raises-110m-on-a-1-3b-valuation-to-expand-its-learning-community-for-creative-types/) after raising over 100 million dollars in funding and being valued at over 1 billion dollars.

The company continued expanding its presence to more countries and reached 800 employees, with a steady growing. Nonetheless, some things started to change during that same year.

## New projects

In the spring of 2022, we had already finished the new video player project. Although users could only experience it in course trailers where we used Vimeo as video provider. At least until the end of the year, we couldn't grant access to all users in course lessons due to encoding issues with some Wistia videos, which caused automatic resolution jumps while playing those videos.

Usually the problem occurred with older videos that Wistia had encoded differently internally and most of the time it was enough to re-upload the videos. On rare occasions that did not solve the problem and it took us some time to fine tune the problem until we found a definitive solution.

Upon finishing the project, _Video Player_ team disbanded, and each team returned to their usual tasks. _Teaching & Community_ team was renamed then to just _Teaching_.

Shortly after that, [news](https://www.epe.es/es/activos/20220428/domestika-tecnologica-despidos-ere-encubierto-13576187) broke that some former Domestika employees had accused the company of a hidden layoff plan.

Some concerns were raised among us at the company in spite of being reassured in All-Hands meetings that everything was fine, especially in the engineering department. Given that more and more developers were being hired, it seemed reasonable to believe.

Furthermore, during the summer, my salary was raised again, that time without me asking. I was told that the company wanted to avoid a talent drain.

Following the new video player project, we embarked on an unsuccessful project aiming to introduce a new subscription plan for users of the platform. We were instructed to implement it as quickly as possible, with the promise that if it worked, we would have the opportunity to take the necessary time to do it properly. I didn't believe it, so I was somewhat relieved when we were informed that the new subscription had not succeeded. I was convinced that we wouldn't have been allowed to start from scratch.

Right after that, our project manager introduced us to the most ambitious project up to that time, the **teacher dashboard**, aimed to improve the experience of the teachers on the platform.

As it was planned to spend around two years on that project, the team almost doubled in size within a few months, consisting of a tech lead, a project manager, four backend developers, four frontend developers, and a UI/UX designer.

In my case, I definitively stayed in the role of a backend developer, contributing less and less to frontend tasks.

And rightfully so. The teacher dashboard provided us the opportunity to start a project from scratch. It was clear that the frontend side would be a SPA, similar to what was being done for decoupled pages, but the backend side of those pages were using a REST API whose endpoints were developed with cleaner and better-organized code, yet still coupled to the Rails monolith.

Because of that, we had numerous meetings with _Decoupling_ and _Architecture_ teams to collectively determine our approach. The final decision was to use [Rails engines](https://guides.rubyonrails.org/v6.1/engines.html), leaning on the approach proposed by Shopify with [Upgrow](https://github.com/backpackerhh/upgrow-docs). That approach had influenced the development of the existing endpoints, but we aimed to be stricter, minimizing coupling with Rails and attempting to loosely apply [Domain-Driven Design (DDD)](https://martinfowler.com/bliki/DomainDrivenDesign.html).

Based on an initial proof of concept, we found that using Rails engines for what we intended was feasible. Still, I was surprised by the limited documentation available online for truly complex use cases in production.

I enjoyed that phase quite a bit, as I could once again lead the development of a project that was crucial for the company and would lay the foundation for backend work in the rest of the application. There was a lot of trial and error, but it was a continuous learning process, with invaluable assistance from my colleagues, of course.

Some highlights include extracting reusable code from the monolith to a gem and creating an authentication engine used both in the REST API and GraphQL.

Of course, not everything was a bed of roses. We had _carte blanche_ to do our best as long as we didn't encounter any obstacles, but at the very moment we encountered some difficulty in extracting certain functionalities from the monolith to an engine, the commitment to the whole engines approach became less certain. We were even told that the development of the engines was to be stopped, but I managed to make it possible for us to continue adding new functionalities to the engines. Luckily, almost everything related to the teacher dashboard was new, as was the next project I got my hands on.

By the end of the summer of 2022, users with a Domestika Plus subscription, both monthly and annual, would gain access to a catalog of 1,200 courses, selected according to a variety of business rules defined by the product team. For instance, a course could be available in the catalog for three months and would need at least one month before it could be a candidate to be available again.

It was another one of those projects that I enjoyed from start to finish, where I had creative freedom when it came to developing the code. In addition, it was a project where I collaborated very closely with a junior developer I had been mentoring in the previous months.

Seeing the company's significant commitment to that project, which was expected to be a _game changer_, nobody anticipated what was to come next.

At some point during that year (honestly, I don't recall when), all Domestika employees were gathered in an All-Hands meeting, and they informed us that they were closing all recording studios except those in Madrid. As a result, employees in Latin America, Italy, France, Germany, the United Kingdom, and the United States were affected. The worst part was that all those people found out they were losing their jobs at that very moment, along with the rest of the company's employees.

The company explained that the decision was made because it was more cost-effective for them to cover travel expenses for each teacher to come to Madrid from anywhere in the world than to maintain recording studios in those other countries.

I remember talking about it later with the rest of my team and we were surprised by the decision. Although it might be reasonable if they had calculated the costs of both options, we were particularly surprised by the way it was notified.

Other than that, they assured us again that everything was going well in the company and that we workers in Spain didn't have to worry, because that decision didn't affect us.

And they kept telling us the same thing in each and every one of the All-Hands meetings in the following months. Even in the last one, shortly before everything changed definitively.

## The beginning of the end

Before we continue, there's a small detail that needs to be mentioned to better understand what comes next. According to the information from [this news article](https://www.epe.es/es/activos/20220428/domestika-tecnologica-despidos-ere-encubierto-13576187), Domestika has several subsidiaries, two of them in Spain: **DMSTK** and **Estudios de Grabación Digital**.

So, on February 22, 2023, we received an email in our company mailbox. The email informed us about the company's intention to initiate a layoff plan (ERE in Spain) at _DMSTK_ that would affect 89 employees, the subsidiary for which I was working at that time.

On the same day, a new Slack account was created where we could discuss the process. For more sensitive matters, private channels were created to ensure that no one from the company's high ranks could access. The first step was to select employee representatives, with a maximum of 3 for each workplace, which were Barcelona and Madrid. We had 15 days to complete that process.

Then we had a meeting with Colectivo Ronda, the lawyers that had already handled the case of the alleged hidden layoff plan from the previous year, and we decided that they were the most suitable option to represent us. After the 15-day period mandated by law to choose employee representatives, a 30-day negotiation period with the company's lawyers would begin.

From the moment we received that email from the company, nothing was the same. No one in my team had any experience in layoff plans, so uncertainty was at its peak. Every meeting we had revolved around that situation, as could be expected. But those weeks since we were notified until layoffs took effect were endless for everyone, especially for those who were on the list of affected employees.

As I see it, during that time, you could approach the situation in two ways. Either you didn't feel like working (which was more than reasonable) or you used work as a way to escape from thinking too much about the possibility of being let go. Personally, I belong to the second group. That's why I continued working on a course recommendation system that I had started a few weeks earlier.

The negotiation period began and the list of affected employees was provided to the lawyers on day 1, although obviously it wasn't made public. Each employee had to contact the lawyers by email, and they would inform us whether we were on the list or not. In my case, I wasn't included.

The company claimed that they didn't have sufficient profits to be able to keep all the employees, although neither the lawyers nor the finance people affected by the layoff plan agreed based on the data available. There were rumors at that time that the intention was to sell the company, but as of now, it seems that nothing concrete has materialized if that is indeed their intention.

The negotiation period had already started when it was leaked to the press that [Domestika was planning to replace employees with AI](https://time.news/no-this-spanish-company-does-not-lay-off-half-its-staff-to-replace-it-with-chatgpt-it-is-because-it-is-sinking/). And as is often the case in such situations, those news went viral. I'd like to take that opportunity to tell the truth about that story.

The news article mentions that [ChatGPT](https://openai.com/blog/chatgpt) would be in charge of the translation work at Domestika from that point on. And here's where I believe I can shed some light on the matter.

At the moment of writing, Domestika website is available in 8 languages. Among other tasks that I am probably unaware of, the translation team was in charge of manually translating the subtitles of the course trailers in each of the languages to ensure the best possible quality, in addition to the contents of each course in its original language. For years, for the remaining languages an automatic translation has been provided by **Google Cloud Translate**, with the [SDK developed by Google for Ruby](https://github.com/googleapis/google-cloud-ruby/blob/main/google-cloud-translate/README.md). Other parts, such as the public forums and each course's forum has been typically automatically translated too.

It's true however that at certain point we had to change the code so those parts that were still manually translated could be automatically translated. Similar to what we were already doing. ChatGPT wasn't involved in any way.

I think it was important to clarify that detail. With that said, I'll continue with the story.

After the negotiation period ended, the lawyers secured a severance package with a payment of 33 days per year worked at the company (the legal requirement is 20 days), among other things. Essentially, it was as if the company acknowledged a wrongful termination.

Finally, the layoff plan took effect on April 19, and on the same day, the employees of the subsidiary _Estudios de Grabación Digital_ were informed about the intention to initiate another layoff plan affecting 89 employees. Once again, the same number of layoffs.

Due to the lack of transparency from the company about what would happen in the future after the whole process ended, many colleagues who weren't affected by the layoff plan decided to leave voluntarily, already having an agreement with another company. In my case, I chose to stay and see what the uncertain future would bring.

The _VP of Engineering_ told us that due to those voluntary departures, they had to change their plans and reorganize the teams they had initially planned. Instead of keeping gradually decoupling the website, we would enter in **maintenance mode** for the monolith. There would be no new developments, so our focus would be on ensuring that nothing broke.

From that point on, there were two teams in the web department: _Ecommerce_ and _Learning_, the latter of which I became a part of again.

We did little work during the next few months. We gathered for the daily stand-up meetings to discuss our personal lives and share our plans for the future. Those who were in selection processes with other companies shared their impressions and progress. It was a time to get to know each other better, especially those who were in other teams and were assigned to ours.

Until one day, the CEO of the company, who hadn't shown up when the first layoff plan was announced, had the nerve to ask us through a Slack channel to welcome two new employees who would take up positions as business and technology managers (the latter alongside the current _VP of Engineering_). Those two people were co-founders of a "live" course platform operating in India, in his own words. Of course, absolutely no one reacted to that message.

People were quite upset by all that situation because in addition to the theory of the sale of the company, there were also rumors of the possibility that they wanted to replace us with cheaper labor, specifically from India. And that message didn't bode well.

A few weeks later, on June 9, 2023 (a Friday at 1:00 PM), the same day that the layoff plan for the subsidiary _Estudios de Grabación Digital_ took effect, we received another email in our company mailbox. The email informed us about the company's intention to initiate another layoff plan at _DMSTK_, that time without specifying how many employees would be affected.

And again the same process. We selected employee representatives and the negotiation period stipulated by law began again. However, in practice, that time it was somewhat shorter than before. The lawyers gave us that option since the negotiations for the previous layoff plans were recent, and they were almost certain that they would secure the same severance package.

A few days after announcing the new layoff plan, the following image from Glassdoor was leaked:

![Job offers in India](https://user-images.githubusercontent.com/685978/259871808-e930e9e9-710a-4729-887b-83b7f18bb724.jpeg align="left")

Salaries are shown in Indian rupees, which, when converted to euros, range from around 4K to 10K. It was then confirmed that those rumors of seeking cheaper labor were indeed true.

Apparently, they must have been embarrassed because they quickly removed those job offers from Glassdoor once they realized that the image had been leaked.

As for the team, although we had discussed the possibility of another layoff plan, I suppose we didn't expect it to happen so soon. Either way, it confirmed that Domestika seemed to lack a clear direction. In our day-to-day work, we continued with the same routine as mentioned earlier. We gathered to talk about ourselves, but there were no tasks to do.

Seeing the situation, I decided to talk to the _VP of Engineering_ and ask him to do everything possible to include me in the list of affected employees for this layoff plan. He said he understood that I wanted to leave and that he would do his best to include me. Most, if not all, members of my team did the same, and fortunately, we were all included.

We were also surprised that a member of the Mobile team, included in the list of affected employees by the layoff plan, was contacted directly on LinkedIn by a developer from India who told him they would be colleagues at Domestika. The company didn't provide any notification about it.

At the end of the negotiation period, with 46 employees affected, our lawyers agreed with the company on the same severance package as in the previous layoff plans and a paid leave from July 10 to 31, 2023. Therefore, I didn't have to work for Domestika anymore.

When I left, the web department consisted of a tech lead, a backend developer, and two frontend developers. No project manager nor UI/UX designer.

Guess what? On the same day our layoff plan took effect, the employees of the subsidiary _Estudios de Grabación Digital_ were informed about the intention to initiate yet another layoff plan.

As if that wasn't enough, it seems that they are now laying off employees in India as well:

![Developers in India apparently being laid off](https://user-images.githubusercontent.com/685978/257259670-3c85078d-79fe-4660-a380-bbf8fcbf2093.png align="left")

With everything that has happened over this year, I think Domestika is harvesting an image from which it will hardly be able to recover. Just look at the [reviews on Glassdoor](https://www.glassdoor.com/Reviews/Domestika-Reviews-E749272.htm).

There were many good things, but little, if anything, of that seems to remain now.

## Farewell

What I feel as I leave is sadness, for what it was and how it ended.

The people were the highlight of this experience, without a doubt.

I wish the best to those who remain, especially to those I've worked most closely with during these two and a half years.

I also wish the best and express my gratitude to all the teammates I had during this time.

Hopefully, what lies ahead for me will be at least as enriching as my experience at Domestika.

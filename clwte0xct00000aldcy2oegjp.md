---
title: "The one about my favorite project (so far)"
seoDescription: "Discover all the details about my favorite project so far."
datePublished: Thu May 30 2024 15:05:57 GMT+0000 (Coordinated Universal Time)
cuid: clwte0xct00000aldcy2oegjp
slug: the-one-about-my-favorite-project-so-far
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717068677764/615f4ad9-0ec2-40fd-bb94-0a46baa95f82.png
tags: aws, projects, software-architecture, domestika, video-player

---

My favorite project ever is the custom video player developed during my time at Domestika. More details about my experience at that company can be found [here](https://blog.davidmontesdeoca.es/the-one-with-my-experience-at-domestika).

At some point a security issue was detected related with Wistia, one of our video providers, that was inadvertently exposing credentials that allowed users to download videos and overwrite subtitles.

Once that issue was fixed, the decision to create a custom video player for Domestika was made. That way, we would have complete control over the player's appearance, audios, subtitles, and thumbnails, while the video providers would continue supplying the videos. Managing video encoding, different resolutions, and other related stuff were deemed unmanageable at that moment.

The *Video Player* team was formed to start working immediately consisting of members from 3 different teams. In the previous year I had been a full-time frontend developer, but for that project I switched back to being a **backend developer**. We worked on that project for 7 months approximately.

In our team we were used to work with Scrum, but for this project we needed more flexibility to pivot if necessary.

We had two video providers:

* Vimeo for course trailers only. Those videos are free for everybody, as the trailer is the best way to promote a new course. For that reason, the traffic generated in our application for those videos usually is greater than for other videos.
    
* Wistia for course lessons mainly. Those videos are only accessible for users that buy the course or have an active monthly/annual subscription when the course is available in the catalog of open courses.
    

The work in the backend side of this project could be divided in four different phases. Let me walk you through them.

**Phase 1**

This was the *state of the art* when the *Video Player* team started the new project.

The **master m3u8** file served was most of the time generated *on-the-fly* for mobile apps only, using a REST API endpoint.

In the web, the end user had access to the player from each video provider.

The codebase was full of conditionals that would check if a given video was a trailer or not, to later check if the video had dubbing or not.

Basically we had four branches regarding the generation of the master m3u8 file:

* For a trailer without dubbing, we would download the master m3u8 file provided by Vimeo and immediately serve it to the video player.
    
* For a trailer with dubbing, we would download the master m3u8 file provided by Vimeo and add the dubbed audio track, to then serve it to the video player.
    
* For a lesson without dubbing, we would create a custom master m3u8 file without any audios, because the video already included the original audio.
    
* For a lesson with dubbing, we would create a custom master m3u8 file with all the available audios.
    

At this point, we had an integration for each provider so we could store in the database a record for each resolution of a video. In the case of Vimeo, we would store an extra video record with the link to the HLS version, that is the link to the master m3u8 file. Regarding audios, we only stored records for dubbing tracks.

![domestika video player phase 1 diagram](https://github.com/backpackerhh/blog-posts/assets/685978/c691bc69-5cfa-410e-804b-cc24e6df5e91 align="center")

**Phase 2**

After some considerations, the backend members of the *Video Player* team made following decisions:

* Investigate in multiple spikes how to calculate the appropriate bandwidth for each video and how to generate audios, subtitles and thumbnails:
    
    * The decision was that we would use [ffmpeg](https://ffmpeg.org/ffmpeg.html) as the tool to generate every resource.
        
* Unify all relevant code so the process to serve a video would be the same regardless the client (mobile, web or any other), the video provider or whether the video had dubbing or not.
    
* The original audio would be extracted from the video with the lowest resolution, to make the extraction process faster, and a record would be created in our database for it.
    
* Store generated files on a [AWS S3](https://aws.amazon.com/s3/) bucket using a non-sequential identifier for each video as part of the path.
    
* The new [master m3u8](https://github.com/sethdeckard/m3u8) file would be served dynamically using the same REST API endpoint than before, enabled only for the members of the team under a feature flag.
    

The code and the process to generate the master m3u8 file was simplified as much as possible:

![domestika video player phase 2 diagram](https://github.com/backpackerhh/blog-posts/assets/685978/d1f3fd33-5cc1-4826-afe0-b25c28f43d38 align="center")

We set a few *coding rules* aiming to keep our code simple and extendable, using **dependency injection** in our production and test code; and avoiding references in classes, methods and variable names to file formats (m3u8, vtt, m4a), video providers (Vimeo, Wistia), video types (trailers, lessons) or dubbing.

Every video record in our database has an attribute with a reference to the video provider name, so we defined a factory method that would return the *namespace* where specific objects are placed for each provider. As simple as that. No more code full of conditionals. Besides, adding a new provider would be extremely easy.

At this point we faced some challenging issues:

* On one side, the mobile team detected that subtitles were out of sync in some videos. After some research, we found that only happended in long videos where the teacher was not talking for a long period. Finally after some trial and error, we found that overriding the duration of every segment automatically generated by ffmpeg with the value of the duration used to split the whole subtitles file would fix the issue.
    
* On the other hand, after extracting the original audio we detected that sound was out of sync in trailers only. The sound was perfectly in sync in Vimeo's video player, so we thought it was definitely something wrong in our side. I tried tweaking every parameter provided to ffmpeg to extract the audio, and after a lot of research of the (literally) hundreds of other parameters that the tool has available, I found the `muxdelay` parameter. After applying a delay of 0.3 seconds, the sound was perfectly in sync. With the use of the provider factory was quite easy to apply that delay to Vimeo videos only.
    

Looking back, this was not the more difficult phase, but was definitely the more important, because it was the one that laid the foundations and the longest one.

**Phase 3**

I designed the architecture we would use to make everything work as we expected in this new phase and worked directly on the most relevant parts of the code.

The goal was moving all the traffic generated by users playing videos off the application. For that purpose, we would generate **static master m3u8 files** that would be stored on S3, along with the rest of files generated.

All those files would be then cached using [AWS CloudFront](https://aws.amazon.com/cloudfront/) as [CDN](https://www.cloudflare.com/learning/cdn), including a query params that represented the timestamp of the last modification date of each file on S3. Therefore, the cache would be invalidated every time the file is uploaded to S3, because we used the exact same path.

Every time an audio, subtitle or video was modified, we would enqueue some background jobs in Sidekiq to regenerate all the required files and the master m3u8 file if needed.

Everything worked like a charm with Wistia videos, but Vimeo had other plans for us.

The links provided by Vimeo to access every version of a video that we used to generate the m3u8 files do not expire but redirect to other link that include a **token valid for 4 hours only**.

I contacted Vimeo's customer support asking for any possible way to get those links without expiration and they basically said that there was nothing they could do about it.

With that unexpected limitation we had to recalculate route and I proposed the team an intermediate solution, that was later approbed by the infrastructure team as well.

The idea was keeping a **semi-static master m3u8 file** stored on S3, that would contain only audios and subtitles. Using a serverless function we would get that file from S3 and the links to the videos at runtime to dynamically generate the master m3u8 file.

For that purpose, we used an [AWS API Gateway](https://aws.amazon.com/api-gateway/) in combination with an [AWS Lambda](https://aws.amazon.com/lambda). The endpoint would receive 3 query parameters:

* The URL to the static master m3u8 file stored on S3.
    
* The URL to the master m3u8 file provided by the video provider.
    
* The fallback URL to dynamically generate the master m3u8 with the same old REST API endpoint as before in case of failure.
    

That approach allowed to achieve the goal of keeping the traffic off the application and we could cache the generated master m3u8 file for a few hours if needed.

Everything worked perfectly on the web and the iOS app, but during the **QA process** our colleages from the Android team detected that videos could not be properly played in their platform.

I jumped on a call with the teach lead of that team and we spent a few hours debugging the relevant Android's source code until we found that for the code to properly work we needed to add **.m3u8** at the end of the API Gateway URL path.

By the way, something I didn't mention before is that with every change we made we had to be extremely careful because it could affect the mobile apps. As you probably know, releasing a new version of a mobile app is not as simple as it is on the web where a change is immediately available to all users if you want.

At that point we expected everything related to the new architecture to be working just fine, but that was not the case.

We noticed in our monitoring system, [Kibana](https://www.elastic.co/kibana), that we kept receiving an unexpected number of requests to the REST API endpoint, that as a reminder, should be just a fallback in case of failure in the lambda function.

I jumped on another call with the teach lead of the infrastructure team and we spent a while checking every part of the architecture and didn't find any possible error. We were logging every important part of the process, so the we believed the problem was not there. We then proceed to check the configuration on AWS and he thought that maybe the problem was that the API Gateway was created in a [VPC](https://www.cloudflare.com/learning/cloud/what-is-a-virtual-private-cloud/) and Vimeo was blocking our requests to get the videos. He changed the configuration to public and everything started working just fine. Suddenly, we were receiving less and less requests to the REST API endpoint.

In case all those issues were not enough, next you will find other challenging issues we faced along the way:

* The **orchestration** was not easy, because this new process had many moving pieces. A master m3u8 file includes links to other playlists for audios, subtitles and videos, where each one of them include links to other m3u8 files for each segment. The order in which those files were regenerated to invalidate the cache mattered and we needed to ensure the right order. For instance, we added some boolean columns to specific tables that we would use as flags to know when a specific process had been completed with success and then a new background job would be enqueued to continue with the rest of processes.
    
* We received lots of notifications in our error tracking system, [Honeybadger](https://www.honeybadger.io/), from processes responsible for generating the resources needed due to missing records. Those processes run in background jobs that would check if the expected record exist before performing any action. However, between that check and the moment that record is used inside some service object, the record could be already missing, due to the way we recreate audios and videos in the database when something changes. To overcome that limitation, we had to apply **pessimistic locking** in the whole transaction until the process finished, releasing the record afterwards.
    
* For some reason, some files were missing on S3, so it could mean a bad UX for the end user because a missing audio, for instance, would freeze the video. One member of our team thought that those missing files maybe were missing due to background jobs that end up dead before completing during a deployment to production. The task to validate that hypothesis was assigned to the infrastructure team and we added a **cronjob** that every morning in a very efficient way checked the files stored on S3 for each video and the list of files that we expected to find. If both lists did not match, some background jobs were enqueued to generate the missing files.
    

At that point, everything was pretty much done and with the help of feature flags we started making available the new video player to a small percentage of users on the platform.

**Phase 4**

Don't you think for a moment that there was not still room for more issues.

Users started reporting jumpy resolutions while playing some course lessons. For instance, you would start playing a video with 1080p resolution and suddenly the resolution would automatically jump to 480p, and later on it would automatically jump to any other available resolution.

Everything was working fine with trailers.

We were shocked because we never found that issue during our QA processes. The people in the support team couldn't keep up with the workload so the people in charge decided to disable the new video player until we could find a solution.

Although we thought that probably was a problem in the backend side, we couldn't discard anything at that moment.

The video player uses a custom version of [video.js](https://github.com/videojs/video.js), so the frontend team tried using the default version of the player and we found the exact same issue. They tried with other video players and again found the exact same issue.

The backend team implemented a service object that could detect if any video segment contained an invalid codec using [ffprobe](https://ffmpeg.org/ffprobe.html) tool. We created a list with all the affected videos and provided that list to Wistia's customer support. They told us that most of the videos affected were videos that were encoded with a very old codec. The suggested solution was as simple as reuploading the videos, so a new codec would be used instead. And that worked for most of the videos, but not for all.

After that, the *Video Player* team disbanded and my team stayed as the owner of the project. We kept investigating for a while without much luck until we finally found a solution.

At that moment, Wistia was already providing a master m3u8 file so we started extracting the **bandwith** from those files instead of doing our own calculations with ffprobe tool, as we were already doing for Vimeo videos.

Besides, we found that the audio segments extracted for the original audio of every video contained a **video stream** that was causing issues while playing the video, so we needed to tweak the order in which we were providing the parameters to ffmpeg. Basically, we must block the video stream before providing the input URL. We then had to extract all original audios again and upload them to S3.

With all those changes in place, we started opening the new video player to a small percentage of users again, we checked that everything was working fine and we kept opening the new video player to more and more users until it finally was available for everybody on the platform.

---

And that is my favorite project so far. It was not easy. It took a lot of hard work from the whole *Video Player* team and from people from other teams as well. Kudos for everybody.

I'm really proud of the work the whole team did and feels great that it is a functionality that is now being used by every user on the platform.

Note that here I'm talking about the backend side of the project but the frontend team also did an amazing job with lots of cool functionalities.

Personally I took lot of responsibility in a key project of the company and I think I delivered.

Thank you for reading and see you in the next one!
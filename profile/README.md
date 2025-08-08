# Project Pewstats

## Or how I learned that I like to roleplay being a developer
This project begain in June of 2020, when, doing COVID lockdowns, boredom and the heavens know what else, @eiriksf and myself decided that all that juicy PUBG we were playing generated a lot of data. 

Being geeks, we wanted data. So in between playing PUBG, we created a technological Frankenstein of various backend like scripts built in R, and a Discord bot built in Go. In 2020, I had been coding in R off and on for about three years, mostly doing data analysis. 

In writing the first version of Pewstats, I learned that there's a big difference between running scripts locally for data analysis, and writing "production grade" code that runs automaically on a server. Note: When I write "production grade" it should be interpreted as "shitty code that mostly runs without crashing too much". 

I also learned that I really enjoy coding, and what's worse, creating pipelines, doing pull requests and what have you. The project became a giant roleplaying session. 

### Version 1.0 
The first iteration consisted of a couple of R scripts to fetch data from the PUBG match API, that fed a database with stats, and created an endpoint of sorts that a Go-based Discord bot polled to get data. 

### Version 2.0
After a while, I continued my learning journey, and built a webapp using the shiny package in R, and dabbled with Azure to host it. In August 2021, it was re-launched as PewStats 2.0, with more data, and somewhat more functionality. In the span of a year though, the project had accumulated something like 10 years of tech debt. Sometime in 2022, I kind of lost interest in the project, and it was dormant for some time.

### Version 3.0 
In May 2023 however, I got back at it, and voila, we have PewStats 3.0. This time around, we decided that the cloud is out, so our resident hardware guru @paulat and myself bought a Threadripper 1990X, invested in 128GB of RAM and a 1080 Ti GPU, which is now living with @paulat. He likes to watch system utilization graphs, and complains when I use too little RAM. Being a citizen developer, I try to deliver some shitty code so that it uses extra RAM. It's a good symbiose that way.

### Version 4.0
Sometime in the winter of 2023/2024, there were quite a few bugs, and progress on adding a webapp and match timelines / qualitative summaries died slowed to a halt. This, coupled with a ever growing database that filled up the disk meant that motivation died down, and the project was halted for about a month.

In March 2024, the project underwent a rewrite of the larger modules, and I swtiched from using mysql to hold the telemetry data (basically data about every loggable action that happens in a match, amounting to between 30 and 40.000 rows of data per match) to storing telemetry as Parquet files. This reduced size and time to read by about a bazillion procent, or something much like it. 

### Version 5.0 
At this point, most of the oldest legacy code from 2020 has been rewritten, and I can mostly look at the code without being (too) ashamed of it. However, there are still some leftover legacy, particularly on the infrastructure side. All scripts are run using crontab, which.. works of a sorts, but it's not really optimal, and there are little to no logging and warnings when things do go sideways. I also think that there is a lot of potential that haven't been realized, such as more interactivity and data analysis / graphics. 

In the current running version, a summary of each match is published to Discord. All data is stored, and I've implemented a basic achievements system which posts achievements to Discord (but there is no comparison or browing of own achivements). 

As I write this, it's mid August 2024, and version 5.0 is up. I'm moving the code to new repositories, and a roadmap is in the works. 


### Version 6.0 

At some point I think we (meaning me, but it sounds less lonely when I write we) need to stop with the versioning and accept that it's really been a long, long early access. Perhaps we are actually closer to a v.1.0 than a v.10.0 which is where we are headed at the current pace. In any case, there has been some rather lager developments lately, so here goes. 

In the last six months, I first rewrote the pipeline code into an R6 class. This caused a fair amount of headahces since I wasn't really familiar with R6 and/or OOP, but after a fashion, it works quite well. This then was deployed, and things were quite stable - i.e. running for months without much hiccups. 

During July of 2025, I looked into joining a Norwegian eSport team as their in-house volunteer data guy. This prompted further rewrites, because the then current iteration didn't really scale that well beyond 10-15 users that the pipeline tracked, and all interactions was directly with the database. In true TeamPew style, this prompted, guess what, another rewrite. 

The good thing about the rewrites is that every time I do it, I learn something new, and it usually improves the solution. This time, it's starting to look like something that is getting close to production grade, at least for a citizen developer like myself. What I've done is: 

- Realized that containers are the way to go, so all services apart from the database and telemetry storage is now containerized (I did containerize the database as well, but after a small bout of an overeager developer coupled with an AI-agent with more permissions than it should, we (in this case, me and the AI-agent) decided that the database should live as a host service. This has caused significant cursing when dealing with docker networking, but it works.
- Moved from a single run pipeline to a multi step pipeline with queuing
- Implemented RabbitMQ (for queuing)
- Ditched plumbeR as the API, and adopted FastAPI (i.e. Python)
- Rewritten the Discord bot (still in Javascript), now interacts with the FastAPI
- Implemented workers (in R) to handle the pipeline
- Implemented Komodo to deploy and handle the containers
- Implemented docker container registry in GitHub, with GitHub Actions that rebuild the images on new releases

It's now August 2025, and while not everything is back up, it's getting closer. The system is now super over-engineered for #instant-insanity, which at the most usually tracks 10-12 players. The current setup can easily handle 150 players, and at that point, the primary bottleneck is the API rate limit (10/minute) from the official API. I've implemented rate limiting and throttling in the pipeline, so it can handle more than that, but at some point the pipeline takes a long time to run given those limits. 

I've also refactored parts of the pipeline that was quite slow (processing telemetry), and there are now four parts of the pipeline: 

- match discovery (handled by the pipeline-scheduler service) : checks for new matches, updates the database and creates a message in the queue
- match summary (handled by the match-summary worker): updates the database with a summary from the /matches endpoint, as well as grabbing the telemetry URL
- match telemetry processing (handled by the telemetry worker): downloads the telemetry data, transforms it to parquet format
- match stats processing (handled by the match-stats worker): updates a lot of telemetry related stats to the database

There are two primary benefits from this. First and foremost, an error in a single match does not stop the whole pipeline, it just updates this match to failed and goes about the next one. Secondly, it allows for scaling, where I can adjust the number of workers via Docker-compose (or rather Komodo). 



### About the team

I am using team in the smallest sense, right now the "team" is mostly myself, and while I like talking to myself and writing readme files that only I read, calling it a team standup is a stretch. At various points in the last four years, it's been @eiriksf and myself, so I guess that makes it a team. Plus I randomly shoot him messages asking for help and/or comments (since he actually works as a developer and pretends to know things). 



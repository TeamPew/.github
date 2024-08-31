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

### Roadmap

#### Backend
- Move to pipelines using `maestro` instead of crontab [DONE]
- Setup logging and warnings when pipelines crash
- Setup performance graphs using Grafana or similar [DONE]
- Split some of the larger modules into parts
- Create necessary backend services to support a Discord bot and a webapp

#### API 
- Expose necessary backend services as endpoitns
- Document using swagger
- Logging

#### Discord bot
- Enable some user interactivity of sorts
- Logging
- ....
- Profit!

#### Webapp
- Stats, all the stats
- Some logging?!
- And graphs, more graphs!

#### Infrastructure
- Setup a development/test environment as well as staging and production
- Pipelines to push code to the server
- Unit tests

### About the team

I am using team in the smallest sense, right now the "team" is mostly myself, and while I like talking to myself and writing readme files that only I read, calling it a team standup is a stretch. At various points in the last four years, it's been @eiriksf and myself, so I guess that makes it a team. Plus I randomly shoot him messages asking for help and/or comments (since he actually works as a developer and pretends to know things). 



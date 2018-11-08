# velocity-london-2018
Notes related to the Velocity 2018 conference in London

# Visited talks
- [A global source of truth for the microservices generation](#a-global-source-of-truth-for-the-microservices-generation)
- [Securing serverless - By breaking in](#securing-serverless-by-breaking-in)
- [Kubernetes is not for developers](#kubernetes-is-not-for-developers)
- [Building resilient serverless systems](#building-resilient-serverless-systems)
- [Lessons learned from 300.000 lines code for infrastructure](#lessons-learned-from-300000-lines-code-for-infrastructure)
- [Everything you wanted to know about monorepos](#everything-you-wanted-to-know-about-monorepos-but-were-afraid-to-ask)
- [Building a distributed real time stream processing system](#building-a-distributed-real-time-stream-processing-system)
- [Architecting for TV](#architecting-for-tv)
- [Victims of our own success](#victims-of-our-own-success)

## A global source of truth for the microservices generation
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/71092)

Where does the data lives? The data is living in events
View [The database inside out](https://martin.kleppmann.com/2015/02/11/database-inside-out-at-salesforce.html)
Take only the data that you need.  Minimize the amount of data that is flowing though your system.

Broadcast events.
Cache shared datasets in the log and make them discoverable.
Let users manipulate event streams directly.
Drive simple microservicesor prepare use case specific views in a DB of your choice.

Making data self service!

---

## Securing serverless - By breaking in
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/70534)

[Serverless security: the theory](https://www.youtube.com/watch?v=CiyUD_rI8D8)

- Using (vulnerable) external libraries
  - For example when you need to fetch file & store in s3
    - The library constists of 191.155 lines of code
  Usage of the package is only 20 lines of code, initialize the 191.000 code. Where would the risk be?
Serverless does not secure the application securities. It does for the OS securities.

1) Beware of vulnerable libraries
2) ReDos, still is an issue, but does not bring you down but could cost a lot of money
3) Secrets: rotate keys, use key management, don't keep the secrets in code. Use KMS!
4) All the files in the zip are available for all the functions in your Lambda. Create different configurations so you upload smaller functions. Deploy granual functions. Different policies for each Lambda functions
5) Use granular policies. A function is perimeter that needs to be secured
6) Don't rely on imutability. You cannot rely on imutability. The container/function will be probably still be warm. If you have written files to /tmp it could be that these still exists the next time.
7) Worry about all functions. Every function increases your attack surface.

---

## Kubernetes is not for developers
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/72195)

[Slides](https://cdn.oreillystatic.com/en/assets/1/event/276/Kubernetes%20is%20not%20for%20developers%2C%20and%20other%20things%20the%20hype%20never%20told%20you%20Presentation.pdf)

Kubernetes is hard! It is not designed for developers as there are many knobs and settings you can setup. The developers should not worry about that.
- Kubernetes abstractions (knobs) are great!
- Deploy quickly and safely
- Self-healing infra

AppDevs should not care about Kubernetes. The PlatformDevs should take care of this. The PlatformDevs should figure out what the AppDevs want to achieve.

Setup Kubernetes with scripting
- Python/Bash/Ruby
- Very flexible
- Not already done for you
- More difficult to maintain

Setup Kubernetes with Helm
- Provides manages releases of an app
- Basic lifecycle manages
- Has a structured user input system (the value file)
- Currently requires a server side component

CRD + Controller
- Reduces your use of Kubernetes to a single resource type
- Users may still need to interact with kubectl
- Libraries exists to help build controllers
- Coupled to Kubernetes

Custom API
- Provides a minimal, opinionated interface for managing apps
- Users don't have to worry about Kubernetes at all
- HTTP is a good delineation point between services
- Isn't good for non-homogeneous workloads
- Extra knobs can quickly become unwieldy

---

## Building resilient serverless systems
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/70919)

[Slides](https://cdn.oreillystatic.com/en/assets/1/event/276/Building%20resilient%20serverless%20systems%20Presentation.pdf)

Things should be resilient when things are on fire. We should be confident that when everything is on fire, we should not worry
![this is fine](https://www.geek.com/wp-content/uploads/2016/08/this-is-fine-meme-625x350.jpg)

- What happens when those vendor managed services fail?
- Or when the services used by those services fail?

Plan for failure! Mitigate through architecture
- [AWS Reliability pillar](https://d1.awsstatic.com/whitepapers/architecture/AWS-Reliability-Pillar.pdf)

Serverless global is really cost efficient because you don't need the cluster of EC2 running in the different AWS region.

API Gateway in regional mode?

---

## Lessons learned from 300.000 lines code for infrastructure
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/72750)

1. **Checklist**
Reason why it takes long before you have a production grade infrastructure:
- [Yak shaving](https://www.techopedia.com/definition/15511/yak-shaving): a seemingly endless series of small tasks you have to do before you can go the work you need to do.
- The infrastructure checklist is a long list.
- Use the [checklist](gruntwork.io/devops-checklist) to build the production grade infrastructure

2. **Tools**
Tools are great but not enough. Change behaviour of the team also! People need to get used to use the tools and this takes time to get confident with the tools.

3. Everything in one file is harmful. making a change in that one file, you could potentially break everything. Also it is hard to test, understand and reuse the confident.
Split up per environment and per component. So staging/acceptance/production and frontend/vpc/Mysql creates isolation in your code and architecture.
live
  dev
    vpc
    mysql
    frontend
  stage
    vpc
    mysql
    frontend
  product
    vpc
    mysql
    frontend
Build infrastructure from small, composable modules.

4. **Tests**
Infrastructure code without automated tests is broken!
Test strategy:
  1. Deploy real infrastructure
  2. Validate it works
  
Use [Terratest](https://github.com/gruntwork-io/terratest) to test the infrastructure code.
Tests creating and deleting a lot of resources.
Clean up the left over resources in the account: [cloud-nuke](https://github.com/gruntwork-io/cloud-nuke)

5. **Releases**
  1. Go through the checklist.
  2. Write some code.
  3. Validate it works.
  4. Profit!

---

## Everything you wanted to know about monorepos, but were afraid to ask
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/71009)

All repos begin as monorepos
Multi repo is normal

Advantages multi repo
  - Freedom
  - Control
  - Speed
But! What happens when you need to share code? You copy the code to all the repo's.
Check [Abjected oriented programming](http://typicalprogrammer.com/introduction-to-abject-oriented-programming)

Colocating the code
- Create directories like $GOPATH
- Organise by language, logical area, or like go get

Shallow, sparse clone are you friends to retrieve parts of the monorepo
[Watchman](https://facebook.github.io/watchman) to track what files have changed

The law of updates:
- Patch level
  - Easy
- Minor version level
  - possible
- Major version level
  - Nuke it from orbit

Handle large push updates to work as a team

Monorepos does require openness

Graph based build tools
- buck
- pants
- bazel, as preferred graph based build tool 


- If your build tool understood what it is building
- Use build rules to define kinds of output
- Express dependencies between rules
- Actually express how we think about builds

Source organisation != deployment organisation
source organisation != release cadence

---

## Building a distributed real time stream processing system
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/70994)

[Slides](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/70994)

Filter/Match the events from the original streams
Aggregate over the matched events from the filtered stream 
Gather all the events and sort the events in timeboxes in the correct order. At some point publish the result. 
- Late data
  - Publish once, drop late events 
  - Publish updates, replace previous result, keep published aggregate in memory 
  - Publish delta, publish new result as a delta

Scaling a streaming system is all about partitioning workload effectively

Build your monitoring as you build your system

Must have dashboards: 
- Data lag 
- Data ownership
- System throughput
- SLIs 

---

## Architecting for TV
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/71054)

[BBC Tal](https://github.com/bbc/tal)

When possible all the data should go trough one API endpoint for various applications. 
Static data when the data is finished. For example the sports score will not change after the 90 minutes of the soccer match. So this will be statically hosted

--- 

## Victims of our own success
[Conference link](https://conferences.oreilly.com/velocity/vl-eu/public/schedule/detail/70529)

First application was build in Delphi
On premise
Nightmare to maintain 
Connected to a backend to send sms

Cloud backend
- Started from scratch
- Continued maintaining old project 
- Java backend, MySQL and Websockets 

New UI was build
Took 2 years to migrate all the old customers to the new platform. The customers needed to be ported from the on premise to the cloud solution

But keep moving forward, despite the server issues 
- Worries about scaling to early and it was slowing them down
- We took tech shortcuts, never paid back tech debt

Servers were even in the cloud treated as pets instead of cattles

Started moving slow, customers unhappy and deploying was painful still and manual

Needed to change the mindset on how to proceed. 

Goals after the stop/step back
- Shipping confidence! 

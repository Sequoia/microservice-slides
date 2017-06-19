class: title
# Practical Microservices

## Tools & Techniques

### Sequoia McDowell

.small-list[
* 🌐  https://sequoia.makes.software
* 🦆  @_sequoia
* 📧  sequoia.mcdowell@gmail.com
]

???

#### Welcome!

* Practical microservices tools & techniques

---

# Goals of this Training

???

By the end of this training you should have...
--

* Core Microservices Concepts
???
#### Introduce core concepts of microservices:
* What they are
* When to use them
* Different architectures
* Pros & Cons & Pitfalls
--

* Familiarity with Providers & Tools
???
#### Providers and tools
* Platforms: AWS, Now.sh, Google Cloud, Bluemix Etc.
* Tools: API building tools, deployment tools, infra management & scaling
--

* **Hands on Experience**
???
#### Hands on
* The bulk of time will be spent on exercises
* Most exercises follow a narrative: decomposing our ebooks store into microservices!

---

# Components of This Training

1.  Lecture
2.  Exercises

???

#### Lecture

I'll explain some concepts as we go, but as much as possible we'll focus on hands-on exercises

#### Exercises

Exercises can be found in the following repository...

--

## Code

<https://github.com/Sequoia/microservices-code-along/>

--

## Notes

<http://bit.ly/msv-notes>

???

* Google doc I'll update as needed with links etc.
* *Demo stepping through code & Show on github*

---

# Who Am I

???

* My name; Go around and introduce ourselves in a moment

#### Background

* Web person to Programmer
* Doing javascript for years, mostly backend these days
* Marketing, Software company, PHP, Java, JavaScript
* Contracting and educating past two years, seen lots of tools & platforms
* Educating is my passion so I have to keep up w/rapidly changing field
* Dabbling in microservices lately & put together this training to share some of what I've learned

--

# Who are you

--

* Name, Role
* Experience with Microservices and Node
* What brought you here today?

???

#### Introductions

* Name, Background w/msv & node, what do you hope to get from training

???

---

# Agenda: Day 1

* Introduction
* Microservice Concepts
* Now.sh
* LoopBack & Bluemix
* Handling Sessions
* Cloud Functions & AWS Lambda
* Infrastructure as Code

---

# Agenda: Day 2

* The "Serverless" Framework
* Using JSON Web Tokens
* Containers & Docker
* Docker Compose
* Proxying with Nginx
* Kubernetes
* Deploying to Google Cloud

???

* A lot to go over!!
* Let's jump in with an introduction to microservices

---

class:title

# Microservices

--

* What is a microservice?
--

* What *isn't* a microservice?

--
* What problem do they solve?

--
* ...Docker?

???
Let's step back and look at where microservices came from...

What's not a microservice? Monolith

---

# Architectures: Monolith

--
* One application

???
Code lives in one repository or is assembled into one artifact
--
* One application server (usually)

???
* In the simplest case.
* There might be a separate database server or filesystem
* (Possibly multiple instances with a load balancer or shared session store but **conceptually it all runs as one unit**)
--
* **One "unit"**
???
All aspects of the application, authentication, webpage rendering, database connection, HTTP request handling, run as one unit.

---

# Monolith: Advantages
--

* Easy to write features
???
* With all code in the same place, it's very simple to get started, very little overhead.
* Less organizing different repositories, less assembling things together etc.
--

* Simple to deploy (maybe)
???
* Stand up a server (LAMP e.g.), run mysql, ftp code to it
* If you need a tool or library, install it on that box
* (often fragile, but we'll get to that later)
--

* Simpler authentication models
???
* Sessions can be managed in memory or on local FS
* Data and/or auth DBs can be connected to locally
--

* Fewer things that can fail
???
* Logs exist on one server, with one application it's easy to know where to start looking for problems

---

# Monolith: Disadvantages
--

* Scaling can be a challenge
???
* Can't scale cpu intensive process without scaling whole server
* Another server = another full stack
--

* Easy to make a mess
???
* Missing a library or dep? Install on the server!
* Without delineating boundaries between entities (products, orders & users), a query in a products controller can span your whole database. This can cause a mess where it's impossible to edit the database or remove a function becuase someone somewhere relies on it
--

* Difficult to use multiple languages
???
* If you have a java app + web server, you're not likely to integrate node.js or python into it even if those are a better choice for a particular feature or you have devs that know that language
--

* Difficult to update/replatform
???
* Eventually your application stack will become defunct. If it's all in one big app you spent 10 years writing with 50 people, a rewrite can be a nonstarter.
* With smaller services with clearly defined API contracts, you can rewrite parts of your application without changing the others & gradually update

---

# Architecture: Microservices

--

* Many smaller applications
???
* One application per business area/entity (or something else!)
* Web server, products API, asset management service
* Tied together (somehow)
--

* Services run in isolation
???
* Commonly, a service runs in its own container
--

* Federated data stores
???
* Two different services typically *do not* share a database
--

* Services reveal limited API interfaces
???
* HTTP, message queuing, or something else
* Products service doesn't allow another service to query database directly
--

* Stateless (typically)
???
* Instance does not rely on local FS (for assets) or local database
* Necessary for scaling

---

# Microservices: Advantages

--

* Easier to organize code (within a repository)
???
* Auth service *only handles auth*
* Easier to work with a codebase that does one thing
* Code should not swell to 5k files and if it does you can split
* One team fully in charge of a service, less collisions
--

* Easier to replatform
???
* Smaller code bases, clear contracts
* rewrite Inventory service in another framework or lang, as long as it implements the same APIs
--

* Easier to scale
???
* Scale one service, not the other
* Lack of state & containerization makes it easy to spin up or down instances 
--

* Allows for multiple languages in an application
???
* Data team uses python, web team uses node, communicate via APIs
--

* More clearly defined
???
* If one service does one thing with dependencies X, Y, Z per container config, it's hard to miss a dependency
* If API contracts are clear, it's less likely to break someone elses code
---

# Microservices: Disadvantages

--

* More things that can break
???
* Application error log vs. 20 error logs across 20 servers
* Oh and the automated deployment itself can break!
--

* More tooling
???
* "Deploy 10 services with the push of a button" but how many weeks did it take you to configure that button?
--

* More challenging to organize code *across* repositories
???
--

* More **Complexity**
???
* **Poll**: What's more complex: large system with 100 features or 10 systems with 10 features each?
* Each service is simple but the contracts in between, encoding infra etc. all comes at a cost

---

![We replaced our monolith with micro services so that every outage could be more like a murder mystery.](img/murder.png)
*https://twitter.com/honest_update/status/651897353889259520*

---

# Getting Started Considerations

* Start with a monolith
???
* Don't start with microservices
* Monoliths are much easier to get up and running and to iterate on initially.
* Starting with a msv arch will add unnecessary overhead for young projects
* Build, iterate, learn what you need & decompose later
--

* Start with just one microservice
???
* Don't attempt to break everything up all at once
* Move slowly, decompose bit by bit
--

* Ensure MSV arch is worth the cost
???
* There *is* a cost associated: expertise & configuration
* "Is there a cheaper/easier way?"
--

* "You're not google"
???
* A lot of tools are built to solve problems that 95% of us never encounter
--

* Everything's a tradeoff
???
* applies everywhere
* No silver bullets

*Let's look at some patterns/archs for msvs*

---

# MSV Architectures: Cloud Providers

* AWS (Amazon)
* Google Cloud
* Azure (Microsoft)
* Bluemix (IBM)

???

* Provide solutions for everything
* Write code (or configure screens) for *that* provider
* API gateways, databases, file storage, etc.
* Tight interoperation
* High lockin
* *Can* Be intermixed

---

# MSV Architectures: Cloud Functions

* AWS Lambda
* Azure Functions
* IBM OpenWhisk
* Google Cloud Functions

---

# MSV Architectures: Cloud Functions

???

* HTTP request, SNS, object added to bucket, run on schedule
* not specifically for HTTP
* Some composible, notably OpenWhisk which is build for composing pipelines
* Limited in functionality: can't stream files from a lambda e.g.

--

* Respond to "triggers"
--

* Turn on, run operation, turn off
--

* "Scale" automatically
--

* Pay for what you use
--

* Stateless

---

# MSV Architectures: Containers

???
* very distinct from cloud providers like using the AWS stack
* Use your own stack & deploy it as a single unit containing all executables & portions of filesystem you need

--

* Not "required", but typically used
--

* Describe the infrastructure your code needs
--

* Reproduce identical execution environments
--

* Docker & More

???

* You thought all you had to do was dockerize your app? Ha! Not so fast! XD
* Still more complexity to come

---

# MSV Concept: Statelessness

* State & persistence live outside the application
???
Databases, sessions, data files
--

* Allows 1 instance to be scaled to 10

--
* Allows instances to be "thrown away" or replaced

???

Let's get started with some exercises!

**STOP**

**NOW.SH**

---

# Now.sh

--

* Application Hosting Platform
--

* CLI Deployment tool
--

* Reverse proxy/alias service
--

* Hosts static sites, Node apps & Docker
--

* Scaling, DNS, domain reseller, & more

---

# Now.sh
???
things to know
--

* Servers are strictly stateless
???
* No filesystem (besides tmp)
* Designed for throw-away, replacible services
* Don't try to run a DB on one of these...
--

* Only HTTP
???
* I tried to run a redis server on it, it doesn't expose tcp directly
--

* **Excellent DX**
???
* Developer Experience
* Waaay easier to use than most cloud providers
--

* A bit opaque
???
* How are secrets managed?
* What physical infra does my code run on?
--

* New
???
They say it's production ready but it's still an unproven service.

--
* Very easy and fun to work with!


---

class: title

# LoopBack

???

* Tool to make it quick & easy to stand up a rest API for a database & tie DBs together
* Automates as much as possible
* Not 100% dashboards, not 100% code

--

* API building framework
--

* Built on Express
--

* Owned by IBM
--

* Integrated (sort of) with IBM Bluemix

???
* This is in process and a bit of a mess.

---

# LoopBack Concepts

--

* Models
???
* Data entities defined as models in code 
* Models can interrelate
* Automatically create API CRUD endpoints
* **Decoupled from datasource**
--

* Datasources
???
* Datasource backs a model
* Can be swapped out (sort of)
* **Allow models from different sources to interconnect**
* One model doesn't know or care what another model's datasource is
--

* "Declarative APIs"
???
* Don't write routes, just describe what your data looks like, who can access it etc.
* Framework writes routes for you

---
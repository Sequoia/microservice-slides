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

* Relations
???
* Create connections between models
* Typical hasOne, hasMany, belongsTo etc.
--

* Datasources
???
* Datasource backs a model
* Can be swapped out (sort of)
* **Allow models from different sources to interconnect**
* One model doesn't know or care what another model's datasource is
--

* Model Discovery
???
Generate models from database schemas
--

* "Declarative APIs"
???
* Don't write routes, just describe what your data looks like, who can access it etc.
* Framework writes routes for you

**END LOOPBACK**

---
class: title

# Authentication, Sessions, and Tokens

--

## Authentication

* Who are you?
* Can you prove it?

--

## Sessions/Tokens

* Keeping a user authenticated

---

# Session/Token Strategies

???
Depends on the type of access needed

--

* Browser?
--
<br>&rarr; Cookies+Sessions
???
* A "session" is a record on the server/database that bearer of a certain token is authenticated as a certain user
* Cookies are built in to browsers
* Strong security when used properly
* Stateful, invalidatable
--

* Programatic API Calls?
--
<br>&rarr; API Keys
???
* Invalidatable: (2 part key you can cancel the part the server has)
* Developer downloads a token "API Secret" and stores is somewhere secure
* ssh keys are a version of this
--

* Communicating Verifiable Claims?
--
<br>&rarr;JSON Web Tokens
???
* Set of claims, roles, permissions, user ids, etc.
* Services share a secret to sign the claims & verify them elsewhere
* Useful for short-term API call permissions
* "Server X said I have permissions for Y, here's my proof"
* **NOT INVALIDATABLE** | **NOT A REPLACEMENT FOR SESSIONS**
* "I can get around x-domain cookie sharing by storing these in localstorage!" &larr; NO!
* You can shoot yourself in the foot trying to circumvent browser security features

---

# Cookies + Sessions Challenges

--

* Sharing cookies across domains
???
* Browsers will only send cookies to the domain that set them
* Subdomains can share cookies
* Putting all services under one domain solves this issue
--

* Sharing sessions across instances
???
* Every service that wants to verify & look up session info for a cookie must have access to same session store
* Alternately, services can share a session secret and info can be serialized in the cookie itself
--

* Limited cookie size
???
* 4096 bytes
* Store info in session, just put an identifier on the cookie itself

**NOW AUTH EXERCISE**

---

class: title
# Infrastructure as Code

"Declarative Infrastructure"

--

* Encode infrastructure requirements in a configuration file
???
* For cloud providers
* Define what your app/service needs: disk storage, a database, an HTTP gateway, etc.
--

* Track infra requirements with code
--

* Deploy infrastructure with a command
--

* Tear down with a command
--

* Does *not* handle application code/configuration
???
* other tools such as ansible etc. deal with this (configuration management)
* Fits into "cloud provider" model, not "container" model

---

# Terraform

* Multiple platforms (AWS, Google Cloud, Cloudflare, Digital Ocean, many more)
* Goes beyond hosting: loggers, github, message queues etc.

--

# CloudFormation

* Specific to AWS
* Tailored towards AWS services

---

class: title
# Serverless

--

* Mix of infra management & configuration management
--

* Specifically for cloud functions
--

* Sets up triggers, e.g. HTTP gateways as well
--

* Multiple platforms
--

---

# What is Serverless?

--

* Breaks applications into functions
--

* Deploys them separately but under one API Gateway
--

* Allows versioning and rollback
--

* Run functions locally
--

* Wire together all necessary resources, permissions, etc.

???

**STOP**

**Serverless hello world**

https://serverless.com/framework/docs/providers/aws/examples/hello-world/node/

---

# Setting up Serverless

1.  Create a an IAM user
2.  Copy key & secret
3.  Set credentials in shell

---
class:img

<https://console.aws.amazon.com/iam/home>

![](img/IAM.png)

???

1. Dashboard
2. IAM
3. Click users

---
class:img

![](img/IAM_Users.png)

???

1. Add user

---
class:img

![](img/IAM_name_access.png)

???

1. Name: serverless admin
2. Access Type: Programmatic (api, not a user/pass login)

---
class:img

![](img/IAM_attach_policy.png)

???

1. Attach policies directly
2. Administrator access


---
class:img

![](img/IAM_User_key.png)

???

1. Copy keys

---

# Configure Serverless to Use Keys

```no-highlight
$ sls configure credentials --provider aws --key KEY --secret SECRET
```

--

```no-highlight
Serverless: Setting up AWS...
Serverless: Saving your AWS profile in "~/.aws/credentials"...
Serverless: Failed! ~/.aws/credentials exists and already has a "default" profile.
```

???

If you see this, you already have a default AWS user

--

```no-highlight
$ cat ~/.aws/credentials

[default]
aws_access_key_id=AKIAITI...
aws_secret_access_key=D9T...
```

???

* Edit file to change name of default profile to something else
* (or append `--profile serverless` but you'll need to recall that later)

---

# Serverless Hello World

```no-highlight
$ sls create --template aws-nodejs --path myService
$ cd myService
$ serverless deploy
$ serverless invoke --function hello
```

???

Other useful commands:

--

`$ sls deploy list functions`

--

`$ sls info`

--

No http endpoint!

---

# Add an Endpoint

```yaml
functions:
    hello:
    handler: handler.hello
    events:
      - http:
          path: /hello
          method: get
```

---

# Deploy again

```no-highlight
$ sls deploy

Serverless: Packaging service...
...
Serverless: Checking Stack update progress...
........................
Serverless: Stack update finished...
Service Information
service: myService
stage: dev
region: us-east-1
api keys:
  None
endpoints:
  GET - https://jyi4ni4p92.execute-api.us-east-1.amazonaws.com/dev/hello
functions:
  hello: myService-dev-hello
```

--

`$ curl https://jyi4ni4p92.execute-api.us-east-1.amazonaws.com/dev/hello`

???

* Look at lambdas
* Look at API Gateway

---

# Lambda Dashboard

![](img/Lambda_Dashboard.png)

---

# API Gateway Dashboard

![](img/APIG_Dashboard.png)

---

# Tear down

`$ sls remove`

???

&rarr; Some other things SLS can do

---

# Create Resources

```yaml
resources:
  Resources:
    NewResource:
      Type: AWS::S3::Bucket
      Properties:
        BucketName: my-new-bucket
```

---

# Grant Permissions to Resources

```yaml
provider:
  iamRoleStatements:
  - Effect: Allow
    Action:
      - s3:GetObject
    Resource: "arn:aws:s3:::books-bucket/*"
```

???

And more!

**STOP**
**SLS LAMBDA STUFF**

---

# JSONWebTokens

* Based on Web Standard (RFC7519)
* Used to securely communicate JSON objects
* Consists of header, payload, and signature
* Self-contained
* "More flexibility" than (browser-only) cookies
* https://tools.ietf.org/html/rfc7519

???

Standard is pretty accessible

---

# More on JWTs

* JWTs are not signed/encrypted themselves
  * JSON Web Signatures (JWS) used for signing
  * JSON Web Encryption (JWE) used for encrypting
* Can be used with public/private keypairs also
* Can also be used for parallel/multiple signers (see JWS-JS)

???

* We're using JWTs with JWS

---

> It is important to understand that the purpose of using JWT is NOT to hide or obscure data in any way. The reason why JWT are used is to prove that the sent data was actually created by an authentic source.

\- [@mikey_s_e](https://medium.com/vandium-software/5-easy-steps-to-understanding-json-web-tokens-jwt-1164c0adfcec)

---

# Parts of a JWT

* Header
* Payload
* Signature

--

`header.payload.signature`

---

```no-highlight
eyJhbGciOiJIUzI1NiJ9
.eyJ1c2VyIjp7ImlkIjoiOTM0ODc1bGFrc2RqZiIsIm5hbWUiOiJTZXF1b2lhIiwicm9sZSI6InNlcmYifSwicGVybWlzc2lvbnMiOnsiYm9va3MiOlsiYWRkIiwiZWRpdCIsImRlbGV0ZSIsInZpZXciXSwidXNlcnMiOlsidmlldyJdfX0
.mgdWdWNPgD5bm1fiC99vlNc65KmsH5kLkEuIJhXIf0U
```

--

```no-highlight
base64(header)
.base64(payload)
.HMACSHA256( base64(header) . base64(footer) , secret
```

---

# Header ("JOSE" header)

*JOSE = JavaScript Object Signing & Encryption*


* (Usually) two keys:
* `typ`: Type of claim (JWT, JWA, JWE)
* `alg`

--

```js
{
  "typ" : "JWT",
  "alg": "HS256"
}
```

---

# Payload

.small-list[
* **public claims**: user-defined attributes

* **reserved claims**: defined by standard
  * `iss`: issuer
  * `sub`: subject
  * `aud`: audience
  * `exp`: expiration time
  * `nbf`: not before
  * `iat`: Issued at
]

---

# Arguments against using JWTs to manage sessions

* http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/
* http://cryto.net/~joepie91/blog/2016/06/19/stop-using-jwt-for-sessions-part-2-why-your-solution-doesnt-work/ 

???

**SHOW JWT DEMO**

**STOP**

**DO SLS Exercises**

---

# Encryption keys

* KMS
* Create keys
* Grant resources access to those keys
* Keep data encrypted locally

---

class:img

![](img/IAM_KMS_start.png)

???

1. IAM dashboard

---

class:img

![](img/IAM_KMS_region.png)

???

Select same region as your sls function!!

---

class:img

![](img/IAM_KMS_alias.png)


---

class:img

![](img/IAM_KMS_tags.png)

???

tag so you can find it later

---

class:img

![](img/IAM_KMS_admin.png)

???

Might not be necessary...

---

class:img

![](img/IAM_KMS_use.png)

---

class:img

![](img/IAM_KMS_get_key.png)

???

Needed for SLS

---

# Encrypt some Secrets

`$ npm i -S serverless-kms-secrets`

--

```yaml
plugins:
  - serverless-kms-secrets

# Provide access to the secrets file to sls config
#   key ARN needed to grant to functions
#   values needed to pass to functions to decrypt
custom:
  kmsSecrets: ${file(kms-secrets.${opt:stage, self:provider.stage}.${opt:region, self:provider.region}.yml)}
```

???
add plugin to SLS config

--

```no-highlight
$ sls encrypt -n JWT_SECRET \
 -v "I've got a lovely bunch of coconuts" \
 -k 6681f426-81eb-49eb-aae5-b785d6d87870
```

---

# Encrypted Secrets

```no-highlight
$ cat kms-secrets.<STAGE>.<REGION>.yml
secrets:
    JWT_SECRET: AQICAHhLbOgGFtsGLmYKC0Ft/zCDCQ4RwsYjTBbLXkK1a7th2gH+UAfXAP0ZQ+0sBu3KpEdGAAAAgTB/BgkqhkiG9w0BBwagcjBwAgEAMGsGCSqGSIb3DQEHATAeBglghkgBZQMEAS4wEQQMXCzK378fl4qjLk+JAgEQgD4TwdWRcTF3oza8eI35GMJDsZ+/ciV6RHHyWoniKvUkYFWj81CyTqxt7Pirj2/lblXg4xvzvy8NVmpmvd6Zrg==
keyArn: 'arn:aws:kms:<REGION>:700002748008:key/6681f426-81eb-49eb-aae5-b785d6d87870'
```

???

* Safe to check in
* Make sure regions match!!

---

class: title

# Docker

--

* "Container Engine"
???
* Application bundles with all binaries & FS resources needed to run
* Typically just one service (not e.g. webserver + redis)
* Contrast with VMs
* Share kernel
--

* "Layers"
???
* Each setup command creates a new layer
* Container can be used as startpoint (layer) for another container
--

* Shared Kernel
???
* Fast to start
* More shared resources
https://www.docker.com/what-container

---

# Docker Development Workflow

--

1. Create dockerfile
???
* Base it on another docker container like linux or node or java or redis
--

1. Add commands to configure your application
???
* Copy files
* Run install commands
--

1. Add command to *start* your application
--

1. `docker build`
???
* Run all the commands to build an "image" artifact in local docker machine
--

1. `docker push`
???
* push the image to Docker hug or another registry

---

# Advantages of Containers

--

* Identical execution environments
--

* Lighter weight than VMs
--

* Fast to set up and tear down
--

* Run the same on dev & prod
--

???

**STOP**

**Dockerize demo**

---

class: title

# Kubernetes

???

* Docker doesn't describe *how* containers should run
* Container orchestration 
* Declarative
* Describe your services and how many should run etc.
* Host on GKE or elsewhere

---

# Kubernetes Concepts

--

* Nodes
???
* Node: VMs that run your kubernetes cluster
--

* Cluster
???
* Cluster: A group of Kubernetes nodes
--

* Pods
???
* Pods: What a container runs in (deployed to a cluster)
--

* Deployment
???
* Deployment: Describes how pods should be scheduled, scales them up and down
--

* Services
???
* Describe how pods should be exposed

---

# Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
    role: loadbalancer
spec:
  containers:
  - name: nginx
    image: nginx
```

???
* labels
* image
* Normally don't create these directly

---

# Deployment

```yaml
apiVersion: v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  template:
    metadata:
    name: nginx
    labels:
        env: test
        role: loadbalancer
    spec:
    containers:
    - name: nginx
        image: nginx
```

???
* Create these to manage your deployment
* This creates a ReplicaSet which creates your pods
* **Work at the highest level of abstraction**

---

# Service: `ClusterIP`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp
  labels:
    name: webapp
    stack: books
spec:
  selector:
    name: webapp
  ports:
   - port: 80
     targetPort: 80
     protocol: TCP
```

???
* Default type: ClusterIP
* No external networking
* Name gets used by kube-dns for internal routing

---

# Service: `NodePort`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: reverseproxy
  labels:
    name: reverseproxy
    stack: books
spec:
  type: NodePort
  selector:
    name: reverseproxy
  ports:
   - port: 80
     targetPort: 8080
     protocol: TCP
```

???
* Use this to expose a port on your node, externally

---

# Service: `LoadBalancer`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: reverseproxy
  labels:
    name: reverseproxy
    stack: books
spec:
  type: LoadBalancer
  selector:
    name: reverseproxy
  ports:
   - port: 80
     targetPort: 8080
     protocol: TCP
```

???
* Use these to expose on cloud provider like GKE

---

# Labels and Selectors

--

## Service has **selectors**

```yaml
...
spec:
  type: LoadBalancer
  selector:
    name: reverseproxy
...
```

--

## Deployment has **labels**

```yaml
...
spec:
  template:
    metadata:
      name: reverseproxy
      labels: # arbitrary key/value pairs (metadata)
        name: reverseproxy
        stack: books
        role: external-facing
...
```

???
* Selectors find resources with matching labels
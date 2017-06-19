TODO:

---

# Now.sh

* [ ] Who is Zeit
* [ ] Other products/libraries
* [ ] What is `now.sh`
    * [ ] Deployment Platform
    * [ ] CLI + Desktop Tool
* [ ] Abstraction
    * [ ] They manage the "cloud" resources (AWS, load balancing, scaling etc.)
* [ ] Deployment types
    * [ ] Static
    * [ ] Node
    * [ ] Docker

## Aliases

## Environment + Secrets

---

# MBAAS/API Gateway/Hosted API

* [ ] What are they
* [ ] Types/Offerings
    *   API Router Framework (code, you do the rest)
    *   All-in-one with own type of database
    *   Gateway to existing databases
    *   "API Gateway" (loose name) Proxy in front of API(s) that manage auth throttling etc.
* [ ] Swagger & OpenAPI
* [ ] LoopBack: a mix
    * [ ] Layer over Express
    * [ ] "Models"
    * [ ] "Datasources"
    * [ ] Deployment platform

---

# Authentication & Session Management

* [ ] Definitions: what do these terms mean
* [ ] Use-cases:
    * [ ] Programmatic access & API tokens
    * [ ] Browser
    * [ ] Stateless
* [ ] Approaches: Sessions
    * [ ] Per-server local session
    * [ ] Load balancing: sticky sessions
    * [ ] External session stores
    * [ ] "Stateless"
* [ ] Browser Tokens:
    * [ ] Cookies
    * [ ] Other options
* [ ] JWT
    * [ ] What is it
    * [ ] How to use it
        * [ ] Header
        * [ ] Any other way
    * [ ] What's it for
        * [ ] Verifying claims statelessly
        * [ ] Temporary or one-time permissions on another service
    * [ ] What's it not for/what isn't it
        * [ ] NOT a session replacement
        * [ ] NOT a cookie replacement
    * [ ] Structure

---

# Cloud Functions

* [ ] A class of "cloud service"
* [ ] Offerings: 
    * [ ] Azure Functions
    * [ ] Lambda
    * [ ] OpenWhisk
* [ ] Uses/benefits
    * [ ] As-needed CPU
    * [ ] Pay only what you use
    * [ ] Platform handles scaling
    * [ ] Respond to various events
        * File systems/S3
        * Messaging service
        * Queue processing
        * **HTTP Requests**
* [ ] Limitations for web
    * [ ] Not specifically built for web (http not automatic)
    * [ ] Startup time 
    * [ ] Statelessness
    * [ ] Pure functions, don't handle streams etc. (typically)

---

# Infra as code

* [ ] What is it
    * [ ] Infrastructure management
* [ ] Offerings:
    * [ ] CloudFormation: AWS Specific
    * [ ] Terraform: Multiple cloud platforms
        * (You always have to get somewhat specific)
* [ ] Vs. Chef, Puppet, Ansible etc.
    * [ ] ---> Configuration management tools: "Work with infra that already exists"
    * [ ] Terraform & CF are focused on *creating* the infrastructure you can then deploy to
* [ ] Why use it
    * [ ] Provision large sets of infra
    * [ ] Tear down quicly
    * [ ] Track infra in code repo with code

---

# Serverless

* [ ] What is it
    * [ ] Part Infra management, part configuration/code management
    * [ ] Specifically for creating cloud functions
    * [ ] Primarily for Web Servers
* [ ] What's it do
    * [ ] Core use-case: Breaking applications out into individual cloud functions, deploying them to a cloud function provider, and exposing them as a website or API
    * [ ] Put your routes in separate functions
    * [ ] Provision whatever extra resources functions need
    * [ ] Wire permissions together
    * [ ] Version & Rollback
    * [ ] Teardown
    * [ ] Test things locally

---

# Title slide

## Subsection

### Inner point
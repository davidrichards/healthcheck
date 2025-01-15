# Healthcheck

Task

Create an app which does health checks in intervals (e.g. every minute). The status of a service is determined by the HTTP response code. A HTTP status code of 200 means the service is healthy and a HTTP status code of 500 means the status is unhealthy. For this exercise a service will be unhealthy 10% of the time (random). Each service is identified by a UUID. For simplicity the health check endpoint will return it's own UUID (see curl example).
You can use any programming language you like and are free to use any resource available to you.


tl;dr;

- Observe 1M services each minute
- 10% of the requests will fail

URL https://go-serv-interview-4a0642b972b8.herokuapp.com/$uuid

Example

$ curl https://go-serv-interview-4a0642b972b8.herokuapp.com/4BD01067-A441-47ED-9487-AAACA73CB7F4
{"ID":"4BD01067-A441-47ED-9487-AAACA73CB7F4"}

## System Design

![healthcheck_sequence_design_1](./diagrams/healthcheck_sequence_design_1.svg)

### Dependency Choices

- Platform vs Leaner Tools
- Event Bus
- Event Handling
- Bundling Service
- Serialization
- Workers
- State Durability

#### Platform vs Leaner Tools Choice

I decided to use a platform instead of a lean project because of the team structure and advice by Johnny regarding the quality of code required in production. Done well, this tool can extend to gathering and aggretating almost any sort of metric monitoring.

While throwaway prototypes are fast to develop, I bought myself some time by structuring this step into a sprint. Calendar time was mostly required because of my anxiety levels and work load. Taking small steps in a well-understood platform produces higher quality code with more opportunities for early refactoring.

Additionally, platforms offer orders of magnitude greater testing surface area. A large ecosystem using a platform for mission critical tools is going to produce much safer solutions. As long as the extra overhead used by a platform can scale horizontally, the choice should be sound.

Choosing Rails vs Phoenix comes down to:

- Concurrency design
- Current team capacity
- Recruitment and retention

A design that can take advantage of lightweight processors is better for designing highly concurrent applications. The Rails design I am currently persuing creates a bundling service that is much more complex and error prone than an event sourced solution depending on GenServer and commanded. This solution relies on ubiquitous and well-understood patterns.

The current team knows Rails and is developing its capacity to deliver reliably good software. From conversations with the team, the priority is likely to reinforce this and choose to do future projects with other toolsets when the risk is more clear follows a small-bets philosophy. Deploying working systems early and often will always outperform audacious / large moves unless the larger moves are necessary. Also, we are more likely to achieve industry leadership with bankable wins and we are less likely to lose our current position by deploying reliable software often.

I have been convinced that recruitment is less of a concern than I thought at the beginning of this project. Johnny has the reputation and reach to bring many highly qualified candidates to the recruitment process. Happy developers using tools that empower them to develop their skillsets is important for retention. Choosing Phoenix for this reason could create some small gains.

In the end, code is conversation. Any design requires at least two paper prototypes. In our case, a Rails prototype gives us most of the advantages we are seeking. If a BEAM-based solution can justify the risks, we are most likely to discover this with the open source development ongoing in the rabbit. That solution uses event sourced architectures and will be implementing an Elixir-based solution for its monitoring and measuring tools. That solution is much more complex than managing server health metrics and will give us a low-cost switching opportunity when the operational data is available.

#### Event Bus Choice

I am assuming Kinesis is a good choice for the event bus because the team already has this event bus and I've used it on other projects. Alternatives include:

- Kafka (Amazon MSK)
- Amazon SQS with SNS
- AWS EventBridge

TODO: compare each to Kinesis wrt CAP-theorem tradeoffs, time to deployment, and complexity.

#### Event Handling Choice

I am assuming I would like to use background jobs. This leads us to deciding between Sidekiq and DelayedJob.

## Getting Started

This project uses `.ruby-version` to autoswitch rubies. To install a ruby with chruby:

> chruby ruby-3.4.1


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

<!--
@startuml healthcheck_system_design_1

title System Sequence Diagram

actor User as U
participant RailsApp as RA
participant S3
participant FlatBuffers as FB
participant Kinesis
participant Worker as W

U -> RA: POST /services/refresh
RA -> S3: Fetch services list
S3 --> RA: Return services list
RA -> FB: Serialize services list to FlatBuffer
FB --> RA: FlatBuffer created
RA -> Kinesis: Publish FlatBuffer to event stream
Kinesis --> RA: Acknowledgment

... Worker Logic ...
RA -> W: Start worker with FlatBuffer pointer and TTL
loop Until TTL expires
    W -> FB: Read assigned span of services
    FB --> W: Return span data
    W -> RA: Report service status (healthy/failed/unchecked)
    W -> Kinesis: Dispatch bundling results
end
W -> RA: Final status and bundling summary

@enduml

-->

![healthcheck_system_design_1](healthcheck_system_design_1.svg)

## Getting Started

This project uses `.ruby-version` to autoswitch rubies. To install a ruby with chruby:

> chruby ruby-3.4.1


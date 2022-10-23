---
title: "CH1 Cloud, Spring in SMIA"
date: "2022-09-03T22:20:10.284Z"
template: "post"
draft: false
slug: "smia-ch1"
category: "Development"
tags:
  - "Microservices"
  - "SMIA"
  - "Java"
description: "마이크로서비스 소개, cloud-based 애플리케이션 설계 접근"
socialImage: ""
---

### Considerations

- 적정크기
- 위치 투명성: 서비스 인스턴트 물리적 위치
- 회복성: 서비스에 문제가 있을때 클라이언트가 빨리 실패하게 하는 방법
- 반복성: 새로운 서비스 인스턴스가 시작될 때 기존 인스턴스와 동일한 코드와 구성 유지 방법
- 확장성: 의존성 최소화, 신속한 확장 방법

### Core microservice development pattern

- Service granularity: decomposing a business domain
- Communication protocols:
- Interface design: structure service URLs to communicate service, versioning
- Configuration management: different environment in cloud
- Event processing: decouple microservices using events

### Routing patterns

client’s request to a specific instance of a service

- Service discovery: abstracts the physical location of the service from the client
- Service routing: provide a single entry point(logical URL) for all of services to talk to and acts as a policy enforcement point for authuorization, authentication, and content  checking

### Client resiliency patterns

- Client-side load balancing: **cache the location** of your service instances on the service client, calls to multiple instances of microservices are **load balanced to all the health instances**
- Circuit breaker pattern: prevent a client from continuing to call service failing → fail fast
- Fallback pattern: when a service fails, plug-in mechanism is provided
- Bulkhead pattern: segregate different service calls on a client to make sure one misbehaving service does not take up all the resources

### Security patterns

### Logging and tracing patterns

microservice is more difficult to debug and trace

- Log aggregation: collects all of the logs from all the services instances
- Log correlation: all service log entries have a correlation ID that ties the log entry to a single transaction
- Microservice transaction tracing: query the log data to find individual transactions which is able to visualize the flow.

### Build/Deployment patterns

- Build and deployment pipeline
- Infrastructure as code: provisioning of services as code(managed under source control e.g. dockerfile)
- Immutable servers: the entire container or image is started with environment-specific variables
- Phoenix servers: the running server is recreated off an immutable image → decreases the change of configuration drift


![ch1-smia-patterns](/media/ch1-smia-patterns.jpg)
![ch1-smia-patterns2](/media/ch1-smia-patterns2.jpg)





### Reference
https://www.manning.com/books/spring-microservices-in-action
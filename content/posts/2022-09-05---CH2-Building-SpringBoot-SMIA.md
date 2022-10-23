---
title: "CH2 Building Springboot in SMIA"
date: "2022-09-03T22:20:10.284Z"
template: "post"
draft: false
slug: "smia-ch2"
category: "Development"
tags:
  - "Microservices"
  - "SMIA"
  - "Java"
description: "REST-based microservice"
socialImage: ""
---

### Microservice fit into a cloud architecture

- A large and diverse user base: features to be delivered quickly
- Extremely high uptime requirements: easily isolate faults and problems to specific parts of an application without taking down the entire application
- Uneven volume requirements: components can be deployed independently of one another

### Identifying and decomposing a business problem into microservice candidates → with Data model

- Describe the business problem, and listen to the nouns you’re using to describe the problem
(contracts, licenses and assets)
- Pay attention to the verbs (looks, updates)
- Look for data cohesion: Microservices should completely own their data

### self-contained unit

해당영역을 소유하는 서비스만 그 영역의 데이터베이스에 액세스 할 수 있음

1. 모든 것을 마이크로서비스로 만들지 않는다 → 마이크로서비스는 비지니스로직의 표현
2. 서비스간 교류방식에 집중
3. 서비스의 크기와 책임이 늘어나면 새 서비스들의 기능을 캡슐화 한다

### Conversation in Services: Service Interface

서비스 인터페이스 설계 지침

1. REST 철학 수용: HTTP protocol and methods
2. URI 사용해 의도 전달
3. 요청, 응답에 JSON 사용
4. HTTP 상태코드로 결과 전달

## Designing the microservice architecture

### Decomposing the business problem → Data model

1. Describe the business problem and listen to the nouns 
2. Pay attention to the verbs
3. Look for data cohesion → Microservices should own their data

### Establishing service granularity

1. start broad with your microservices and refactor to smaller services
2. focus on how your services will interact with one another → interfaces
3. responsibilities will change over time as your understanding of the problem domain grows

Microservice : an expression of business logic and not an abstraction layer over your data sources

### Service interfaces

1. Embrace the REST philosophy
2. Use URI’s to communicate intent
3. Use JSON for your request and response
4. Use HTTP status codes to communicate results

### 마이크로 서비스 기본 골격

- maven script(pom.xml) 작성
- Bootstrap 클래스 작성
    - @SpringBootApplication

### Reference
https://www.manning.com/books/spring-microservices-in-action
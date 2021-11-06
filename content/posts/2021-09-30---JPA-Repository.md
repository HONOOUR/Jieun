---
title: "JPA Repository"
date: "2021-09-30T15:10:10.284Z"
template: "post"
draft: false
slug: "jpa-repository"
category: "Development"
tags:
  - "JAVA"
  - "Repository"
  - "JPA"
description: "JPA Repository for data access layer"
socialImage: ""
---

## Spring Data Repositories

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories)

### Concept

- reduce the amount of boilerplate code to implement data access layers for persistence data
- Central Interface is **Repository** T: domain class, ID: ID type
  **JPARepository** extends **CrudRepository** extends **Repository**<T, ID>

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference)

### Query Method

- CRUD functionality to query on the datastore
- Set up Spring to create proxy instances for those interfaces
  - JavaConfig
  - XML to define a bean

### Query Method Creation

- Any text between **find** (or other introducing keywords) and **By** is considered to be descriptive unless using one of the result-limiting keywords such as a Distinct
  e.g. FindTagsByAccount
  The word(Tags) is ignored and descriptive.
- Repository query keywords
  [https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.method.subject](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.method.subject)
  [subject keywords](https://www.notion.so/669740a532e8485d8e422a592009e9b1)

[predicate keywords](https://www.notion.so/de63fbba1f324a3296420b2777adcf33)

[https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference)

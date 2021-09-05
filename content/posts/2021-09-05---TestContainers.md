---
title: "TestDatabase with TestContainers"
date: "2021-09-05T15:10:10.284Z"
template: "post"
draft: false
slug: "test-database-testcontainers"
category: "Development"
tags:
  - "JAVA"
  - "Web MVC"
  - "TestContainers"
  - "PostgreSql"
description: "TestContainers 를 사용해 테스트DB 를 PostgreSql로 전환하기"
socialImage: ""
---

TestContainers 를 사용해 테스트DB 를 PostgreSql로 전환하기

## PreCondition

### [application-test.properties](http://application-test.properties) 만들기 (스프링부트 연동)

- resource 폴더 생성 [application-test.properties](http://application-test.properties) 만들기
- project structure 에서 resources로 지정해주기
- MockMvcTest(interface)에서 application-test.properties 사용하기

```java
// MockMvcTest.java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@ActiveProfiles("test")
@Transactional
@SpringBootTest
@AutoConfigureMockMvc
public @interface MockMvcTest {
}
```

![resources](/media/resources.jpg)

## Docker Requirement

[https://www.docker.com/get-started](https://www.docker.com/get-started)

## Pom.xml dependency

latest version을 찾아서 pom.xml 에 적용 maven project reload

[https://mvnrepository.com/artifact/org.testcontainers/testcontainers](https://mvnrepository.com/artifact/org.testcontainers/testcontainers)

[https://mvnrepository.com/artifact/org.testcontainers/postgresql](https://mvnrepository.com/artifact/org.testcontainers/postgresql)

```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers</artifactId>
    <version>1.16.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>postgresql</artifactId>
    <version>1.16.0</version>
    <scope>test</scope>
</dependency>
```

## TestContainers

[https://www.testcontainers.org/](https://www.testcontainers.org/)

use a containerized instance of a MySQL, PostgreSQL or Oracle database to test your data access layer code for complete compatibility

- JPA, Hibernate 가 만들어주는 쿼리가 DB 벤더에 따라 다름
- Test Database 를 운영하는 것이 번거로움

### Shared containers

각 Test class 에 컨테이너를 만드는 방법

[https://www.testcontainers.org/test_framework_integration/junit_5/](https://www.testcontainers.org/test_framework_integration/junit_5/)

@TestContainers finds all field with @Container and call container lifecycle methods

```java
@Testcontainers
public class AccountControllerTest {

    @Container
    PostgreSQLContainer postgreSQLContainer = new PostgreSQLContainer()
            .withDatabaseName()
            .withUsername()
            .withPassword()
```

### Singleton containers

[https://www.testcontainers.org/test_framework_integration/manual_lifecycle_control/#singleton-containers](https://www.testcontainers.org/test_framework_integration/manual_lifecycle_control/#singleton-containers)

abstract class 생성해서

static 안에 아래 내용을 넣어 base class 실행시 한번 호출되도록 한다

```java
// AbstractContainerBaseTest.java

public abstract class AbstractContainerBaseTest {
    static final PostgreSQLContainer POSTGRE_SQL_CONTAINER;

    static {
        POSTGRE_SQL_CONTAINER = new PostgreSQLContainer();
        POSTGRE_SQL_CONTAINER.start();
    }
}
```

```java

// SettingsControllerTest.java

@MockMvcTest
class SettingsControllerTest extends AbstractContainerBaseTest {
}
```

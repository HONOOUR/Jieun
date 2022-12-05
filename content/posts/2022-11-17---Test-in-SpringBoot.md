---
title: "Test in spring boot"
date: "2022-11-17T20:50:10.284Z"
template: "post"
draft: false
slug: "test-spring"
category: "Development"
tags:
  - "SpringBootTest"
  - "SpringBoot"
  - "mockito"
description: "build time out in spring boot test"
socialImage: ""
---

unit test 환경에서는 가능하면 MockitoExtension 을 사용해서 가볍게 실행


## @SpringBootTest

[https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications)

@ExtendWith(SpringExtension.class) 와 동일하다고 공식 문서에 나와 있음, SpringExtension을 가지고 테스트가 확장됨

기본적으로는 서버를 실행시키지 않지만 webEnvironment 속성을 줘서 변경 가능함

`SpringExtension` integrates the *Spring TestContext Framework* into JUnit 5's *Jupiter* programming model.

Annotation that can be specified on a test class that runs Spring Boot based tests.

*Spring TestContext Framework*

- Uses [SpringBootContextLoader](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootContextLoader.html) as the default [ContextLoader](https://docs.spring.io/spring-framework/docs/5.3.23/javadoc-api/org/springframework/test/context/ContextLoader.html?is-external=true) when no specific [@ContextConfiguration(loader=...)](https://docs.spring.io/spring-framework/docs/5.3.23/javadoc-api/org/springframework/test/context/ContextConfiguration.html?is-external=true#loader--) is defined.
- Automatically searches for a [@SpringBootConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringBootConfiguration.html) when nested `@Configuration` is not used, and no explicit [classes](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#classes--) are specified.
- Allows custom [Environment](https://docs.spring.io/spring-framework/docs/5.3.23/javadoc-api/org/springframework/core/env/Environment.html?is-external=true) properties to be defined using the [properties attribute](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#properties--).
- Allows application arguments to be defined using the [args attribute](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#args--).
- Provides support for different [webEnvironment](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html#webEnvironment--) modes, including the ability to start a fully running web server listening on a [defined](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.WebEnvironment.html#DEFINED_PORT) or [random](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.WebEnvironment.html#RANDOM_PORT) port.

<br/>

## @ExtendWith(MockitoExtension.class)

Developers can register one or more extensions *declaratively* by annotating a test interface, test class, test method, or custom *[composed annotation](https://junit.org/junit5/docs/current/user-guide/#writing-tests-meta-annotations)* with `@ExtendWith(…)` and supplying class references for the extensions to register.

Extension that initializes mocks and handles strict stubbings. This extension is the JUnit Jupiter equivalent of our JUnit4 `MockitoJUnitRunner`

<br/>

## Unit Test Example

### @Mock

mock injection + void method

```java
@ExtendWith(MockitoExtension.class)
@DisplayName("EventService Test")
public class EventServiceTest {

	@Mock
  EventRepository eventRepository;

	@Test
	void set_event_success() {
		...
		doNothing().when(eventRepository).enqueueEvent(event);
		...
	}
}
```

mock injection + T method

```java
@ExtendWith(MockitoExtension.class)
@DisplayName("EventService Test")
public class EventServiceTest {

	@Mock
  EventRepository eventRepository;

	@Test
	void set_event_success() {
		...
		when(eventRepository.getEvents()).thenReturn(List.of(EventVo));
		...
	}
}
``` 

### Reference

[https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications)

[https://stackoverflow.com/questions/61433806/junit-5-with-spring-boot-when-to-use-extendwith-spring-or-mockito](https://stackoverflow.com/questions/61433806/junit-5-with-spring-boot-when-to-use-extendwith-spring-or-mockito)
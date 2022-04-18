---
title: "IoC Container"
date: "2022-01-28T22:10:10.284Z"
template: "post"
draft: false
slug: "ioc-container"
category: "Development"
tags:
  - "Spring Boot"
  - "IoC"
description: "inversion of control and IoC in Spring"
socialImage: ""
---

## Inversion of control

- library 와 구분하는 framework 특징 중 하나
    
    *[inversion of control](https://en.wikipedia.org/wiki/Inversion_of_control)*
    : In a framework, unlike in libraries or in standard user applications, the overall program's [flow of control](https://en.wikipedia.org/wiki/Control_flow) is not dictated by the caller, but by the framework.
    
    [https://en.wikipedia.org/wiki/Software_framework](https://en.wikipedia.org/wiki/Software_framework)
    

A 인스턴스에서 B 인스턴스를 생성해서 사용하는것이 아니라 

제어권을 C 인스턴스에 위임하여 B를 생성하고,  A 인스턴스는 이미 생성한 B를 주입받아 사용하는 형태

## IoC in Spring

### ****Spring IoC container and beans****

Objects define dependencies, constructor arguments, arguments to a factory method, properties on the object instance(after it is constructed or returned from a factory method)

The container injects those dependencies when it creates the bean.

![ioc](/media/ioc.jpg)

Bean : IoC 컨테이너에서 관리하는 객체

IoC : 빈을 생성 관리(필요시 다른객체에 주입) 관리

한번 생성한 객체(인스턴스) 하나를 애플리케이션 전반에서 계속 사용하도록함 (재생성 없이) → singleton  scope

### Reference

[https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html)

****5.5.1 The singleton scope****

you define a bean definition and it is scoped as a singleton, the Spring IoC container creates *exactly one*
 instance of the object defined by that bean definition. This single instance is stored in a cache of such singleton beans, and *all subsequent requests and references*
 for that named bean return the cached object.

Spring's concept of a singleton bean differs from the Singleton pattern as defined in the Gang of Four (GoF) patterns book. The GoF Singleton hard-codes the scope of an object such that one *and only one*
 instance of a particular class is created *per `ClassLoader`*
. The scope of the Spring singleton is best described as *per container and per bean.*

****5.4.1 Dependency injection****

*Dependency injection* (DI) is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then *injects* those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name *Inversion of Control* (IoC), of the bean itself controlling the instantiation or location of its dependencies on its own by using direct construction of classes, or the *Service Locator* pattern.

Code is cleaner with the DI principle and decoupling is more effective when objects are provided with their dependencies. The object does not look up its dependencies, and does not know the location or class of the dependencies. As such, your classes become easier to test, in particular when the dependencies are on interfaces or abstract base classes, which allow for stub or mock implementations to be used in unit tests.

DI exists in two major variants, [Constructor-based dependency injection](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html#beans-constructor-injection) and [Setter-based dependency injection](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html#beans-setter-injection).

This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) [[1]](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/beans.html#ftn.d5e1144)
principle. IoC is also known as *dependency injection*
 (DI). It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then *injects*
 those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name *Inversion of Control*
 (IoC), of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes, or a mechanism such as the *Service Locator*
 pattern.
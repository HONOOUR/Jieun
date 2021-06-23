---
title: "ModelMapper - web project"
date: "2021-06-20T21:14:02.284Z"
template: "post"
draft: false
slug: "model-mapper"
category: "Development"
tags:
  - "ModelMapper"
  - "Spring"
  - "Java"
description: "ModelMapper 적용하기"
socialImage: ""
---

Object mapping makes it easy to convert one model to another, allowing separate models to remain segregated.

[http://modelmapper.org/](http://modelmapper.org/)

1. pom.xml 에 dependency 추가

    ```xml
    <!-- https://mvnrepository.com/artifact/org.modelmapper/modelmapper -->
    <dependency>
        <groupId>org.modelmapper</groupId>
        <artifactId>modelmapper</artifactId>
        <version>2.3.6</version>
    </dependency>
    ```

2. Bean 으로 등록

```java
// AppConfig.java		
		@Bean
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }
```

3. modelMapper 사용하기

map(source, destination)

source, destination 객체에 프로퍼티 모두 매칭이 될때,  source 데이터 destination으로 복사 

```java
// AccountService.java
	modelMapper.map(profile, account);
```

### configuration

ModelMapper 는 nested 객체를 참조하는것이 가능하기 때문에 어떻게 자를것인지 정해야한다.

```java
// Appconfig.java
		@Bean
    public ModelMapper modelMapper() {
        ModelMapper modelMapper = new ModelMapper();
        modelMapper.getConfiguration()
                .setDestinationNameTokenizer(NameTokenizers.UNDERSCORE)
                .setSourceNameTokenizer(NameTokenizers.UNDERSCORE);
        return new ModelMapper();
    }
```

UNDERSCORE(_)를 사용했을 때에만 nested 객체를 참조하는 것으로 간주하고 그렇지 않은 경우에는 해당 객체의 직속 프로퍼티에 바인딩 한다.

[http://modelmapper.org/user-manual/configuration/#matching-strategies](http://modelmapper.org/user-manual/configuration/#matching-strategies)

### 생성자 대신 사용

Notification class 프로퍼티를 직접 account를 주입받아 설정하는 대신 아래 방법 사용

```java

model.addAttribute(modelMapper.map(account, Notification.class));
```
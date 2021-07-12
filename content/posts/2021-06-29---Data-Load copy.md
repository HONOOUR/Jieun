---
title: "Load Data - web project"
date: "2021-06-29T21:20:55.284Z"
template: "post"
draft: false
slug: "load-data"
category: "Development"
tags:
  - "SpringBoot"
  - "Data Load"
  - "Java"
description: "애플리케이션 시작시 데이터 추가하기"
socialImage: ""
---

- CommandLineRunner, ApplicationRunner
- ApplicationStartedEvent 에 핸들러 추가

## CommandLineRunner, ApplicationRunner

These interfaces get called just before SpringApplication.run(…) completes.

[https://dzone.com/articles/spring-boot-applicationrunner-and-commandlinerunne](https://dzone.com/articles/spring-boot-applicationrunner-and-commandlinerunne)

> 23.8 Using the ApplicationRunner or CommandLineRunner
> [https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html](https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html)

```java
import org.springframework.boot.*;
import org.springframework.stereotype.*;

@Component
public class MyBean implements CommandLineRunner {

	public void run(String... args) {
		// Do something...
	}
}
```

## ApplicationStartingEvent

It is sent at the start of a run but before any processing, except for the registration of listeners and initializers

> 23.5 Application Events and Listeners
> [https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html](https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html)

## ApplicationStartedEvent

It is sent after the context has been refreshed but before any application and command-line runners have been called.

> 23.5 Application Events and Listeners
> [https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html](https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/boot-features-spring-application.html)

## PostConstruct

The PostConstruct annotation is used on a method that needs to be executed after dependency injection is done to perform any initialization.

Initializing a managed bean specifies the lifecycle callback method that the CDI framework should call after dependency injection but before the class is put into service.

[https://docs.oracle.com/javaee/7/api/javax/annotation/PostConstruct.html](https://docs.oracle.com/javaee/7/api/javax/annotation/PostConstruct.html)

```java
import javax.annotation.PostConstruct;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.util.List;
import java.util.stream.Collectors;

@Controller
@Transactional
@RequiredArgsConstructor
public class ZoneService {

    private final ZoneRepository zoneRepository;

    @PostConstruct
    public void initZoneData() throws IOException {
        if (zoneRepository.count() == 0) {
            Resource resource = new ClassPathResource("zone_kr.csv");
            List<Zone> zoneList = Files.readAllLines(resource.getFile().toPath(), StandardCharsets.UTF_8).stream()
                    .map(line-> {
                        String[] split = line.split(",");
                        return Zone.builder().city(split[0]).localNameOfCity(split[1]).province(split[2]).build();
                    }).collect(Collectors.toList());
        }
    }
}
```

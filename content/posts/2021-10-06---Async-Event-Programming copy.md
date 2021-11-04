---
title: "Async Event Driven Programming"
date: "2021-10-06T15:10:10.284Z"
template: "post"
draft: false
slug: "event-programming"
category: "Development"
tags:
  - "Async"
  - "Event"
description: "비동기 이벤트기반 프로그래밍"
socialImage: ""
---

## Async

- separate the thread
- response time (when processing an event - exception 이 발생해도 콜러에 영향을 주지 않음)

## ApplicationEventPublisher

```
publishEvent(Object event)
```

Notify all **matching** listeners registered with this application of an event.

### Publishing an Event

```java
public class StudyService {

    private final StudyRepository studyRepository;
    private final ModelMapper modelMapper;
    private final ApplicationEventPublisher eventPublisher;

    public Study createNewStudy(Study study, Account account) {
        Study newStudy = studyRepository.save(study);
        newStudy.addManager(account);
        eventPublisher.publishEvent(new StudyCreatedEvent(newStudy));
        return newStudy;
    }
}
```

### Event

- object or ApplicationEvent type
  [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEvent.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEvent.html)

```java
@Getter
public class StudyCreatedEvent {

    private Study study;

    public StudyCreatedEvent(Study study) {
        this.study = study;
    }
}
```

### Listener

- ApplicationListener를 구현하는 대신 @EventListener annotation 추가
- 비동기 → exception 이 발생해도 콜러에 영향을 주지 않음

```java
@Async
@Slf4j
@Transactional(readOnly = true)
@Component
public class StudyEventListener {

    @EventListener
    public void handleStudyCreatedEvent(StudyCreatedEvent studyCreatedEvent) {
        Study study = studyCreatedEvent.getStudy();
        log.info(study.getTitle() + " is created.");
        // TODO 해당 스터디 주제에 관심이 있는 사용자에게 알림
    }
}
```

---
title: "JPA - Summary"
date: "2021-06-01T20:14:02.284Z"
template: "post"
draft: false
slug: "jpa-summary"
category: "Development"
tags:
  - "Java"
  - "JPA"
  - "Persistence"
  - "Web"
description: "The example shows Entity State with JPA"
socialImage: ""
---

## Entity State

**Transient(New)** : no representation in the database

**Persistent(Managed)** : associated with persistence-context and transactional

**Detached** : have been disconnected from the EntityManager

**Removed** : marked for deletion, deleted from database during commit

## 예시

**problem**

database 업데이트 안됨

account 객체는 principal(session), detached 상태

```java
// SettingController.java
@PostMapping(SETTINGS_PROFILE_URL)
public String updateProfile(@CurrentAccount Account account, @Valid Profile profile, Errors errors, Model model) {
	if (errors.hasErrors()) {
		model.addAttribute(account);
    return SETTINGS_PROFILE_VIEW_NAME;
  }

  accountService.updateProfile(account, profile);
  return "redirect:/" + SETTINGS_PROFILE_VIEW_NAME;
}
```

**solution**

```java
// AccountService.java
public void updateProfile(Account account, Profile profile) {
    account.setUrl(profile.getUrl());
    account.setOccupation(profile.getOccupation());
    account.setLocation(profile.getLocation());
    account.setBio(profile.getBio());
    accountRepository.save(account);
}
```

![entity_1.jpa](entity_1.jpa)

![entity_2.jpa](entity_2.jpa)

## JPA (Java Persistence API)

- Java ORM 기술 표준
- **Persistence :** 데이터를 생성한 프로그램이 종료되어도 사라지지 않는 데이터의 특성 (DB, File)
- 동일한 트랜잭션에서 조회한 엔터티는 같음

  ```java
  String memberId = "100";
  Member m1 = jpa.find(Member.class, memberId); // SQL
  Member m2 = jpa.find(Member.class, memberId); // 캐시 (SQL 1번만 실행, m1을 가져옴)
  m1 == m2 // true
  ```

## ORM (Object Relational Mapping)

- OOP + RDBS 를 매핑

## JPA

![entity_3.jpa](entity_3.jpa)

![entity_4.jpa](entity_4.jpa)

### Insert

1. Member DAO Entity Object 저장
2. JPA → Entity 분석, 적절한 INSERT SQL 생성, JDBC API 사용, **패러다임의 불일치 해결**
3. JPA → JDBC API 사용하여 DB에 저장

![entity_5.jpa](entity_5.jpa)

### FIND

1. Member DAO Entity Object 찾음
2. JPA → 적절한 SELECT SQL 생성, JDBC API 사용, ResultSet 매핑, **패러다임의 불일치 해결**
3. JPA → JDBC API 사용하여 DB에서 결과를 받아옴

![entity_6.jpa](entity_6.jpa)

## JPA 표준

- interface 모음
- 구현체: 하이버네이트, EclipseLink, DataNucleus

## JPA 성능 최적화

### 1차 캐시와 동일성 보장

1. 같은 트랜잭션 안에서는 같은 엔터티를 반환 - 약간의 조회 성능 향상
2. DB isolation level 이 Read Commit 이어도 애플리케이션에서 Repeatable Read 보장

### 트랜잭션을 지원하는 쓰기 지연 - INSERT

### 지연 로딩(Lazy Loading), 즉시 로딩

지연 로딩: 객체가 실제 사용될 때 로딩

즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회

## JPA 영속성 컨텍스트(Persistence Context)

- 영속 상태 ← 영속성 컨텍스트에 의해 관리
- JPA 영속 컨텍스트에 속한 엔터티를 데이터베이스에 반영
- 엔티티 매니저를 사용해서 엔티티를 데이터베이스에 등록/수정/삭제/조회할 수 있다.

### Entity Manager

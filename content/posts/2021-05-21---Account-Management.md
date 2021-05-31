---
title: "Account manangement feature - web project"
date: "2021-05-21T20:14:02.284Z"
template: "post"
draft: false
slug: "account-management-web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "Spring Security"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

## Spring Security

It creates a user with the generated security password

![account1](/media/account_1.jpg)

Try login with Id(User) and the security password generated.

![account2](/media/account_2.jpg)

## Create Account Domain

domain package → Account class

- Log in, Profile, Alarm

```java
package com.studyolle.demo.domain;

import lombok.*;

import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Getter @Setter @EqualsAndHashCode(of = "id")
@Builder @AllArgsConstructor @NoArgsConstructor
public class Account {
    @Id @GeneratedValue
    private Long id;

    @Column(unique = true)
    private String email;

    @Column(unique = true)
    private String nickname;

    private String password;

    private boolean emailVerified;

    private String emailCheckerToken;

    private LocalDateTime joinedAt;

    private String bio;

    private String url;

    private String occupation;

    private String location; // varchar(255)

    @Lob @Basic(fetch = FetchType.EAGER) // text type larger than varchar
    private String profileImage;

    private boolean studyCreatedByEmail;

    private boolean studyCreatedByWeb;

    private boolean studyEnrollmentResultByEmail;

    private boolean studyEnrollmentResultByWeb;

    private boolean studyUpdateByEmail;

    private boolean studyUpdateByWeb;
}
```

## Create Account Controller

- GET “/sign-up” → servlet dispatcher → account/sign-up.html
- 회원 가입 폼에서 입력 받을 수 있는 정보를 “닉네임", “이메일", “패스워드" 폼 객체로 제공

spring boot auto configuration: it finds views under template directory

resolved by creating templates\account\sign-up.html

![account3](/media/account_3.jpg)

![account4](/media/account_4.jpg)

reference: https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-JPA-%EC%9B%B9%EC%95%B1/dashboard

---
title: "Account Manangement Feature - web project"
date: "2021-05-21T20:14:02.284Z"
template: "post"
draft: false
slug: "web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "Spring Security"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

### Spring Security

It creates a user with the generated security password

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/46c0ff71-8051-4c66-9d1b-6d225fcd9f9e/Screenshot_2021-05-25_at_10.52.22_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/46c0ff71-8051-4c66-9d1b-6d225fcd9f9e/Screenshot_2021-05-25_at_10.52.22_PM.png)

Try login with Id(User) and the security password generated.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/962fa45e-b62b-47ca-a906-f8812a91a703/Screenshot_2021-05-25_at_10.53.25_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/962fa45e-b62b-47ca-a906-f8812a91a703/Screenshot_2021-05-25_at_10.53.25_PM.png)

### Create Account Domain

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

### Create Account Controller

- GET “/sign-up” → servlet dispatcher → account/sign-up.html
- 회원 가입 폼에서 입력 받을 수 있는 정보를 “닉네임", “이메일", “패스워드" 폼 객체로 제공

spring boot auto configuration: it finds views under template directory

resolved by creating templates\account\sign-up.html

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a98da5fa-4c86-480b-8df8-6d3cf13623bc/Screenshot_2021-05-25_at_11.21.25_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a98da5fa-4c86-480b-8df8-6d3cf13623bc/Screenshot_2021-05-25_at_11.21.25_PM.png)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0790bc8c-a8db-4369-a9a6-3a975f050fe9/Screenshot_2021-05-26_at_11.29.54_PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0790bc8c-a8db-4369-a9a6-3a975f050fe9/Screenshot_2021-05-26_at_11.29.54_PM.png)

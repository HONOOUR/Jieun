---
title: "Create Test Code - web project"
date: "2021-05-27T20:14:02.284Z"
template: "post"
draft: false
slug: "create-test-code-web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "MockMvc"
  - "Test Code"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

- 코드를 변경한 이후에도 비지니스로직이 그대로 인지 검증한다

1. MockMvc class 사용
2. perform() 함수를 사용하여 그 리턴값 검사

   리턴값 = ResultActions instance

3. mockMvc.perform().andExpect() or andDo()

```java
@DisplayName("Sign in - input error")
@Test
void signUpSubmit_with_wrong_input() throws Exception {
		mockMvc.perform(post("/sign-up")
				    .param("nickname", "jieun")
            .param("email", "email...")
            .param("password", "123"))
            .andExpect(status().isOk())
            .andExpect(view().name("account/sign-up")); // page check
```

## Cross Site Request Forgery (CSRF)

[https://en.wikipedia.org/wiki/Cross-site_request_forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

[https://docs.spring.io/spring-security/site/docs/3.2.0.CI-SNAPSHOT/reference/html/csrf.html](https://docs.spring.io/spring-security/site/docs/3.2.0.CI-SNAPSHOT/reference/html/csrf.html)

a type of malicious exploit of a website where unauthorized commands are submitted from a user that the web application trusts.

such commands; specially-crafted image tags, hidden forms, and JavaScript XMLHttpRequests

note!

cookies can be sent along with other requests

### Solution

**Synchronizer Token Pattern**

our website requires a randomly generated token as an HTTP parameter to our session cookie

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3a7dc217-fb49-4132-abd4-3d2a0a32cc7c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3a7dc217-fb49-4132-abd4-3d2a0a32cc7c/Untitled.png)

### Cookies ??

### MockMvc with Spring security feature

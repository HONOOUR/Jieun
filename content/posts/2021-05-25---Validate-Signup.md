---
title: "Sign up validation - web project"
date: "2021-05-25T20:14:02.284Z"
template: "post"
draft: false
slug: "signup-validation-web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "Validation"
  - "Thymeleaf"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

## Add spring-boot-starter-validation

refer to [https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.3-Release-Notes#validation-starter-no-longer-included-in-web-starters](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.3-Release-Notes#validation-starter-no-longer-included-in-web-starters)

**Validation Starter no longer included in web starters**

As of [#19550](https://github.com/spring-projects/spring-boot/issues/19550), Web and WebFlux starters do not depend on the validation starter by default anymore. If your application is using validation features, for example you find that `javax.validation.*` imports are not being resolved, you’ll need to add the starter yourself.

For Maven builds, you can do that with the following:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

For Gradle, you will need to add something like this:

```json
`dependencies {
  ...
  implementation 'org.springframework.boot:spring-boot-starter-validation'
}`
```

```java
// 복합객체 생략 가능
@ModelAttribute
```

![signup](/media/signup.jpg)

## Spring Validation

- validate the **length**
- validate the **necessary value**

Spring 3 introduces several enhancements to its validation support.

1. the JSR-303 Bean Validation API is now fully supported.
2. when used programmatically, Spring’s DataBinder can now validate objects as well as bind to them.
3. Spring MVC now has support for declaratively **validating @Controller inputs**.

[https://docs.spring.io/spring-framework/docs/4.2.4.RELEASE/spring-framework-reference/html/validation.html#validation-beanvalidation](https://docs.spring.io/spring-framework/docs/4.2.4.RELEASE/spring-framework-reference/html/validation.html#validation-beanvalidation)

[http://hibernate.org/validator/](http://hibernate.org/validator/)

### JSR-303 Bean Validation API

Annotate **domain model properties** with declarative validation constraints and the runtime enforces them

## Custom Validation

- validate the value from database and show the same page again when it has an error.

1. Create SignUpFormValidator class
2. Implement Validator interface (spring framework)
3. Use SignUpFormValidator with @InitBinder inController
4. @Valid Check if SignUpForm is entered.

```java
// AccountController class

@PostMapping("/sign-up")
public String signUpSubmit(@Valid SignUpForm signUpForm, Errors errors) {
		if (errors.hasErrors()) {
    return "account/sign-up"; // return form
    }
}
```

```java
// SignUpFormValidator class

@Override
public void validate(Object object, Errors errors) {
    // access to nickname, email field in database
    SignUpForm signUpForm = (SignUpForm)object;
    if (accountRepository.existsByEmail(signUpForm.getEmail())) {
        errors.rejectValue("email", "invalid.email", new Object[]{signUpForm.getEmail()}, "이미 사용중인 이메일 입니다.");
    }
    if (accountRepository.existsByNickname(signUpForm.getNickname())) {
        errors.rejectValue("nickname", "invalid.nickname", new Object[]{signUpForm.getEmail()}, "이미 사용중인 닉네임 입니다.");
    }
}
```

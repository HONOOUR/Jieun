---
title: "Login - Spring Security 2"
date: "2021-11-22T22:10:10.284Z"
template: "post"
draft: false
slug: "security-login"
category: "Development"
tags:
  - "JAVA"
  - "Web MVC"
  - "Spring Security"
  - "Spring"
description: "Spring Security login with userDetailsService"
socialImage: ""
---

[https://docs.spring.io/spring-security/site/docs/4.1.x/reference/htmlsingle/](https://docs.spring.io/spring-security/site/docs/4.1.x/reference/htmlsingle/)

## Form Login

```html
<-- login.html --!>
<form
  class="needs-validation col-sm-6"
  action="#"
  th:action="@{/login}"
  method="post"
  novalidate
>
  <div class="form-group">
    <label for="username">이메일 또는 닉네임</label>
    <input
      id="username"
      type="text"
      name="username"
      class="form-control"
      placeholder="your@email.com"
      aria-describedby="emailHelp"
      required
    />
    <small id="emailHelp" class="form-text text-muted">
      패스워드가 기억나지 않는다면,
      <a href="#" th:href="@{/email-login}">패스워드 없이 로그인하기</a>
    </small>
    <small class="invalid-feedback">이메일을 입력하세요.</small>
  </div>
  <div class="form-group">
    <label for="password">패스워드</label>
    <input
      id="password"
      type="password"
      name="password"
      class="form-control"
      aria-describedby="passwordHelp"
      required
    />
    <small id="passwordHelp" class="form-text text-muted">
      패스워드가 기억나지 않는다면,
      <a href="#" th:href="@{/email-login}">패스워드 없이 로그인하기</a>
    </small>
    <small class="invalid-feedback">패스워드를 입력하세요.</small>
  </div>
  <div class="form-group form-check">
    <input
      type="checkbox"
      class="form-check-input"
      id="rememberMe"
      name="remember-me"
      checked
    />
    <label
      class="form-check-label"
      for="rememberMe"
      aria-describedby="rememberMeHelp"
      >로그인 유지</label
    >
  </div>
  <div class="form-group">
    <button
      class="btn btn-success btn-block"
      type="submit"
      aria-describedby="submitHelp"
    >
      로그인
    </button>
    <small id="submitHelp" class="form-text text-muted">
      처음 오셨다면 <a hre="#" th:href="@{/sign-up}">계정을 먼저 만드세요</a>
    </small>
  </div>
</form>
```

- A POST to the `/login` URL will attempt to authenticate the user ← Extract the username/password combination from the HTTP Basic Auth header in a filter. You don’t have to do anything for that, it will happen under the hood.
- Any other request needs the user to be authenticated *first*, i.e. the user needs to login.
- You are allowing form login (username/password in a form), with a custom loginPage (_`/login`_, i.e. not Spring Security’s auto-generated one). Anyone should be able to access the login page, without having to log in first (permitAll; otherwise we would have a Catch-22!).
- On top of that, you are also allowing Basic Auth, i.e. sending in an HTTP Basic Auth Header to authenticate.
- The username must be present as the HTTP parameter named *username*
- The password must be present as the HTTP parameter named *password*

### POST to /login

```java
public class AccountService implements UserDetailsService {

     // 1. Load the user from the users table by username. If not found, throw UsernameNotFoundException.
     // 2. Convert/wrap the user to a UserDetails object and return it.

    @Transactional(readOnly = true)
    @Override
    public UserDetails loadUserByUsername(String emailOrNickname) throws UsernameNotFoundException {
        Account account = accountRepository.findByEmail(emailOrNickname);
        if (account == null) {
            account = accountRepository.findByNickname(emailOrNickname);
        }

        if (account == null) {
            throw new UsernameNotFoundException(emailOrNickname);
        }
        return new UserAccount(account);
    }
}
```

Signup → store users to database table

Login → access to the user (loadUserByUsername(emailOrNickname))

Remember Login → access to user's hashed password

---
title: "Login - Spring Security"
date: "2021-07-12T22:10:10.284Z"
template: "post"
draft: false
slug: "security-persistent-login"
category: "Development"
tags:
  - "JAVA"
  - "Web MVC"
  - "Spring Security"
  - "Spring"
description: "Spring Security login 설정하기"
socialImage: ""
---

## Spring Security Login/ Logout

- 스프링 시큐리티에 의해서 모든 요청은 인증이 필요한 것으로 처리한다
- WebSecurityConfigurer 를 구현해 시큐리티 설정을 커스터마이징 할 수 있다.
- 인증없이 요청을 허락하도록 아래 처럼 작성한다

```java

// SecurityConfig class
@Configuration
@EnableWebSecurity // 스프링 시큐리티 설정 커스터마이징 할 것
@RequiredArgsConstructor
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http.authorizeRequests()
				.mvcMatchers("/", "/login", "/sign-up", "/check-email-token",
                        "/email-login", "/check-email-login", "/login-link").permitAll()
        .mvcMatchers(HttpMethod.GET, "/profile/*").permitAll()
        .anyRequest().authenticated();

    http.formLogin()
				.loginPage("/login").permitAll();
		http.logout()
        .logoutSuccessUrl("/");
}
```

```java
// login.html
<form class="needs-validation col-sm-6" action="#" th:action="@{/login}" method="post" novalidate>
```

Cookies

information from client (user id..)

로그인 전

![cookie](/media/cookie_1.jpg)

로그인 후 리다이렉트한 후

Value ← 서버쪽 세션 값과 동일

![cookie2](/media/cookie_2.jpg)
</br>
</br>

## Persistent-login

세션이 만료 되더라도 로그인을 유지하고 싶을 때

> 10.12. Remember-Me Authentication
> Remember-me or persistent-login authentication refers to web sites being able to remember the identity of a principal between sessions. This is typically accomplished by sending a cookie to the browser, with the cookie being detected during future sessions and causing automated login to take place. Spring Security provides the necessary hooks for these operations to take place, and has two concrete remember-me implementations. One uses hashing to preserve the security of cookie-based tokens and the other uses a database or other persistent storage mechanism to store the generated tokens.
> Note that both implementations require a `UserDetailsService`. If you are using an authentication provider which doesn’t use a `UserDetailsService` (for example, the LDAP provider) then it won’t work unless you also have a `UserDetailsService` bean in your application context.

- 쿠키를 한개 더 만든다
- 그 쿠키에 로그인 정보(아이디, 비밀번호)를 암호화해서 넣어둠
- 쿠키에 인증 정보를 남겨두고 세션이 만료 됐을 때에는 쿠키에 남아있는 정보로 인증한다.

### 문제 쿠키 탈취

**해시 기반의 쿠키**

- Username
- Password
- 만료 기간
- Key (애플리케이션 마다 다른 값을 줘야 한다.)
- 치명적인 단점, **쿠키를 다른 사람이 가져가면... 그 계정은 탈취당한 것과 같다.**

스프링 시큐리티 설정: 해시 기반 설정

http.rememberMe().key("랜덤한 키 값")

조금 더 안전한 방법은

- 쿠키안에 랜덤한 **문자열(토큰)**을 만들어 같이 저장하고 매번 인증할 때마다 바꾼다.
- Username, 토큰
- 문제는, 이 방법도 취약하다. 쿠키를 탈취 당하면, 해커가 쿠키로 인증을 할 수 있고, 희생자는 쿠키로 인증하지 못한다.

조금 더 개선한 방법

- Username, 토큰(랜덤, 인증할 때마다 매번 바뀜), 시리즈(랜덤, 인증할 때마다 바뀌지 않음, 고정)

  해커가 재 인증을 하면 토큰 값이 바뀜

  Username, 시리즈 비교

  Username, 토큰 비교 → 일치하지 않은 것으로 나옴

- 쿠키를 탈취 당한 경우, 희생자는 **유효하지 않은 토큰**과 **유효한 시리즈와 Username**으로 접속하게 된다.
- 이 경우, 모든 토큰을 삭제하여 해커가 더이상 탈취한 쿠키를 사용하지 못하도록 방지할 수 있다.

### Spring Security Settings

```java
//SecurityConfig.java
				http.rememberMe()
                .userDetailsService(accountService)
                .tokenRepository(tokenRepository());

    private PersistentTokenRepository tokenRepository() {
        JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
        jdbcTokenRepository.setDataSource(dataSource);
        return jdbcTokenRepository;
```

@Entity 맵핑으로 생성

```java
// PersistentLogins.java
@Table(name = "persistent_logins")
@Entity
@Getter @Setter
public class PersistentLogins {
```

![persistent](media/persistent.jpg)

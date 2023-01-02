---
title: "JWT발급, 인증 필터"
date: "2022-12-24T20:50:10.284Z"
template: "post"
draft: false
slug: "jwt-authentication"
category: "Development"
tags:
  - "JWT"
  - "Spring Security"
  - "Filter"
description: "스프링시큐리티 + 로그인 후 JWT 발급 필터 + JWT 인증, 권한 부여 필터"
socialImage: ""
---

# Spring security

스프링시큐리티 + 로그인 후 JWT 발급 필터 + JWT 인증, 권한 부여 필터

## 로그인 후 JWT 발급 필터 구현

1. UserDetails 를 반환하는 UserDetailsService를 생성
    1. loadUserByUsernameAndDomain() : DB에서 username 으로 가입한 계정을 확인하고 UserDetails 를 반환하는 함수
2. AbstractUserDetailsAuthenticationProvider 을 확장한 CustomAuthenticationProvider 생성
    1. AuthenticationProvider은 AuthenticationManager + ProviderManager method
    2. retrieveUser(username, authentication) 함수를 재정의함
        1. 전달받는 객체는 UsernamePasswordAuthenticationToken(authentication type)으로 만듦
        2. loadUserByUsernameAndDomain()을 호출해서 받은  UserDetails 객체를 반환
3. UsernamePasswordAuthenticationFilter 을 확장한 JwtAuthenticationFilter생성
    1. [https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html#servlet-authentication-usernamepasswordauthenticationfilter](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/form.html#servlet-authentication-usernamepasswordauthenticationfilter)
    2. {username, password} 를 요청에서 얻음
    3. 파라미터로 custom authentication provider을 전달
    4. **attemptAuthentication** (HTTP request 진입점, default address /login)
    5. **successfulAuthentication**  Authentication 객체 반환 성공 후
4. JwtAuthenticationFilter 에서 **attemptAuthentication**과  **successfulAuthentication**을 재정의
    1. attemptAuthentication() : authentication token 을 생성해 전달받은 CustomAuthenticationProvider.authenticate(authentication token)함수에 파라미터로 넘겨 호출 하면 위에서 재정의한 retrieveUser(retrieveUser) 함수가 호출, 응답으로 받은 UserDetails 객체를 UsernamePasswordAuthenticationToken type으로 만들고 반환
    2. successfulAuthentication() : 위에서 정상적으로 Authentication 객체를 반환하면 호출됨, JWT 를 생성해 응답값 헤더에 포함 시킴

```java
// AbstractUserDetailsAuthenticationProvider.java
public abstract class AbstractUserDetailsAuthenticationProvider
		implements AuthenticationProvider, InitializingBean, MessageSourceAware {
	...
	@Override
	public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		Assert.isInstanceOf(UsernamePasswordAuthenticationToken.class, authentication,
				() -> this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.onlySupports",
						"Only UsernamePasswordAuthenticationToken is supported"));
		String username = determineUsername(authentication);
		boolean cacheWasUsed = true;
		UserDetails user = this.userCache.getUserFromCache(username);
		if (user == null) {
			cacheWasUsed = false;
			try {
				user = retrieveUser(username, (UsernamePasswordAuthenticationToken) authentication);
			}
			catch (UsernameNotFoundException ex) {
				this.logger.debug("Failed to find user '" + username + "'");
				if (!this.hideUserNotFoundExceptions) {
					throw ex;
				}
				throw new BadCredentialsException(this.messages
						.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
			}
			Assert.notNull(user, "retrieveUser returned null - a violation of the interface contract");
		}
		try {
			this.preAuthenticationChecks.check(user);
			additionalAuthenticationChecks(user, (UsernamePasswordAuthenticationToken) authentication);
		}
		catch (AuthenticationException ex) {
			if (!cacheWasUsed) {
				throw ex;
			}
			// There was a problem, so try again after checking
			// we're using latest data (i.e. not from the cache)
			cacheWasUsed = false;
			user = retrieveUser(username, (UsernamePasswordAuthenticationToken) authentication);
			this.preAuthenticationChecks.check(user);
			additionalAuthenticationChecks(user, (UsernamePasswordAuthenticationToken) authentication);
		}
		this.postAuthenticationChecks.check(user);
		if (!cacheWasUsed) {
			this.userCache.putUserInCache(user);
		}
		Object principalToReturn = user;
		if (this.forcePrincipalAsString) {
			principalToReturn = user.getUsername();
		}
		return createSuccessAuthentication(principalToReturn, authentication, user);
	}
	...
}
```

## JWT 인증, 권한 부여 필터

1. BasicAuthenticationFilter를 확장한 JWTAuthorizationFilter 를 생성
    1. doFilterInternal 재정의
2. doFilterInternal() 
    1. JWT 서명검증
    2. Authentication 객체 생성 
    3. 시큐리티 세션에 Authentication 객체 저장
![jwt](/media/jwt.jpg)

## 용어 설명

Authentication : who are you 

### AuthenticationManager

- method: authenticate() → return an Authentication
- implementation: by AuthenticationProvider(ProviderManager) → query whether is supports a given Authentication type
- customization: by AuthenticationManagerBuilder(global - @Autowired) → set up in-memory, JDBC, or LDAP or UserDetailsService(custom)
- Spring boot provides a default global AuthenticationManager

### AuthenticationProvider

- like an AuthenticaitonManager + an extra method to allow the caller to query whether it supports a given Authentication type

authorization : access control (what are you allowed to do)

### Web Security

- based on the path of the request URI → filters, servlet applied
- FilterChainProxy: contains all the security logic arranged internally

### Creating and Customizing Filter Chains

### Request Matching for Dispatch and Authorization

- request matcher: decide whether to apply it to an HTTP request
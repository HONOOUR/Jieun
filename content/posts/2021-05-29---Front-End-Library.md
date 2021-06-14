---
title: "Front End Library - web project"
date: "2021-05-29T20:14:02.284Z"
template: "post"
draft: false
slug: "front-end-library-web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "Front End"
  - "NPM"
  - "BootStrap"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

## Considerations

- Build
- Version
- Security Settings

만들고 있는 웹 프로젝트는 Front/Back 구분하지 않음

프론트 라이브러리는 NPM 으로 다운로드

## Build

### NPM

- package.json

mv, pom.xml 사용해서 빌드함

package 순서에 npm install 같이 필요함

→ plugin 사용

```html
<!--index.html-->
<!--w npm-->
<link
  rel="stylesheet"
  href="/node_modules/bootstrap/dist/css/bootstrap.min.css"
/>
<!--w/o npm-->
<link
  href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
/>
```

## Version

```
.gitignore

### NPM ###
src/main/resources/static/node_modules
```

## Security Settings

Spring boot 정적 리소스 제공

- src/main/resources/static

/node_modules/\*\* 요청에는 시큐리티 필터를 적용하지 않도록 설정한다.

```java
// SecurityConfig.java
@Override
public void configure(WebSecurity web) throws Exception {
    web.ignoring()
            .mvcMatchers("/node_modules/**")
            .requestMatchers(PathRequest.toStaticResources().atCommonLocations());
}
```

## Thymeleaf Fragment

[https://www.thymeleaf.org/doc/articles/layouts.html](https://www.thymeleaf.org/doc/articles/layouts.html)

재사용 가능한 부분

해더, 네비게이션 바, 풋터

## Library

```jsx
<!--fragments.html-->
<link rel="stylesheet" href="/node_modules/font-awesome/css/font-awesome.min.css">
<script src="/node_modules/jquery/dist/jquery.min.js"></script>
<script src="/node_modules/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
<script src="/node_modules/jdenticon/dist/jdenticon.min.js"></script>
```

폰트어썸 아이콘 사용하기

- <i class=”fa fa-XXXX”></i>

Jdenticon으로 아바타 생성하기

- <svg width="80" height="80" data-jdenticon-value="user127"></svg>

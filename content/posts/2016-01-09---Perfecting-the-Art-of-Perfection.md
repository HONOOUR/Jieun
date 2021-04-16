---
title: Blog
date: "2021-04-14T21:30:37.121Z"
template: "post"
draft: false
slug: "blog"
category: "Development"
tags:
  - "Gatsby"
  - "Blog"
  - "Netlify"
description: "Gatsby framework, Netlify, JAM Stack"
socialImage: "/media/dns.jpg"
---

Gatsby 프레임워크를 사용해 프로젝트를 만들고 도메인을 구매하여 Netlify에 연결하였다.<br />
아래 사이트를 참고했다.

- https://www.netlify.com/blog/2016/02/24/a-step-by-step-guide-gatsby-on-netlify/
- https://www.regyu.dev/etc/start-gatsby-for-blog/
  <br />
  <br />

## **Step**

### 1. Install Gatsby

```javascript
npm install -g gatsby-cli
```

### 2. Install the theme you select from Gatsby site.

https://www.gatsbyjs.com/starters/? 에서 테마 선택

```javascript
gatsby new <project name> https://github.com/alxshelepenok/gatsby-starter-lumen

```

### 3. Create Git Repository for this project

### 4. Connect the git repo to Netlify

### 5. Buy domain

https://www.godaddy.com 에서 jieun.dev 를 구매했다.

### 6. Add custom domain to Netlify

jieun.dev 를 추가하고 아래 경고 메시지가 보였다.
고대디(provider)로 가서 네임서버를 확인하라는 내용같아 고대디-> 도메인 설정-> DNS 관리를 확인해보니 호스트 이름이 달랐고 두개 를 맞춰주고 Netlify 에서 DNS를 관리하도록 변경하였다.
나중에 Netlify에서 관련 내용을 찾을 수 있었다. (https://docs.netlify.com/domains-https/netlify-dns/delegate-to-netlify/)

![warning](/media/dns_2.jpg)

![domain](/media/dns.jpg)
<br />
<br />

## **Reference**

- https://docs.netlify.com/domains-https/netlify-dns/delegate-to-netlify/ (Delegate Domain to Netlify)
- https://docs.netlify.com/domains-https/custom-domains/#definitions (Grossary)
- https://www.netlify.com/blog/2016/02/24/a-step-by-step-guide-gatsby-on-netlify/
- https://www.regyu.dev/etc/start-gatsby-for-blog/

---
title: "Create sign up page - web project"
date: "2021-05-22T20:14:02.284Z"
template: "post"
draft: false
slug: "signup-page-web-project"
category: "Development"
tags:
  - "Java"
  - "Spring"
  - "BootStrap"
  - "Thymeleaf"
  - "Web"
description: "Create a web application with Spring"
socialImage: ""
---

## bootstrap

[https://getbootstrap.com/](https://getbootstrap.com/)

Install CSS, JS plugins

[https://getbootstrap.com/docs/5.0/getting-started/introduction/](https://getbootstrap.com/docs/5.0/getting-started/introduction/)

### CSS

```html
<link
  href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
  rel="stylesheet"
  integrity="sha384-+0n0xVW2eSR5OomGNYDnhzAbDsOXxcvSN1TPprVMTNDbiYZCxYbOOl7+AMvyTG2x"
  crossorigin="anonymous"
/>
```

### JS

JS plugins, Poper

Include every Bootstrap JavaScript plugin and dependency

at the end of page right before </body> tag

```html
<!--Bundle-->
<script
  src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.bundle.min.js"
  integrity="sha384-gtEjrD/SeCtmISkJkNUaaKMoLD0//ElJ19smozuHV6z3Iehds+3Ulb9Bn9Plx0x4"
  crossorigin="anonymous"
></script>

<!--Separate-->
<script
  src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js"
  integrity="sha384-IQsoLXl5PILFhosVNubq5LC7Qb9DXgDA9i+tQ8Zj3iwWAwPtgFTxbJ8NT4GN1R8p"
  crossorigin="anonymous"
></script>
<script
  src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.min.js"
  integrity="sha384-Atwg2Pkwv9vp0ygtn1JAojH0nYbwNJLPhwyoVbhoPwBhjQPR5VtM2+xf0Uwh9KtT"
  crossorigin="anonymous"
></script>
```

- create a navigation bar
- create a form
  </br>
  </br>

## Thymeleaf

it allows you to define your own sets of template attributes (or even tags) with the names you want, evaluating the expressions you want in the syntax you want and applying the logic you want. It’s more like a template engine framework.

rendering with thymeleaf ??

@ for the time when sublet path is root

### Standard Expression syntax

Most Thymeleaf attributes allow their values to be set as or containing *expressions*, which we will call *Standard Expressions* because of the dialects they are used in. These can be of five types:

- **`${...}`** : Variable expressions.
- **`{...}`** : Selection expressions.
- **`#{...}`** : Message (i18n) expressions.
- **`@{...}`** : Link (URL) expressions.
- **`~{...}`** : Fragment expressions.

```html
The object they act on is specified by a th:object attribute:

<div th:object="${book}">
  ...
  <span th:text="*{title}">...</span>
  ...
</div>
```

[https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html](https://www.thymeleaf.org/doc/articles/standarddialect5minutes.html)

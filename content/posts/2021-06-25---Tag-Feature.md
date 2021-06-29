---
title: "Tag feature - web project"
date: "2021-06-20T21:14:02.284Z"
template: "post"
draft: false
slug: "tag"
category: "Development"
tags:
  - "Tagify"
  - "Spring"
  - "Java"
description: "Tag 기능 추가하기"
socialImage: ""
---

### 1. Tag view (page) 생성

- 타임리프 자바스크립트 템플릿

  ```java
  <script type="application/javascript" th:inline="javascript">
  </script>
  ```

- [https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#javascript-inlining](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#javascript-inlining)
  - Escaped: [[${variable}]]
  - Unescaped: [(${variable})]
  - 네추럴 템플릿: /_[[${variable}]]_/ null;

**note**

- Ajax 호출시 CSRF 토큰을 전달 하는 방법 + 타임리프
- [https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-csrf-include-ajax](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-csrf-include-ajax)

  ```java
  <script type="application/javascript" th:inline="javascript">
          $(function() {
              var csrfToken = /*[[${_csrf.token}]]*/ null;
              var csrfHeader = /*[[${_csrf.headerName}]]*/ null;
              $(document).ajaxSend(function (e, xhr, options) {
                  xhr.setRequestHeader(csrfHeader, csrfToken);
              });
          });
  </script>
  ```

### 2. Tag front library 사용

- [Tagify](https://github.com/yairEO/tagify)
- npm install @yaireo/tagify
- [https://yaireo.github.io/tagify/](https://yaireo.github.io/tagify/)

### 3. Tag 추가, 제거 기능 구현

1. Add 요청

   - TagRepository 를 만들어 DB 에서 Tag를 찾는 메소드 생성

     ```java
     // TagRepository.java
     @Transactional(readOnly = true)
     public interface TagRepository extends JpaRepository<Tag, Long> {
         Tag findByTitle(String title);
     }
     ```

   - 요청이 들어온 tag 를 DB 에서 찾고 없으면 Tag view에 tags를 보여주는 곳에 저장한다

     ```java
     @PostMapping(SETTINGS_TAGS_URL + "/add")
     @ResponseBody
     public ResponseEntity addTag(@CurrentAccount Account account, @RequestBody TagForm tagForm) {
             String title = tagForm.getTagTitle();
             Tag tag = tagRepository.findByTitle(title);
             if (tag == null) {
                 tag = tagRepository.save(Tag.builder().title(tagForm.getTagTitle()).build());
             }

             accountService.addTag(account, tag);
             return ResponseEntity.ok().build();
     }
     ```

   - 그 계정이 가지고 있는 tag 리스트에도 추가한다

     note: detached 상태 객체는 one-to-many / many to many 관계 value 는 null 이고

     account 에 저장하려할 때 detached 객체라면 먼저 persistent 객체로 가져와야 한다.

     ```java
     // AcountService.java
     public void addTag(Account account, Tag tag) {
             Optional<Account> byId = accountRepository.findById(account.getId());
             byId.ifPresent((a -> a.getTags().add(tag)));
             accountRepository.save(account);
     }
     ```

2. Remove 요청

   - 지우려는 tag가 보이지 않는다면 아래 처럼 badRequest 를 반환한다.

     ```java
     @PostMapping(SETTINGS_TAGS_URL + "/remove")
     @ResponseBody
     public ResponseEntity removeTag(@CurrentAccount Account account, @RequestBody TagForm tagForm) {
             String title = tagForm.getTagTitle();
             Tag tag = tagRepository.findByTitle(title);
             if (tag == null) {
                 return ResponseEntity.badRequest().build();
             }

             accountService.removeTag(account, tag);
             return ResponseEntity.ok().build();
     }
     ```

---
title: "Form data with Model Attribute"
date: "2021-08-10T22:10:10.284Z"
template: "post"
draft: false
slug: "form-requestparam"
category: "Development"
tags:
  - "JAVA"
  - "Web MVC"
  - "Spring"
description: "단일 데이터 주고 받기"
socialImage: ""
---

### @RequestParam 을 사용

- @RequestParam 생략가능
- 직접 유효한지 검사 필요

```java
@PostMapping("/study/title")
public String updateStudyTile(@CurrentAccount Account account, @PathVariable String path, Model model, @RequestParam String newTitle, RedirectAttributes attributes) {
		Study study = studyService.getStudyToUpdate(account, path);;
    if (!studyService.isValidTitle(newTitle)) {
        model.addAttribute(account);
        model.addAttribute(study);
        model.addAttribute("studyTitleError", "해당 스터디 이름은 변경할 수 없습니다.");
        return "study/settings/study";
    }
    studyService.updateStudyTitle(study, newTitle);
    attributes.addFlashAttribute("messge", "이름을 변경했습니다.");

    return "redirect:/study/" + study.getPath() + "/settings/study";
}
```

### <form 에 object 를 사용하지 않고 <input name, th:value 사용

- name="newTitle"
- th:value="${study.title}"

```jsx
<form class="needs-validation col-12" action="#" th:action="@{'/study/' + ${study.path} + '/settings/study/title'}" method="post" novalidation>
    <div class="alert alert-warning" role="alert">
        스터디 이름을 수정합니다.<br/>
    </div>
    <div class="form-group">
        <label for="title">스터디 이름</label>
        <input id="title" type="text" name="newTitle" th:value="${study.title}" class="form-control"
               placeholder="스터디 이름" aria-describedby="titleHelp" required maxlength="50">
        <small id="titleHelp" class="form-text text-muted">
            스터디 이름을 50자 이내로 입력하세요.
        </small>
        <small class="invalid-feedback">스터디 이름을 입력하세요.</small>
        <small class="form-text text-danger" th:if="${studyTitleError}" th:text="${studyTitleError}">Title Error</small>
    </div>
    <div class="form-group">
        <button class="btn btn-outline-warning" type="submit" aria-describedby="submitHelp">스터디 이름 수정</button>
    </div>
</form>

```

### 객체앞에 @RequestParam @ModelAttribute 모두 생략가능함

@RequestParam String newPath

@ModelAttribute StudyPathfrom studyPathForm

### Model Attribute 를 통해 주고 받는 법과 비교

참고 ([https://jieun.dev/posts/form-model-attribute](https://jieun.dev/posts/form-model-attribute))

```html
<--study.html--!>
<form
  th:if="${!study.closed}"
  th:action="@{'/study/' + ${study.getPath()} + '/settings/study/path'}"
  th:object="${studyPathForm}"
  method="post"
  novalidate
>
  <div class="alert alert-info" role="alert">
    스터디 경로를 수정하면 이전에 사용했던 경로로 스터디에 접근할 수 없으니
    주의하세요.
  </div>
  <input
    id="path"
    type="text"
    th:field="*{path}"
    class="form-control"
    placeholder="예) study-path"
    aria-describedby="pathHelp"
    required
    min="2"
    max="20"
  />
  <small id="pathHelp" class="form-text text-muted">
    공백없이 문자, 숫자, 대시(-)와 언더바(_)만 2자 이상 20자 이내로 입력하세요.
    스터디 홈 주소에 사용합니다. 예) /study/<b>study-path</b>
  </small>
  <small class="invalid-feedback">스터디 경로를 입력하세요.</small>
  <small
    class="form-text text-danger"
    th:if="${studyPathError}"
    th:text="${studyPathError}"
    >Path Error</small
  >
  <div class="form-group">
    <button
      class="btn btn-outline-primary btn-block"
      type="submit"
      aria-describedby="submitHelp"
    >
      경로 수정
    </button>
  </div>
</form>
```

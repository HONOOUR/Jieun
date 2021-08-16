---
title: "Form data with Model Attribute"
date: "2021-07-10T22:10:10.284Z"
template: "post"
draft: false
slug: "form-model-attribute"
category: "Development"
tags:
  - "JAVA"
  - "Web MVC"
  - "Spring"
description: "Model Attribute 로 객체 주고 받기"
socialImage: ""
---

### 1. Form 채울 객체 생성

Profile class 생성

```java
// Profile.java
@Data
public class Profile {

    @Length(max = 35)
    private String bio;

    @Length(max = 50)
    private String url;

    @Length(max = 50)
    private String occupation;
}
```

### 2. Model Attribute 정보를 채워 View 로 보여주기

Profile view 가 처음 보여졌을 때 account 에서 가져온 정보중에서 profile 에 해당하는 것을 채워줌

```java
// SettingsController.java
@GetMapping(SETTINGS_PROFILE_URL)
public String updateProfileForm(@CurrentAccount Account account, Model model) {
    model.addAttribute(account);
    model.addAttribute(modelMapper.map(account, Profile.class));
    return SETTINGS_PROFILE_VIEW_NAME;
}
```

### 3. Form 을 만들기

th:action="@{/settings/profile}"

→ directs the form to POST to /settings/profile endpoint

th:object="${profile}"

→ declares the model object(Profile) to use for collecting the form data.

th:field="\*{bio}"

→ correspond to the fields in the Profile object

```html
<--profile.html--!>
<form
  class="col-sm-6"
  action="#"
  th:action="@{/settings/profile}"
  th:object="${profile}"
  method="post"
  novalidate
>
  <div class="form-group">
    <label for="bio">한 줄 소개</label>
    <input
      id="bio"
      type="text"
      th:field="*{bio}"
      class="form-control"
      placeholder="간략한 소개를 부탁합니다."
      aria-describedby="bioHelp"
      required
    />
  </div>
  <div class="form-group">
    <label for="url">링크</label>
    <input
      id="url"
      type="url"
      th:field="*{url}"
      class="form-control"
      placeholder="http://studyolle.com"
      aria-describedby="urlHelp"
      required
    />
  </div>
  <div class="form-group">
    <label for="company">직업</label>
    <input
      id="company"
      type="text"
      th:field="*{occupation}"
      class="form-control"
    />
  </div>
  <div class="form-group">
    <button
      class="btn btn-primary btn-block"
      type="submit"
      aria-describedby="submitHelp"
    >
      수정하기
    </button>
  </div>
</form>
```

### 4. Model Attribute 로 주고 받기

@Valid @ModelAttribute Profile profile

@ModelAttribute 가 생략됨

Spring MVC profile 객체를 확인하는데 객체가 채워져 있지 않다면 Null Reference 발생할 수 있음

필요하다면 Profile class에 default 생성자를 추가 하도록 한다

```java
@PostMapping(PROFILE)
public String updateProfile(@CurrentAccount Account account, @Valid Profile profile, Errors errors,
                            Model model, RedirectAttributes attributes) {
    if (errors.hasErrors()) {
        model.addAttribute(account);
        return SETTINGS + PROFILE;
    }
    accountService.updateProfile(account, profile);
    attributes.addFlashAttribute("message", "프로필을 수정했습니다.");
    return "redirect:/" + SETTINGS + PROFILE;
}
```

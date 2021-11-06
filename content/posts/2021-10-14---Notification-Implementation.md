---
title: "Notification icon implementation"
date: "2021-10-14T15:10:10.284Z"
template: "post"
draft: false
slug: "notification-implementaion"
category: "Development"
tags:
  - "Java"
  - "Spring Boot"
description: "알람 아이콘 구현하기"
socialImage: ""
---

## HandlerInterceptor

A HandlerInterceptor gets called before the appropriate HandlerAdapter triggers the execution of the handler itself

### postHandle

(after handler and before view rendering)

### Create NotificationInterceptor class

- View rendering 전에 알림이 있는지에 대한 flag를 Model 에 담아준다

```java
@Component
@RequiredArgsConstructor
public class NotificationInterceptor implements HandlerInterceptor {

    private final NotificationRepository notificationRepository;

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
        if (modelAndView != null && !isRedirectView(modelAndView) && authentication != null && authentication.getPrincipal() instanceof UserAccount) {
            Account account = ((UserAccount)authentication.getPrincipal()).getAccount();
            Long count = notificationRepository.countByAccountAndChecked(account, false);
            modelAndView.addObject("hasNotification", count > 0);
        }
    }

    private boolean isRedirectView(ModelAndView modelAndView) {
        return modelAndView.getViewName().startsWith("redirect:") || modelAndView.getView() instanceof RedirectView;
    }
}
```

### implements WebMvcConfigurer

- NotificationInterceptor를 등록한다

```java
@Configuration
@RequiredArgsConstructor
public class WebConfig implements WebMvcConfigurer {

    private final NotificationInterceptor notificationInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        List<String> staticResourcesPath = Arrays.stream(StaticResourceLocation.values())
                .flatMap(StaticResourceLocation::getPatterns)
                .collect(Collectors.toList());
        staticResourcesPath.add("/node_modules/**");

        registry.addInterceptor(notificationInterceptor)
                .excludePathPatterns(staticResourcesPath);
    }
}
```

### View

- bootstrap if 문법을 사용해 아이콘 색을 변경한다

```html
<i th:if="${!hasNotification}" class="fa fa-bell-o" aria-hidden="true"></i>
<span class="text-info">
  <i th:if="${hasNotification}" class="fa fa-bell-o" aria-hidden="true"></i>
</span>
```

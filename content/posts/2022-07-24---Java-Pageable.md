---
title: "Pageable 적용"
date: "2022-07-24T22:20:10.284Z"
template: "post"
draft: false
slug: "pageable"
category: "Development"
tags:
  - "Java"
  - "Querydsl"
  - "CloudFormation"
description: "Pageable 적용 with querydsl"
socialImage: ""
---


인터페이스 Pageable 구현한 객체는 PageRequest, QPageRequest

### QPageRequest

Querydsl 을 위한 Pageable 구현체

### PagingAndSortingRepository

Pageable 을 파라미터로 받는 매서드를 가지고 JpaRepository 가 상속한다

### PageRequest

[https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/PageRequest.html](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/PageRequest.html)

pageRequest 객체를 생성해  pageable 파라미터를 받는 곳(repository)에 넘긴다

PageRequest.of(`page` , `size`)

`page` : zero-based page index, must not be negative

`size` : the size of the page to be returned, must be greater than 0.

```java
// test use
// 한 페이지 사이즈를 3으로 정하고 첫 번째 페이지 반환
PageRequest pageRequest = PageRequest.of(0, 3);
```

결과가 없는경우 

![pagerequest-1](/media/pagerequest-1.jpg)

```json
{
    "content": [],
    "pageable": "INSTANCE",
    "totalPages": 1,
    "last": true,
    "totalElements": 0,
    "number": 0,
    "sort": {
        "sorted": false,
        "unsorted": true,
        "empty": true
    },
    "numberOfElements": 0,
    "first": true,
    "size": 0,
    "empty": true
}
```

결과있는경우

![pagerequest-2](/media/pagerequest-2.jpg)

### Pageable and Querydsl method

querydsl을 사용해서(여기서 constructor 사용) 반환한 객체를 

[https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/PageImpl.html](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/PageImpl.html)

PageImple 을 content 로 리스트를 인자를 주어 생성한다

`content` : the content of this page, must not be null.

`pageable` : the paging information, must not be null.

`total` : the total amount of items available. The total might be adapted considering the length of the content given, if it is going to be the content of the last page. This is in place to mitigate inconsistencies.

```java
public Page<ItemVo> findByItemIdxAndAccountIdx(ItemListParam form, Long accountIdx, Pageable pageable) {
        List<ItemVo> items = jpaQueryFactory.select(Projections.fields(ItemVo.class, item.itemIdx, item.itemKindIdx).from(item)
                .where(item.itemIdx.eq(form.getItemIdx()).and(item.accountIdx.eq(accountIdx)))
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetch();
        return new PageImpl<>(items);
```

리스트 형으로 컨텐츠를 리턴하는 방법

```java
public List<ItemVo> findByItemIdxAndAccountIdx(ItemListParam form, Long accountIdx, Pageable pageable) {
        return jpaQueryFactory.select(Projections.fields(ItemVo.class, item.itemIdx, item.itemKindIdx).from(item)
                .where(item.itemIdx.eq(form.getItemIdx()).and(item.accountIdx.eq(accountIdx)))
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetch();
```
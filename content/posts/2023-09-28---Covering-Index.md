---
title: "Covering Index"
date: "2023-09-28T20:50:10.284Z"
template: "post"
draft: false
slug: "covering-index"
category: "Development"
tags:
  - "Index"
  - "Database"
  - "Querydsl"
  - "Mysql"
description: "key lookup, no key lookup(covering index)"
socialImage: ""
---

## Key Lookups
인덱스를 가지고 실제 데이터를 찾음 

### Clustered index
- 실제 데이터페이지를 포함하는 인덱스
- 테이블당 한개 존재
- 리프페이지 = 데이터페이지

### Non Clustered index
- 유니크 제약조건으로 생성
- 데이터 페이지의 주소만 가지고 있음 (실제 데이터 X)
- 리프페이지 = 논 클러스터링 인텍스 페이지 (추가적인 페이지)

## Covering index
- no key lookup
- 인덱스 컬럼에만 접근하고 데이터 페이지에 접근하여 다른 컬럼을 가져오지 않아 속도가 빠름
- 쿼리 실행계획에서 using index 를 표시

![explain](/media/explain.jpg)

## Querydsl 에서 적용하는법

```java
private List<TripDto> getSearchTripRouteGroup(TripSearchDto searchDto, PagingParam pagingParam) {
    List<Long> ids = getTripIndexQuery(searchDto)
            .limit(pagingParam.getPageSize())
            .offset(pagingParam.getOffset() * pagingParam.getPageSize())
            .fetch();

    if (CollectionUtils.isEmpty(ids)) {
      return new ArrayList<>();
    }

    return from(trip)
						.select(trip.name, trip.createdAt)
						.where(trip.id.in(ids)
						.fetch();
  }

  private JPQLQuery<Long> getTripIndexQuery(TripSearchDto searchDto) {
    return from(trip)
            .select(trip.id)
            .where(trip.name.like(searchDto.name+"%")
            .orderBy(trip.id)
  }
```

### 속도 향상 (1분 → 1초)
- 기존
    ![org](/media/org.jpg)
    
- 적용 후
    ![after](/media/after.jpg)

## References
https://jojoldu.tistory.com/476
https://jojoldu.tistory.com/529
https://leanpub.com/opinionatedjpa/read
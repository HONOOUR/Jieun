---
title: "Index in mysql"
date: "2022-08-20T22:20:10.284Z"
template: "post"
draft: false
slug: "index-mysal"
category: "Development"
tags:
  - "SQL"
  - "MySQL"
  - "Index"
description: "Index with real mysal chapter 8"
socialImage: ""
---

## 인덱스?

읽기 성능을 향상시키기 위해 사용하고 쓰기 성능 희생 (PK, 보조키)

### 읽어야하는 레코드 수에 따른 효율적인 방법

- 인덱스를 통해 읽는 것이 테이블에서 직접 레코드를 읽는 것 보다 4-5 배 정도 많은 비용
- 전체 레코드에서 20-25% 이하의 양을 읽는 다면 인덱스를 사용하는 것이 효율적

## B-Tree 인덱스를 통한 데이터 읽기

### 인덱스 레인지 스캔

1. Index seek: 인덱스에서 조건을 만족하는 값이 저장된 위치를 찾음
2. Index scan: 1에서 탐색한 위치부터 필요한 만큼 인덱스를 차례로 읽음
3. 2번에서 읽은 인덱스 키와 레코드 주소를 이용해 레코드가 저장된 페이지를 가져오고, 최종 레코드를 읽음

### 인덱스 풀 스캔

인덱스에 포함된 컬럼만 처리하는 경우

### 루스 인덱스 스캔

- min, max 함수에 대해 최적화 하는경우 사용됨
- where 조건에 만족하는 모든 범위를 스캔하지 않고 다음레코드로 넘어감

### 인덱스 스킵 스캔

- MySQL 8.0 부터 옴티마이저가 where 조건절에 index 조합중에 한개 만 있어도 인덱스 검색을 가능하게 해준다
    
    ```sql
    ALTER TABLE employees
    	ADD INDEX ix_gender_birthdate (gender, birth_date)
    
    // 인덱스를 사용 못하는 쿼리
    mysql> SELECT * FROM employees WHERE birth_date>='1965-02-01'
    
    // 인덱스를 사용할 수 있는 쿼리
    mysql> SELECT * FROM employees WHERE gender='M' AND birth_date>='1965-02-01'
    ```
    

## 유니크 인덱스

- Mysql 에서는 인덱스 없이 유니크 제약만 설정할 수 없음
- 테이블이나 인덱스에 같은 값이 2 개 이상 저장될 수 없응

### 인덱스 읽기

- CPU 에서 칼럼값을 비교하는 작업이 있어 유니크하지않은 인덱스와 성능상 차이가 없다
- 유니크하지않은 인덱스가 검색에서 느린것은 읽어야할 레코드 수가 많은것

### 인덱스 쓰기

- 유니크 인덱스는 키 값을 쓸 때 중복된 값이 있는지 체크하는 과정이 더 필요함

## Reference

[http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158392703](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791158392703)
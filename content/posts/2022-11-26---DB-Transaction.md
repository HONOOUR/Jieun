---
title: "DB transaction"
date: "2022-11-26T20:50:10.284Z"
template: "post"
draft: false
slug: "db-transaction"
category: "Development"
tags:
  - "DB"
  - "transaction"
  - "isolation level"
description: "테이블 갱신은 복수 쿼리를 연속적으로 수행하는 경우가 대부분이고 트랜잭션과 잠금이 필요하다."
socialImage: ""
---

테이블 갱신은 복수 쿼리를 연속적으로 수행하는 경우가 대부분이고 트랜잭션과 잠금이 필요하다.

트랜잭션: 복수 쿼리를 한 단위로 묶은 것

## 트랜잭션 특징

### 원자성

데이터의 변경을 수반하는 일련의 데이터 조작이 전부 성공할지 전부 실패할지를 보증하는 구조

### 일관성

일련의 데이터 조작 전후에 그 상태를 유지하는 것을 보증하는 정합성 제약을 추가한다 (예: 사용자 식별을 위한 일련번호)

### 고립성(격리성)

일련의 데이터 조작을 복수 사용자가 동시에 실행해도 각각의 처리가 모순없이 실행되는 것을 보증한다

모순없이 실행되는것 = 복수의 트랜잭션이 순서대로 실행되는 경우와 같은 결과를 얻을 수 있는 상태

(예: MySQL 에서 트랜잭션 처리를 할 때 주로 행 단위의 잠금 기능을 이용)

- DBMS 격리 수준 : 동시에 동작하는 트랜잭션을 1 개 허락하는 직렬화는 성능 문제로 실용적이지 않고 완화한  수준이있음
    - 커밋되지않은 읽기 (Read Uncommitted)
    - 커밋된 읽기 (Read Committed)
    - 반복 읽기 (Repeatable Read)
    - 직렬화 가능 (Serializable)
- 격리 수준 완화 현상
    - dirty read : 트랜잭션이 커밋되기 전에 다른 트랜잭션에서 데이터를 읽는 현상
    - fuzzy/NonRepeatable read : 트랜잭션 이전에 읽어 들인 데이터를 다시 읽어 들일 때, 이전에 읽은 데이터가 다른 현상
    - phantom read : 트랜잭션을 읽을 때 선택할 수 있는 데이터가 나타나거나 사라지는 현상

![isolationlevel](/media/isolationlevel.jpg)

### 지속성

트랜잭션을 완료한 시점부터 장애도 견딜 수 있도록 해야한다. 데이터베이스의 로그 기록 복원 시스템

## 복수 커넥션 읽기 쓰기 테스트

1. create test database
    ![db-1.jpg](/media/db-1.jpg)

    
2. create table  (innoDB type)
    ![db-2.jpg](/media/db-2.jpg)

    
3. show table t1    
    ![db-3.jpg](/media/db-3.jpg)

    
4. 2 개의 MySQL 클라이언트 실행
transaction A, B
    ![db-4.jpg](/media/db-4.jpg)

    ![db-4-1.jpg](/media/db-4-1.jpg)

    ![db-4-2.jpg](/media/db-4-2.jpg)

    
5. 다수 읽기    
    ![db-5-1.jpg](/media/db-5-1.jpg)
    
    ![db-5-2.jpg](/media/db-5-2.jpg)

    
6. 다수 쓰기 (타 트랜잭션 갱신이 끝나기를 기다리다 타임아웃 - 교착 상태)    
    ![db-6-1.jpg](/media/db-6-1.jpg)

    ![db-6-2.jpg](/media/db-6-2.jpg)

    
7. 격리수준에 따른 차이     
    ![db-7.jpg](/media/db-7.jpg)
    
    - Repeatable read - transaction A
        
        복수 쿼리 실행 사이 다른 트랜잭션 커밋이 있어도 반영되지 않음  
        ![db-7-1.jpg](/media/db-7-1.jpg)
        
    - Read committed - transaction B
    복수 쿼리 실행 사이 다른 트랜잭션 커밋이 있다면 최신 커밋된 데이터를 읽음

## Reference

[https://www.alibabacloud.com/blog/comprehensive-understanding-of-transaction-isolation-levels_596894](https://www.alibabacloud.com/blog/comprehensive-understanding-of-transaction-isolation-levels_596894)

[https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)
---
title: "Cassandra"
date: "2022-11-11T20:50:10.284Z"
template: "post"
draft: false
slug: "cassandra"
category: "Development"
tags:
  - "Cassandra"
  - "NoSql"
description: "카산드라 데이터구조, 모델링"
socialImage: ""
---

기본적으로 파티셔닝 기법을 제공함 (분산처리 가능, ordered dictionary) 

### Denormalization

RDB 가 정규화를 통해 Write 성능과 data integrity 를 향상 시키고 Read 성능 감소, 복잡한 쿼리에 대한 단점이 있었다면, 

Cassandra는 비정규화 장점을 따르고, 중복하는 값을 저장 함으로 Read 성능 향상(Write 성능 감소)

### 수평확장 (= 샤딩)

[https://discord.com/blog/how-discord-stores-billions-of-messages](https://discord.com/blog/how-discord-stores-billions-of-messages)

just add nodes to scale it (linear scalability) and it can tolerate a loss of nodes without any impact on the application

Related data is stored contiguously on disk providing minimum seeks and easy distribution around the cluster

### 파티셔닝을 위한 데이터 구조

Cassandra is a KKV store

KK: comprise the primary key (**partition key** + **clustering key**)

partition key: be used to determine **which node the data lives** on and **where it is found on disk** 

한 프라이머리 키에 요청이 짧은 시간에 많으면 예를 들어 iot 처럼 십초에 한번이면 파티션키에 month_year 칼럼을 추가할 수 있다

clustering key: acts as both a primary key within the partition and **how the rows are sorted**

보낸시간(unix time timestamp)에 의해 sorting 된 열을 갖도록 한다 (알림의 경우 시간의 순서가 긴박하지 않음)

## Cassandra Data modeling

[https://youtu.be/_W5VvxzoS6w](https://youtu.be/_W5VvxzoS6w)

1. Analyze user behavior (application needs)
    1. Use case: 유저가 offline → online 일때 자기가 받았던 알림(이벤트)을 시간 순서대로 확인하고 싶음
2. Identify/determine the required workflows (dependencies and needs)
    1. Workflow: 해당 유저가 특정 시점 전까지 받은, 빠른 시간 순서로 정렬된 이벤트를(복수) 찾는다
3. Define them to a number of queries
    1. 유저 이벤트를 유저 index 로 찾는다 (show most recent first) → event_to_user
    
    ```sql
    SELECT * FROM events_to_user WHERE user_idx = <UUID>;
    ```
    
4. Design a table around each query (using denormalization)
    
    ```mermaid
    erDiagram
    	event_to_user {
    		partition_key user_idx
    		cluster creation_datetime 
    		payload  source
    	}
    ```
    
    ```mermaid
    erDiagram
    	event_to_user {
    		long user_idx
    		long creation_datetime 
    		string  source
    	}
    ```
    
    ```sql
    CREATE TABLE IF NOT EXISTS event_to_user {
    		user_idx bigint
    		creation_datetime timestamp
    		source text
    		PRIMARY KEY ((user_idx), creation_datetime)
    } WITH CLUSTERING ORDER BY (creation_datetime DESC);
    ```
    
5. Use BATCH when inserting or updating denormalized data of multiple tables
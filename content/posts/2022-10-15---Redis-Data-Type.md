---
title: "Redis data type"
date: "2022-10-15T22:20:10.284Z"
template: "post"
draft: false
slug: "redis-type"
category: "Development"
tags:
  - "Redis"
  - "DataType"
  - "Java"
description: "레디스 기본 데이터 타입 String, Hash 사용하기"
socialImage: ""
---

## Redis data types

[https://redis.io/docs/data-types/tutorial/](https://redis.io/docs/data-types/tutorial/)

### Strings

Redis strings store sequences of bytes, including text, serialized objects, and binary arrays. As such, strings are the most basic Redis data type.

### Hashes

Redis hashes are record types structured as collections of field-value pairs. You can use hashed to represent basic objects and to store groupings of counters, among other things

## Redis Hashes

[https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#redis.hashmappers.root](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#redis.hashmappers.root)

mapping data to hashes

1. using HashOperations and serializer
2. using Redis Repositories
3. using HashMapper and HashOperations

### HashOperation

- 해쉬에 데이터를 맵핑하기 위해 사용
- serialize 를 같이 사용하지 않으면 redis 에 저장된 key, value 값은 bytes 형태(string 으로 확인하고 싶으면 redisTemplate 를 빈에 등록하는 과정에서 아래를 추가하면 된다)
- StringRedisSerializer 는 RedisSerializer 를 string ↔ bytes 위해 구현
    
    ```java
    // redisConfiguration.java
    public RedisTemplate redisTemplate() {
    		RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
    		redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new StringRedisSerializer());
    		...
    		return redisTemplate;
    }
    ```
    
    [https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#redis:serializer](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#redis:serializer)
    
    > `RedisTemplate` are configured to use Java native serialization. Java native serialization is known for allowing the running of remote code caused by payloads that exploit vulnerable libraries and classes injecting unverified bytecode
    >

## Reference

[https://developer.redis.com/howtos/chatapp/](https://developer.redis.com/howtos/chatapp/)

채팅에서 방과 유저 아이디를 사용해서 키를 만들어 해쉬 데이터 타입을 사용하는 방법
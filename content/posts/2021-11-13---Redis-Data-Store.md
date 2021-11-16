---
title: "Redis Data Store"
date: "2021-11-13T15:10:10.284Z"
template: "post"
draft: false
slug: "redis-data-store"
category: "Development"
tags:
  - "WebSocket"
  - "Spring Boot"
  - "Redis"
description: "chatting with Redis"
socialImage: ""
---

[https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#get-started:first-steps:samples](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#get-started:first-steps:samples)

key-value 스타일 data store

## Redis

in-memory data structure store, database, cache, message broker

### Install Redis

```bash
brew install redis
```

### pom.xml 에 redis 추가

```html
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### Setup Redis

```java
// chatRepository.java
import org.springframework.data.repository.CrudRepository;
public interface ChatRepository extends CrudRepository<ChatRoom, String> {

    @Override
    List<ChatRoom> findAll();

    ChatRoom findByRoomId(String roomId);
}
```

```java
// chatRooms.java
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;

@Getter @Setter
@Builder @AllArgsConstructor @NoArgsConstructor
@RedisHash("ChatRooms")
public class ChatRoom {

    @Id
    private String id;

    private String roomId;

    private String name;

}
```

### Redis database

```
http://localhost:8080/chat/rooms

**[{"id":"3fe1b107-cce6-4cc8-b213-20212ba5ec20","roomId":"c10c1395-36d7-41a2-94b8-479b8742951b","name":"jieun"},
{"id":"220dbdc0-dd05-40f6-b802-3cbc152240e9","roomId":"d8a092fc-524a-4689-8a64-e3e1305a9c6a","name":"jieun_hyogi"},
{"id":"007bee89-e1d3-405e-bf17-4dd7d9570783","roomId":"e6964027-c618-4b15-a4d8-c8232f45c508","name":"hyogi"}]**
```

![chatroom](/media/chatroom_redis.jpg)

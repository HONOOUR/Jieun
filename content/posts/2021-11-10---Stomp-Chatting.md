---
title: "Stomp Chatting"
date: "2021-11-10T15:10:10.284Z"
template: "post"
draft: false
slug: "stomp-chat"
category: "Development"
tags:
  - "WebSocket"
  - "Spring Boot"
  - "STOMP"
description: "chatting with STOMP"
socialImage: ""
---

## STOMP

- a simple, messaging protocol originally created for use in scripting languages with frames inspired by HTTP.
- to address a subset of commonly used messaging patterns
- used over any reliable 2-way streaming network protocol (TCP and WebSocket)
- provide a richer programming model as a sub-protocol
- No need to invent a custom messaging protocol and message format.
- STOMP clients are available including a [Java client](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html#websocket-stomp-client) in the Spring Framework.
- STOMP client communicate with any STOMP message broker

[https://jieun.dev/posts/websocket](https://jieun.dev/posts/websocket)

## Chatting using STOMP

- 채팅방 생성 - publish, subscribe 를 위한 topic 생성
- 채딩방 입장 - topic subscribe 시작
- 채팅방에서 메시지 송수신 - topic으로 publish 또는 subscribe

### WebsocketConfig 설정

메시지 구독 - /subscribe

메시지 발행 - /publish

endpoint - /ws-stomp

```java
// WebsocketConfig.java

@Configuration
@RequiredArgsConstructor
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        registry.enableSimpleBroker("/subscribe");
        registry.setApplicationDestinationPrefixes("/publish");
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws-stomp").setAllowedOriginPatterns("*").withSockJS();
    }
}
```

```html
<!-- roomdetail.ftl -->
<script>
    // websocket & stomp initialize
    var sock = new SockJS("/ws-stomp");

  ws.connect({}, function(frame) {
          ws.subscribe("/subscribe/chat/room/"+vm.$data.roomId, function(message) {
            var recv = JSON.parse(message.body);
              vm.recvMessage(recv);
          });
          ws.send("/publish/chat/message", {}, JSON.stringify({messageType:'ENTER', roomId:vm.$data.roomId, sender:vm.$data.sender}));
    }
</script>
```

### H2 in memory database 사용

```java
// chatService.java

public ChatRoom createRoom(String name) {
        String randomId = UUID.randomUUID().toString();
        ChatRoom chatRoom = ChatRoom.builder()
                .roomId(randomId)
                .name(name)
                .build();
        chatRepository.save(chatRoom);
        return chatRoom;
    }
```

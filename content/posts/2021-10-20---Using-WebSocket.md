---
title: "Using Websocket"
date: "2021-10-20T15:10:10.284Z"
template: "post"
draft: false
slug: "websocket"
category: "Development"
tags:
  - "WebSocket"
  - "Spring Boot"
  - "STOMP"
description: "using websocket or stomp"
socialImage: ""
---

[https://spring.io/guides/gs/messaging-stomp-websocket/](https://spring.io/guides/gs/messaging-stomp-websocket/)

[https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html)

## Use WebSocket

[https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html)

- where the client and server need to exchange events at high frequency and with low latency
- when low latency and high frequency are crucial
- Web socket API is too low level (prefer to use STOMP)

## HTML5 Web Socket

- create a new web socket instance (specification ws:// wss://
- provide it with the URL
- connection during the initial handshake between the client and the server (underlying TCP/IP)

![websocket_flow](/media/websocket_flow.jpg)

## STOMP (Simple Text Orientated Messaging Protocol)

- a simple, messaging protocol originally created for use in scripting languages with frames inspired by HTTP.
- to address a subset of commonly used messaging patterns
- used over any reliable 2-way streaming network protocol (TCP and WebSocket)
- provide a richer programming model as a sub-protocol
- No need to invent a custom messaging protocol and message format.
- STOMP clients are available including a [Java client](https://docs.spring.io/spring-framework/docs/4.3.x/spring-framework-reference/html/websocket.html#websocket-stomp-client) in the Spring Framework.
- Message brokers such as RabbitMQ, ActiveMQ, and others can be used (optionally) to manage subscriptions and broadcast messages.
- Application logic can be organized in any number of `@Controller`'s and messages routed to them based on the STOMP destination header vs handling raw WebSocket messages with a single `WebSocketHandler` for a given connection.
- Use Spring Security to secure messages based on STOMP destinations and message types.
- structure

```
COMMAND
header1:value1
header2:value2

Body^@
```

### Enable STOMP

```java
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;

@Configuration@EnableWebSocketMessageBrokerpublicclass WebSocketConfigimplements WebSocketMessageBrokerConfigurer {

@Overridepublicvoid registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/portfolio").withSockJS();
    }

@Overridepublicvoid configureMessageBroker(MessageBrokerRegistry config) {
        config.setApplicationDestinationPrefixes("/app");
        config.enableSimpleBroker("/topic", "/queue");
    }
}
```

### FLOW

![flow1](/media/socket_flow1.jpg)

with external broker (RabbitMQ)

![flow2](/media/socket_flow2.jpg)

텍스트 기반 메시지 프로토콜

[https://docs.spring.io/spring-integration/reference/html/stomp.html](https://docs.spring.io/spring-integration/reference/html/stomp.html)

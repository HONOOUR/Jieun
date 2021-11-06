---
title: "WebSocket Server - Client "
date: "2021-10-20T15:10:10.284Z"
template: "post"
draft: false
slug: "websocket-server-client"
category: "Development"
tags:
  - "WebSocket"
  - "Spring Boot"
  - "STOMP"
description: "websocket server - client"
socialImage: ""
---

1. 채팅방 만들고 ChatRoom Entity 리턴

   만들어진 각 ChatRoom 은 고유 room id 를 가짐

2. WebSocket Client 를 사용해 WebSocket(ChatMessage) Server에 전달

   ChatMessage - type(ENTER, TALK) & roomId & message를 json 형태 web socket 으로 전달

   그 session을 roomId 를 키로 가지는 해쉬값에 추가해 같은 채팅방에 입장한 모든 세션에 메시지를 전달함

### Create ChatRoom

Post Request by ChatController returns ChatRoom Entity

```java
// ChatController
@RestController
@RequiredArgsConstructor
@RequestMapping("/chat")
public class ChatController {

    private final ChatService chatService;

    @PostMapping
    public ChatRoom createRoom(@RequestParam String name) {
        return chatService.createRoom(name);
    }
}
```

```java
// ChatServie
public ChatRoom createRoom(String name) {
		String randomId = UUID.randomUUID().toString();
    ChatRoom chatRoom = ChatRoom.builder()
    .roomId(randomId)
    .name(name)
    .build();
		return chatRoom
}
```

Postman 으로 테스트

![postman](/media/postman.jpg)

### Websocket Client ↔ Server

WebSocket Handler handles client session and websocket

```java
// WebSocketHanler
@Slf4j
@Component
@RequiredArgsConstructor
public class WebSocketHandler extends TextWebSocketHandler {

    private final ObjectMapper objectMapper;
    private final ChatService chatService;

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        log.info("payload {}", payload);
        ChatMessage chatMessage = objectMapper.readValue(payload, ChatMessage.class);
        chatService.handleAction(chatMessage, session);
    }
}
```

```java
// ChatService
    // Type: Enter -> chatroom(key), sessions(value) 로 chatRoomToSession 해쉬맵에 추가 한다.
    // Type: Talk -> chatMessage에 roomId로 chatroom을 찾고, chatRoom roomId를 키 값으로 갖는 value에 해당하는 모든 세션에 메시지를 발송함
    public void handleAction(ChatMessage chatMessage, WebSocketSession session) {
        MessageType type = chatMessage.getMessageType();
        String roomId = chatMessage.getRoomId();
        if (type == MessageType.ENTER) {
            if (roomIdToSession.get(roomId) != null) {
                HashSet<WebSocketSession> sessions = roomIdToSession.get(roomId);
                sessions.add(session);
                roomIdToSession.put(roomId, sessions);
            } else {
                HashSet<WebSocketSession> sessions = new HashSet<>();
                sessions.add(session);
                roomIdToSession.put(roomId, sessions);
            }
            chatMessage.setMessage("님이 입장했습니다.");
        }
        HashSet<WebSocketSession> sessions = roomIdToSession.get(roomId);
        for (WebSocketSession s: sessions) {
            sendMessage(s, chatMessage);
        }
    }
```

WebSocketClient 로 테스트

[https://chrome.google.com/webstore/search/websocket](https://chrome.google.com/webstore/search/websocket)

![socket_client](/media/socket_client.jpg)

![socket_client_1](/media/socket_client_1.jpg)

### Reference

[https://daddyprogrammer.org/post/4077/spring-websocket-chatting/](https://daddyprogrammer.org/post/4077/spring-websocket-chatting/)

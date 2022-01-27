---
title: "Redis Publish/Subscribe"
date: "2022-01-16T22:10:10.284Z"
template: "post"
draft: false
slug: "redis-pub-sub"
category: "Development"
tags:
  - "Redis"
  - "Chatting"
  - "Web Socket"
description: "publish and subscribe message with redis"
socialImage: ""
---

![chatting](/media/chatting.jpg)

채팅시작 → return room view

채팅 방 개설 또는 입장

채팅 방 개설 (chat/room/{room_name}

```jsx
// room.ftl
createRoom: function() {
	var params = new URLSearchParams();
	axios.post('/chat/room', params)
```

```java
// ChatController.java
@PostMapping("/room")
@ResponseBody
public ChatRoom createRoom(@RequestParam String name) {
    return chatService.createRoom(name);
}
```

채팅 방 입장 (chat/room/enter/{room_id} → return roomdetail view

- Login 한 유저 정보로 token 생성
- 유저가 입장한 채팅방에 들어오는 메시지를 계속 수신하고 있음
- 유저가 입장한 내용을 입장한 채팅방에 발신, 이 때 생성한 토큰값 전달하여 유저 정보를 확인할 수 있도록 함

```jsx
axios.get('/chat/user').then(response => {
    _this.token = response.data.token;
    ws.connect({"token":_this.token}, function(frame) {
				// 채팅방에 들어오는 메시지를 계속 수신
		    ws.subscribe("/subscribe/chat/room/" + _this.roomId, function(message) {
		    var recv = JSON.parse(message.body);
        _this.recvMessage(recv);
        });
				// 유저가 입장한 내용을 입장한 채팅방에 발신
        _this.**sendMessage**('ENTER');
    }
```

```jsx
// 생성한 토큰값 전달하여 유저 정보를 확인
sendMessage: function(type) {
    ws.send("/publish/chat/message", {"token":this.token}, JSON.stringify({messageType:type, roomId:this.roomId, sender:this.sender, message:this.message}));
    this.message = '';
},
```

채팅 방 메시지 발신 (chat/message)

- Publisher : 같은 주제를 구독하고 있는 구독자에 메시지를 publish
- 입장 했을 때 호출
- 메시지 전송 버튼 누를 때 호출

```jsx
// publisher
    @MessageMapping("/chat/message")
    public void sendMessage(ChatMessage chatMessage, @Header("token") String token) {
        String nickname = jwtTokenProvider.getUserNameFromJwt(token);
        chatMessage.setSender(nickname);
        if (chatMessage.getMessageType().equals(MessageType.ENTER)) {
            chatMessage.setMessage(nickname + " 님이 입장하셨습니다.");
        }
        **redisTemplate.convertAndSend(channelTopic.getTopic(), chatMessage);**
    }
```

채팅 방 메시지 수신 (←**redisTemplate.convertAndSend(channelTopic.getTopic(), chatMessage**)

- Subscribe : 메시지를 받으면 처리함
- 메시지를 수신하고 있는 채팅방에 전송하는 역할을 빈으로 등록 (+ RedisSubscriber 객체)

```jsx
class RedisConfig {
// ...
		@Bean
    public MessageListenerAdapter listenerAdapter(RedisSubscriber subscriber) {
        return new MessageListenerAdapter(subscriber, "sendMessage");
    }
//...
}
```

```java
public class RedisSubscriber {
// ...
	public void sendMessage(String publishMessage) {
    try {
      ChatMessage chatMessage = objectMapper.readValue(publishMessage, ChatMessage.class);
	    messageSendingOperations.convertAndSend("/subscribe/chat/room/" + chatMessage.getRoomId(), chatMessage);
    } catch (Exception e) {
	  log.error("Exception {}", e);
	}
}
```

```jsx
created() {
            this.roomId = localStorage.getItem('wschat.roomId');
            this.sender = localStorage.getItem('wschat.sender');
            this.findRoom();
            var _this = this;
            axios.get('/chat/user').then(response => {
                _this.token = response.data.token;
                ws.connect({"token":_this.token}, function(frame) {
                    ws.subscribe(" /subscribe/chat/room/" + _this.roomId, function(message) {
                        var recv = JSON.parse(message.body);
                        _this.recvMessage(recv);
                    });
                    _this.sendMessage('ENTER');
                }, function (error) {
                    alert("서버 연결에 실패했습니다. 다시 접속해 주세요.");
                    location.href="/chat/room";
                });
            });
        },
```

### Reference

[https://daddyprogrammer.org/post/5072/spring-websocket-chatting-server-spring-security-jwt/](https://daddyprogrammer.org/post/5072/spring-websocket-chatting-server-spring-security-jwt/)

---
title: "알림 서비스 디자인 문서"
date: "2023-01-16T20:50:10.284Z"
template: "post"
draft: false
slug: "alarm-service"
category: "Development"
tags:
  - "Alarm"
  - "SpringBoot"
  - "Notification"
  - "Cassandra"
  - "MQ"
description: "알림 서비스는 사용자가 관심 있는 서비스의 상태 변화 정보를 실시간으로 받아볼 수 있는 서비스이다."
socialImage: ""
---
## 개요

- 알림 서비스는 사용자가 관심 있는 서비스의 상태 변화 정보를 실시간으로 받아볼 수 있는 서비스이다.
- 알림은 사용자가 등록한 서비스의 상태 변화가 발생할 때마다 이벤트 큐에 저장한다.
- 이벤트 큐를 소비하는 서비스에서 MQTT를 통해 클라이언트에게 실시간으로 알림을 전달한다.
- 사용자는 언제 어디서든 알림 히스토리를 확인할 수 있으며, 알림 히스토리는 Cassandra에 저장되어 REST API를 통해 반환한다,

## 구조

- 이벤트 큐(redis) 등록: 사용자는 관심 있는 서비스의 상태 변화 정보를 이벤트 큐(redis)에 등록할 수 있음
- 이벤트 큐(redis) 소비: 이벤트 큐(redis)를 소비하는 서비스(spring boot project)를 통해 등록된 정보를 가져와 MQTT 프로토콜을 사용해 클라이언트에게 실시간으로 알림을 전달
- 알림 히스토리(Cassandra) 저장: 알림 서비스는 Cassandra에 알림 히스토리를 저장
- 알림 히스토리 반환(REST API): 사용자는 REST API를 통해 자신의 알림 히스토리를 확인

## 이벤트 소비

Notification service를 스프링부트 프로젝트로 만들어 Redis 큐에 있는 알림을 전송한다. (third party 서비스 TBD)

### Notification service

- 이벤트 큐에 쌓인 이벤트의 타입을 확인하고 타입에 따른 소비자 클래스를 생성해 해당 이벤트를 전달한다.
- 소비자 클래스는 MQTT를 통해 클라이언트에게 알림을 전송하고, Cassandra에 데이터(이벤트 객체-string 타입 event를 JSON 형태로 파싱한 것)를 저장한다.
- 전송 실패한 이벤트는 RETRY_COUNT 만큼 재시도한다.

### Behaviour

1. 쓰래드 5 개의 쓰래드풀을 생성한다. (Bootstrap 클래스에서 쓰래드풀 커스터마이징 가능)
2. 스케줄링에 따라 이벤트 큐 뒤쪽부터 가능한 최대 EVENT_QUEUE_SIZE로 정한 만큼 삭제하고 반환한다.
3. 비동기로 각 쓰레드는 최대 EVENT_QUEUE_SIZE 만큼의 이벤트를 소비자 클래스에 전달한다.
4. 이벤트를 특정 서비스로 전달하며, 실패한 이벤트는 RETRY_COUNT 만큼 재시도한다.

### Diagram
![noti](/media/noti.jpg)
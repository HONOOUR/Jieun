---
title: "Network and Distributed System"
date: "2021-06-01T20:14:02.284Z"
template: "post"
draft: false
slug: "network-distributed-system"
category: "Development"
tags:
  - "Java"
  - "JPA"
  - "Persistence"
  - "Web"
description: "The example shows Entity State with JPA"
socialImage: ""
---

## Network, Distributed System 장점

### 분산 시스템

통신 네트워에 의해 상호 연결된 노드들의 집합

다양한 사이트의 사용자가 정보 교환(단일 컴퓨터 메시지 시스템의 프로세스 간의 전달되는 것과 유사 → 파일 저장, 응용 프로그램 실행 및 원격 프로시저 호출(RPC))

### 분산 시스템 구축 이유

1. 자원 공유 ( Resource Sharing)

   A의 사용자, B 에 구축된 데이터베이스에 질의

2. 계산 속도 향상 (Computation Speedup)

   Load balancing

3. 신뢰성 (Reliability)

   하나의 사이트에서 에러 발생 시

## Network 구조

지리적 분포 방식 차이

WAN, LAN 서로 상호 연결

### LAN (Local Area Network)

- 사무실, 가정
- 여러 다른 컴퓨터, 다양한 공유 장치 및 다른 네트워크에 대한 접근을 제공하는 하나 이상의 라우터(특수 네트워크 통신 프로세서)로 구성
- Ethernet(다중 엑세스 버스)로 구성 → 새 호스트 네트워크에 쉽게 추가
- WiFi 로 구성 → 이더넷 보완 또는 자체적으로 존재, 무선 송, 수신기 가지고 있음

### WAN (Wide Area Network)

- 인터넷(WWW) or 사설
- 전화선, 임대(전용) 회선, 광케이블, 전파 및 위성 채널
- WAN 을 사용하여 지리적으로 분리된 사이트의 호스트가 통신

## 통신 구조

### 이름 지정 및 이름 찾기

예) [eric.cs.yale.edu](http://eric.cs.yale.edu) → 128.148.31.100

1. 시스템 라이브러리 또는 시스템 A의 커널 (edu, org, com...도메인 담당) edu 네임서버에 요청을 보내 [yale.edu](http://yale.edu) 네임서버의 주소 요청
2. edu 네임서버는 [yale.edu](http://yale.edu) 네임서버의 주소 반환
3. 시스템 A 요청을 통해 받은 네임서버를 조회하고 [cs.yale.edu](http://cs.yale.edu) 에 대해 요청보냄
4. 주소가 반환된다. [eric.cs.yale.edu](http://eric.cs.yale.edu) 요청은 해당 호스트의 인터넷 주소 호스트-id(128.148.31.100) 를 반환

### 통신 프로토콜

각 층은 다른 시스템의 동등한 층과 통신

1. 물리 층: 하드웨어에 구현 , 비트 전송
2. 데이터 링크 층: 고정 길이 패킷 처리 무리 주소 간에 프레임 보내고
3. 네트워크 층: 메시지를 패킷으로 나누고, 논리 주소 사이의 연결을 제공, 패킷 라우팅 담당 (IP)
4. 전송 층: 노드 간 메시지 전송, 패킷 순서 유지 흐름 제어 (TCP-UDP)
5. 세션 층: 세션, 프로세스 간 통신 프로토콜 구현
6. 프리젠테이션 층 : 네트워크 간의 형식 차이 해결
7. 응용 프로그램 층: 사용자와 상호 작용, 파일 전송, 원격 로그인 프로토콜 및 이메일 뿐만 아니라 분산 데이터베이스의 스키마 처리 (HTTP, FTP, SSH, DNS, SMTP)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e58aaa7-f159-4cf4-a407-296eb88b365b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e58aaa7-f159-4cf4-a407-296eb88b365b/Untitled.png)

## 네트워크 및 분산 운영체제

### 분산 시스템

- 메모리나 시계를 공유하지 않는 프로세서 모음
- 각 프로세서는 로컬 메모리(다양)를 가지고 고속버스 또는 인터넷 같은 다양한 통신 회선을 통해 서로 통신
- 사용자에게 모든 시스템 자원에 대한 엑세스 제공

  데이터 마이그레이션, 계산 마이그레이션, 프로세스 마이그레이션 → 공유 자원에 대한 접근 제공

### Network Operating System

사용자는 원격 로그인, 원격 자원에 접근할 수 있는 환경 제공 받음 (원격 시스템에서 자신의 시스템으로 데이터 전송)

현재 모든 범용 운영체제 및 Android, IOS

- Remote Login → internet provides ssh protocol ([https://www.ssh.com/academy/ssh](https://www.ssh.com/academy/ssh))
- Remote File Transfer → FTP, SFTP file transfer protocol
- Cloud Storage

### Distributed Operating System

사용자는 로컬 자원에 접근 하는 것과 동일한 방식으로 원격 자원에 접근 (운영체제는 한 사이트에서 다른 사이트로 데이터 및 프로세스 마이그레이션을 제어)

- Date Migration → 자동화된 FTP
- Computation Migration → 원격 시스템에 명령을 내림 (RPC)
- Process Migration →

## 분산 시스템의 설계 문제

견고성

투명성

확장성

## 분산 파일 시스템 (Distributed File System)

분산 컴퓨팅 → WWW, DFS

### Client-Server Model

NFS(Network File System)

### Cluster Model

마스터 메타데이터 서버와 연결된 여러 데이터 서버가 그 데이터 청크를 가지고있음 그리고 메타데이터 서버는 매핑 관계도 가지고있어 장애로 부터 보호함

클라이언트 → 메타데이터 서버 → 데이터 서버 아이디 반환 → 클라이언트 그 아이디에 해당하는 데이터 서버에 접속 파일 정보 수신

GFS(Google File System), HDFS(Hadoop Distributed File System)

- 하드웨어 구성 요소 장애를 일상적으로 예상함
- 시스템에 저장한 파일의 크기가 큼
- 기존 데이터를 덮어쓰지 않고 파일 끝에 새 데이터 추가
- 응용프로그램 및 API를 위 계층에 설계함으로서 융통성 향상 시킬 수 있음 (e,g, MapReduce)

---
title: "AWS - VPC Resource"
date: "2022-05-21T22:20:10.284Z"
template: "post"
draft: false
slug: "vpc-resource"
category: "Development"
tags:
  - "AWS"
  - "VPC"
  - " "
description: "독립된 가상의 클라우드 네트워크"
socialImage: ""
---

## VPC

클라우드 내 논리적으로 독립된 섹션 정의, 가상 네트워크상에서 리소스(IP 대역, 인터페이스, 서브넷, 라우팅 테이블 … )를 생성하고 제어할 수 있음

****CloudNeta-VPC****

IPv4 CIDR: 10.0.0.0/16

![vpc](/media/vpc.jpg)

## Internet Gateway

퍼블릭 서브넷에서 외부 인터넷 구간과 통신

인터넷 게이트웨이 생성, VCP와 연결

**CloudNeta-IGW → CloudNeta-VPC 연결**

IGW ID : [igw-0aa2dd6b1ccb2fc51](https://ap-northeast-2.console.aws.amazon.com/vpc/home?region=ap-northeast-2#InternetGateway:internetGatewayId=igw-0aa2dd6b1ccb2fc51)

## 퍼블릭 서브넷 추가
외부 인터넷 구간과 직접적으로 통신할 수 있는 부분 (공공)네트워크 망  

### Public Subnet

****CloudNeta-Public-SN****

IPv4 CIDR: 10.0.0.0/24

![subnet](/media/subnet.jpg)

Each subnet must reside entirely within one Availability Zone and cannot span zones.

### Public Routing table

*contains a set of rules, called routes, that determine where network traffic from your subnet or gateway is directed.*

1. 퍼블릭 라우팅 생성, 퍼블릭 서브넷과 연결
    
    **CloudNet-Public-RT → CloudNeta-Public-SN 연결** 
    
2. 퍼블릭 라우팅 테이블에 라우팅 경로 추가
    
    모든 네트워크 (0.0.0.0/0) → 인터넷 게이트웨이
    

### Public Subnet 외부 인터넷 통신 확인

1. EC2 인스턴스 퍼블릭 서브넷에 생성

![public-ec2](/media/public-ec2.jpg)

![public-ec2-1](/media/public-ec2-1.jpg)

1. (Putty 사용) SSH로 생성한 EC2(Public IP) 접근, 외부 인터넷 통신 확인

![public-ec2-2](/media/public-ec2-2.jpg)

## 프라이빗 서브넷 추가
외부 인터넷 구간과 직접적인 통신을 할 수 없는 폐쇄적인 네트워크망
### Private Subnet

****CloudNeta-Private-SN****

IPv4 CIDR: 10.0.1.0/24

### NAT Gateway

프라이빗 서브넷에서 외부 인터넷 구간과 통신

위치상으로 퍼블릭 서브넷에 배치 

NAT Gateway ID: nat-05510180b6a5d9eef

![nat](/media/nat.jpg)

### Private Routing Table

1. 프라이빗 라우팅 생성, 프라이빗 서브넷과 연결
    
    **CloudNet-Private-RT → CloudNeta-Private-SN 연결** 
    
2. 프라이빗 라우팅 테이블에 라우팅 경로 추가
    
    모든 네트워크 (0.0.0.0/0) → NAT 게이트웨이 
    

### Private subnet 외부 인터넷 통신 확인

1. EC2 인스턴스 프라이빗 서브넷에 생성 
    
    ![private](/media/private.jpg)
    
    사용자 데이터 입력 ← EC2 인스턴스가 부팅될 때 값을 읽어 적용 
    
    생성한 private EC2 는 퍼블릭 IP 없음
    
    ![private-ec2-1](/media/private-ec2-1.jpg)
    
2. (Putty 사용) SSH로 Public EC2(Public IP) 접근 후 Private  EC2(로컬통신, Private IP) 에 접근, 외부 인터넷 통신 확인
    
    public EC2 with public IP → private EC2 with private IP (e.g. 10.0.1.114)
    
    ![private-ec2-2](/media/private-ec2-2.jpg)
    

## Endpoint

### Gateway Endpoint

*Gateway endpoints provide reliable connectivity to **Amazon S3** and **DynamoDB** without requiring an internet gateway or a NAT device for your VPC. Gateway endpoints do not enable AWS PrivateLink*

![enpoint](/media/endpoint.jpg)

1. 게이트웨이 앤드포인트 생성
    
    ![endpoint1](/media/endpoint1.jpg)
    
2. 검증
    
    ![endpoint2](/media/endpoint2.jpg)
    

### Interface Endpoint (using PrivateLink)

*to connect to services powered by AWS PrivateLink, including many AWS services*

![can not connect to the public server from private EC2](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/396369d0-3513-4d38-893f-8965debdb017/Untitled.png)

can not connect to the public server from private EC2

1. 엔드포인트 전용 DNS 호스트가 생성 (DNS 호스트이름  활성화)
2. 인터페이스 게이트웨이 생성

## Endpoint Service

*사용자가 생성한 VPC와 프라이빗 연결 확인*

타겟그룹 생성 → NLB 에 추가하여 생성 

CloudFormation 사용해서 VPC(MyVPC, CustomVPC) 두개 만들고 

MyVPC 에 public EC2 → CustomVPC public EC2 통신 테스트 ← internet, internet gateway 통신


## Reference

[Classless Inter-Domain Routing - Wikipedia](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)

[https://datatracker.ietf.org/doc/html/rfc4632](https://datatracker.ietf.org/doc/html/rfc4632)

**Classless Inter-Domain Routing**(**CIDR**) is a method for allocating [IP addresses](https://en.wikipedia.org/wiki/IP_address) and for [IP routing](https://en.wikipedia.org/wiki/IP_routing).

[How Amazon VPC works - Amazon Virtual Private Cloud](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html)

[[인터넷 교보문고] 꿈을 키우는 세상 (kyobobook.co.kr)](http://pod.kyobobook.co.kr/podBook/podBookDetailView.ink?barcode=1400000392652&ejkGb=KOR)
---
title: "DB PostgreSQL"
date: "2021-07-04T22:10:10.284Z"
template: "post"
draft: false
slug: "database-postgresql"
category: "Development"
tags:
  - "JAVA"
  - "DataBase"
  - "Role"
description: "데이터베이스, 유저 생성, 권한 설정하기"
socialImage: ""
---

## PostgreSQL 설치

[https://www.postgresql.org/download/](https://www.postgresql.org/download/)

## psql 에 접속

[https://postgresapp.com/](https://postgresapp.com/)

## Create DB and User + 권한 설정

```bash
Last login: Sat Feb 29 19:22:22 on ttys003
➜  ~ psql
psql (12.1)
Type "help" for help.

jieun=# create database testdb;
CREATE DATABASE
jieun=# create user testuser with encrypted password 'testpass';
CREATE ROLE
jieun=# grant all privileges on database testdb to testuser;
GRANT
jieun=#
```

## [application-dev.properties](http://application-dev.properties) 에 DB 정보 설정

dev 프로파일(Profile)용 설정 파일

```bash
//[application-dev.properties](http://application-dev.properties)

spring.datasource.url=jdbc:postgresql://localhost:5432/testdb
spring.datasource.username=testuser
spring.datasource.password=testpass
```

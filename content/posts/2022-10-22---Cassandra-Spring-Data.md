---
title: "Spring boot with docker"
date: "2022-10-15T20:20:10.284Z"
template: "post"
draft: false
slug: "springboot-docker"
category: "Development"
tags:
  - "Springboot"
  - "Docker"
description: "building a Docker image for running a Spring Boot application"
socialImage: ""
---

**Instruction** : [https://spring.io/guides/gs/spring-boot-docker/](https://spring.io/guides/gs/spring-boot-docker/)

**Docker** : Linux container management toolkit, letting users publish container images and consume those published by others

**Docker image**: a recipe for running a containerized process

## Init project - spring boot application

### run app without docker container

`./gradlew build && java -jar build/libs/testApp-0.0.1-SNAPSHOT.jar`
→ [localhost:8080](http://localhost:8080) shows “Hello World from Spring Boot” 

## Containerize it

### 1. create Dockerfile

```bash
FROM openjdk:8-jdk-alpine  ## first image
ARG JAR_FILE=target/*.jar  ## build/*.jar 동일
COPY ${JAR_FILE} ./app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

### 2. build image and tags it as springio/shop-snapshot

```bash
docker build --build-arg JAR_FILE=./build/libs/*.jar -t springio/testApp-snapshot .
```

```bash
PS C:\dev\source\bg-server\bg-shop> docker build --build-arg JAR_FILE=./build/libs/*.jar -t springio/testApp-snapshot .

[+] Building 5.2s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                                                                               0.0s
 => => transferring dockerfile: 31B                                                                                                                0.0s 
 => [internal] load .dockerignore                                                                                                                  0.0s 
 => => transferring context: 2B                                                                                                                    0.0s 
 => [internal] load metadata for docker.io/library/openjdk:8-jdk-alpine                                                                            1.0s 
 => [internal] load build context                                                                                                                  0.0s
 => => transferring context: 173B                                                                                                                  0.0s 
 => [1/2] FROM docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                      3.8s 
 => => resolve docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                      0.0s 
 => => sha256:a3562aa0b991a80cfe8172847c8be6dbf6e46340b759c2b782f8b8be45342717 3.40kB / 3.40kB                                                     0.0s 
 => => sha256:e7c96db7181be991f19a9fb6975cdbbd73c65f4a2681348e63a141a2192a5f10 2.76MB / 2.76MB                                                     0.6s 
 => => sha256:f910a506b6cb1dbec766725d70356f695ae2bf2bea6224dbe8c7c6ad4f3664a2 238B / 238B                                                         0.3s 
 => => sha256:c2274a1a0e2786ee9101b08f76111f9ab8019e368dce1e325d3c284a0ca33397 70.73MB / 70.73MB                                                   2.9s 
 => => sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3 1.64kB / 1.64kB                                                     0.0s 
 => => sha256:44b3cea369c947527e266275cee85c71a81f20fc5076f6ebb5a13f19015dce71 947B / 947B                                                         0.0s 
 => => extracting sha256:e7c96db7181be991f19a9fb6975cdbbd73c65f4a2681348e63a141a2192a5f10                                                          0.1s
 => => extracting sha256:f910a506b6cb1dbec766725d70356f695ae2bf2bea6224dbe8c7c6ad4f3664a2                                                          0.0s
 => => extracting sha256:c2274a1a0e2786ee9101b08f76111f9ab8019e368dce1e325d3c284a0ca33397                                                          0.8s
 => [2/2] COPY ./build/libs/*.jar app.jar                                                                                                          0.1s 
 => exporting to image                                                                                                                             0.2s 
 => => exporting layers                                                                                                                            0.2s 
 => => writing image sha256:130a2e258861b3b64de4ad4c9fcd6d924203e80ccaaed5dcae16a755588747af                                                       0.0s 
 => => naming to docker.io/springio/testApp-snapshot
```

### 3. a spring boot app with just java and a jar file

```bash
docker build -t springio/testApp-snapshot
docker run -p 8080:8080 springio/testApp-snapshot
```

**container name** : 05791b92cae5

![docker1](/media/docker1.jpg)

app.jar 는 컨테이너에 쉘을 실행시켜 접근해서 확인할 수 있음 (alpine image)
[https://spring.io/guides/topicals/spring-boot-docker](https://spring.io/guides/topicals/spring-boot-docker) → **A Basic Dockerfile** section 
[linux - Starting a shell in the Docker Alpine container - Stack Overflow](https://stackoverflow.com/questions/35689628/starting-a-shell-in-the-docker-alpine-container)

```bash
# The alpine base container we used in the example does not have bash, so this is an ash shell. It has some but not all of the features of bash.
docker run -ti --entrypoint /bin/sh myorg/myapp
```

![docker2](/media/docker2.jpg)

### 4. add user privileges and separate dependencies and application resources

```bash
FROM openjdk:8-jdk-alpine
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring
ARG JAR_FILE=build/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

![docker3](/media/docker3.jpg)

- build\dependency 폴더 만들기 `mkdir -p build/dependency`
- unpack jar `jar -xf ../libs/*.jar`
    
    ```bash
    PS C:\dev\source\testApp\build\dependency> jar -xf ..\libs\testApp-0.0.1-SNAPSHOT.jar
    PS C:\dev\source\testApp\build\dependency> jar -xf ..\libs\testApp-0.0.1-SNAPSHOT-plain.jar
    ```
    
    layers.idx → to separate code based on how likely it is to change between application builds 
    e.g. library code is less likely to change between builds and re-use the layers from cache
    
    ```
    - "dependencies":
      - "BOOT-INF/lib/"
    - "spring-boot-loader":
      - "org/"
    - "snapshot-dependencies":
    - "application":
      - "BOOT-INF/classes/"
      - "BOOT-INF/classpath.idx"
      - "BOOT-INF/layers.idx"
      - "META-INF/"
    ```
    
- 도커 실행시켜 확인
    
    ```bash
    docker build --build-arg DEPENDENCY=build/dependency -t springio/shop-snapshot .
    ```
    
    ![docker4](/media/docker4.jpg)
    

### 5. Gradle build

`./gradlew bootBuildImage --imageName=springio/gs-spring-boot-docker`

### 6. Publish port to run (final)

- dockerfile
    
    ```bash
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG JAR_FILE=build/*.jar
    COPY ${JAR_FILE} ./app.jar
    ARG DEPENDENCY=build/dependency
    WORKDIR app
    COPY ${DEPENDENCY}/BOOT-INF/classes BOOT-INF/classes
    COPY ${DEPENDENCY}/BOOT-INF/lib BOOT-INF/lib
    COPY ${DEPENDENCY}/META-INF META-INF
    COPY ${DEPENDENCY}/org org
    
    ENTRYPOINT ["java","-jar","/app.jar"]
    ```
    
- docker build
빌드 인자 두개 이상 일때
    
    ```bash
    docker build --build-arg JAR_FILE=./build/libs/*.jar --build-arg DEPENDENCY=build/dependency -t springio/testApp-snapshot .
    ```
    
    ```bash
    PS C:\dev\source\testApp> docker build --build-arg JAR_FILE=./build/libs/*.jar --build-arg DEPENDENCY=build/dependency -t springio/testApp-snapshot .
    [+] Building 1.0s (13/13) FINISHED
     => [internal] load build definition from Dockerfile                                                                                               0.0s
     => => transferring dockerfile: 32B                                                                                                                0.0s 
     => [internal] load .dockerignore                                                                                                                  0.0s 
     => => transferring context: 2B                                                                                                                    0.0s 
     => [internal] load metadata for docker.io/library/openjdk:8-jdk-alpine                                                                            0.9s 
     => [internal] load build context                                                                                                                  0.0s
     => => transferring context: 14.47kB                                                                                                               0.0s 
     => [1/8] FROM docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                      0.0s 
     => CACHED [2/8] RUN addgroup -S spring && adduser -S spring -G spring                                                                             0.0s 
     => CACHED [3/8] COPY ./build/libs/*.jar ./app.jar                                                                                                 0.0s 
     => CACHED [4/8] WORKDIR app                                                                                                                       0.0s 
     => CACHED [5/8] COPY build/dependency/BOOT-INF/classes BOOT-INF/classes                                                                           0.0s 
     => CACHED [6/8] COPY build/dependency/BOOT-INF/lib BOOT-INF/lib                                                                                   0.0s 
     => CACHED [7/8] COPY build/dependency/META-INF META-INF                                                                                           0.0s 
     => CACHED [8/8] COPY build/dependency/org org                                                                                                     0.0s 
     => exporting to image                                                                                                                             0.0s 
     => => exporting layers                                                                                                                            0.0s 
     => => writing image sha256:1bae517ebc7079a5ab8d7243ff36225b1fc5f20b32b7a31821910fb327f79bd1                                                       0.0s 
     => => naming to docker.io/springio/shop-snapshot                                                                                                  0.0s 
    
    Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
    ```
    
- docker run
    
    ```bash
    PS C:\dev\source\testApp> docker run -p 8080:8080 springio/testApp-snapshot                                                                          
 

## Reference

### **Layering Docker image** 
[https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#container-images.efficient-images.layering](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#container-images.efficient-images.layering)

### **Spring Boot Docker** 
[https://spring.io/guides/topicals/spring-boot-docker](https://spring.io/guides/topicals/spring-boot-docker)
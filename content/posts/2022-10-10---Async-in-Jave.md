---
title: "Async in Java"
date: "2022-10-10T22:20:10.284Z"
template: "post"
draft: false
slug: "async-java"
category: "Development"
tags:
  - "Async"
  - "@EnableAsync"
  - "Java"
description: "쓰레드 풀을 직접 생성하지 않고 Executor를 사용해서 커스터 마이징해서 사용하기"
socialImage: ""
---

The @EnableAsync annotation switches on Spring’s ability to run `@Async` methods in a background thread pool.

```java
@Configuration
 @EnableAsync
 public class AppConfig {

 }
```

[https://spring.io/guides/gs/async-method/](https://spring.io/guides/gs/async-method/)

```java
package com.example.asyncmethod;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@SpringBootApplication
@EnableAsync
public class AsyncMethodApplication {

  public static void main(String[] args) {
    // close the application context to shut down the custom ExecutorService
    SpringApplication.run(AsyncMethodApplication.class, args).close();
  }

  @Bean
  public Executor taskExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(2);
    executor.setMaxPoolSize(2);
    executor.setQueueCapacity(500);
    executor.setThreadNamePrefix("GithubLookup-");
    executor.initialize();
    return executor;
  }
}
```

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html)

Spring will be searching for an associated thread pool definition: either a unique `[TaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/task/TaskExecutor.html)`
 bean in the context, or an `[Executor](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html?is-external=true)` bean named "taskExecutor" otherwise.

## 동시성 in Clean Code

### Executor 프레임워크

- 직접 생성한 스레드 풀 대신 고려해 볼 수 있음
- 스레드 풀 관리, 크기 자동 조정, 재사용
- Future(독립적인 연산 여럿을 실행한 후 모두가 끝나기를 기다릴 때),  Runnable, Callable

```java

Future<String> result = executorService.submit(makeExternalCll);
String partialResult = doSomeLocalProcessing();
return result.get() + partialResult // Future가 끝나기 기다림
```

### Non bloking 방법

다중 스레드 환경에서 값을 안전하게 갱신하기 위해 동기화를 수행

- synchronized
- AtomicBoolean, AtomicInteger, AtomicReference
    
    ```java
    private AtomicInteger value = new AtomicInteger(0);
    
    public void incrementValue() {
    	value.incrementAndGet();
    }
    
    public int getValue() {
    	return value.get();
    }
    ```
    

현대 프로세서는 CAS(compare and swap)을 제공하여 Atomic 연산이 항상 락을 거는 방법(synchronized)보다 빠르다

여러 스레드가 같은 값을 수정해 문제를 일으키는 상황이 잦지 않는 가정에서 효율적으로 감지해 갱신이 성공할 때까지 재차 시도한다. 현재 변수 값이 최종으로 알려진 값인지(메모리에 있는) 확인하고 변수 값을 갱신한다.

```java
// non-blocking code 로 표현
int variableBeingSet;
void simulateNonBlockingSet(int newValue) {
	int currentValue;
	do {
		currentValue = variableBeingSet;
	} while (currentValue != compareAndSwap(currentValue, newValue));

int synchronized compareAndSwap(int currentValue, int newValue) {
	if (variableBeingSet == currentValue) {
		variableBeingSet = newValue;
	return currentValue;
	}
	return variableBeingSet;
}
```


### Reference
[https://en.wikipedia.org/wiki/Compare-and-swap](https://en.wikipedia.org/wiki/Compare-and-swap)

*In [computer science](https://en.wikipedia.org/wiki/Computer_science), **compare-and-swap**(**CAS**) is an [atomic](https://en.wikipedia.org/wiki/Atomic_(computer_science)) [instruction](https://en.wikipedia.org/wiki/Instruction_(computer_science)) used in [multithreading](https://en.wikipedia.org/wiki/Thread_(computer_science)#Multithreading) to achieve [synchronization](https://en.wikipedia.org/wiki/Synchronization_(computer_science)). It compares the contents of a memory location with a given value and, only if they are the same, modifies the contents of that memory location to a new given value.*

[https://spring.io/guides/gs/async-method/](https://spring.io/guides/gs/async-method/)

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableAsync.html)
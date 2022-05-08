---
title: "Non Blocking IO in Java"
date: "2022-05-07T22:20:10.284Z"
template: "post"
draft: false
slug: "non-blocking-io"
category: "Development"
tags:
  - "Java"
  - "Non-Blocking"
  - "IO"
description: "low-level I/O operation with non blocking in Java"
socialImage: ""
---

## Non-blocking I/O

[https://en.wikipedia.org/wiki/Non-blocking_I/O_(Java)](https://en.wikipedia.org/wiki/Non-blocking_I/O_(Java))

The APIs of NIO were designed to provide access to the low-level I/O operations of modern OS

## NIO

[https://docs.oracle.com/javase/10/docs/api/java/nio/package-summary.html](https://docs.oracle.com/javase/10/docs/api/java/nio/package-summary.html)

- New Input Output API package
- java.nio : buffer class
- java.nio.channels : file channel, tcp channel, udp channel
- channel (양방향) ↔  stream (단방향-기존 IO)
- 동기, 비동기 모두 지원
- 블로킹, 넌블로킹 모두 지원

## Non Blocking operation

- connect(), accept(), read(), write() 즉시 리턴됨으로 작업 준비가 된 채널만 선택해서 처리해야함
- Selector 필요  → Channel 작업 처리가 준비된 채널 선택
    - Selector : event listener
    - channel 작업 처리 준비 → selector 에 통보
    - selector → 통보받은 channel 선택
- 작업 스레드가 블로킹되지 않기 때문에 selector가 선택한 채널을 메소드 호출 후 리턴한 하나의 스레드에서 다시 처리함
- 스레드풀을 사용하는 경우, 적은 수의 스레드로 많은 양의 작업 처리

### Selector operation

1. 채널은 자신의 작업 유형을 키(Selection Key)로 생성
    
    selector.register() → selectionKey 생성한 후 관심키셋에 저장
    
2. register the selection key to selector’s interest-set
3. selector detects key’s ready channel in interest-set,  update it to selected-key set 
4. working threads operate the key in selected-key set (작업 스레드가 채널 작업을 처리할 때 SelectionKey필요)
    
    → with SelectionKey, 작업유형 변경, 첨부 객체 저장, 채널 등록 취소
    
    ![selector](/media/selector-operation.jpg)
    

### SelectionKey

- selector.register() 로 생성됨
- 작업 유형 정보를 가짐(읽기, 쓰기,)
- 작업 스레드가 채널작업을 할 때 필요로함
- 채널의 keyFor() 메소드로 얻을 수 있음

### Selector creation

```java
try {
	Selector selector = Selector.open();
} catch (IOException e)
```

### Selector registration

```java
// 어떤 작업에 관심을 둘지 ops
SelectionKey selectionKey = serverSocketChannel.register(Selectot sel, int ops);
SelectionKey selectionKey = socketChannel.register(Selector sel, int ops);
```

### IO Stream Blocking

- input stream → read() → block thread before the data input
- output stream → write() → block thread before the data out
- no interrupt allowed
- large and sequential data

### **Selectors**

- provides a mechanism for waiting on channels and recognizing when one or more become available for data transfer.
- When a number of channels are registered with the selector, it enables blocking of the program flow until **at least one channel is ready for use**, or until an interruption condition occurs. → select() returns
- selector.wakeup(): 작업유형을 변경하고(selectionKey) selector 에 알려주기 위해 사용
- Although this multiplexing behavior could be implemented with threads, the selector can provide a significantly more efficient implementation using lower-level operating system constructs.
- multiplexor of SelectableChannel objects
- created by invoking open method(use the system’s default selector provider to create a new selector)
- remains open until close method
- three sets of selection keys
    - key set : the current registrations of this selector returned by key method
        
        added to a selectors’ key set as a side effect of registering a channel via the channel’s register method
        
    - selected-key set : each key’s channel was detected to be ready for at least one of the operations identified in the key’s interest set during a prior selection operation that adds keys or updates keys in the set returned by selectedKeys method
        
        removed by invoking the remove method
        
    - cancelled-key : the set of keys that have been cancelled but whose channels have not yet been deregistered.
        
        added to selector’s cancelled-key set when it is canceled by closing its channel or invoking its cancel method
        

### Channel Operation in working threads

1. selected-key set 에서 selectionKey를 하나씩 꺼내 작업 유형 확인
2. 작업 유형별(the selectionKey)로 채널 객체 얻기 
    
    ```java
    ServerSocketChannel serverSocketChannel = (ServerSocketChannel) selectionKey.channel();
    ```
    
    selectionKey 의 첨부객체를 얻기도 함
    

### Reference

- [https://developer.android.com/reference/java/nio/channels/Selector](https://developer.android.com/reference/java/nio/channels/Selector)
- [https://developer.android.com/reference/java/nio/channels/ServerSocketChannel](https://developer.android.com/reference/java/nio/channels/ServerSocketChannel)
- [http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968481475](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968481475)
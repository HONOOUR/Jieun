---
title: "Garbage Collection"
date: "2021-09-02T15:10:10.284Z"
template: "post"
draft: false
slug: "garbage-collection"
category: "Development"
tags:
  - ".NET"
  - "Managed"
  - "C#"
description: "Garbage Collection in .NET"
socialImage: ""
---

## Benefits

- 개발자가 Memory 해제를 하지 않아도 됨(unmanaged resource 사용하지 않을 때)
- managed heap에 효율적으로 객체 할당
- 사용하지 않는 객체를 회수하고 메모리 정리 (data field initialization)
- 다른 객체를 사용 중인 메모리에 할당하지 않음

![gg_1](/media/gg_1.jpg)

## Garbage Collection Algorithm

- The list of **active roots** is maintained by the just-in-time (JIT) compiler and common language runtime (CLR)
- Garbage Collector running → 참조 그래프 생성 building graph of all objects reachable from the roots (any object not in the graph are not accessible by the application) → looking for blocks of garbage objects(free space) → shifting the non-garbage objects down in memory(memcpy) / removing all of the gaps in the heap → modifying the application's root for pointer to point to the object's new locations (all pointer to the objects are invalid)

_Roots : storage locations e.g. global, static object pointers, local variable/parameter object pointers on thread's stack(on the managed heap or to null)_

_Every Application has a set of roots._

### Occurrance of GC

- Heap is full
- GC Thread (working threads stop)
- Generates a performance hit
- but managed heap is much faster than C-runtime heap

![gg_2](/media/gg_2.jpg)

## GC Features

### Unmanaged resources

[https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals?redirectedfrom=MSDN](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals?redirectedfrom=MSDN)

- e.g. object that wraps an operating system resources (file, window handle, network connection)
- provide code to clean up (Dispose, safe handle, finalize method)

  [https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/unmanaged](https://docs.microsoft.com/en-us/dotnet/standard/garbage-collection/unmanaged)

### Finalization

- developers explicitly handle resource management and cleanup by implementing Finalize, Close / Dispose
- you create an object and when the object is collected, the object’s Finalize method is called gracefully clean up after itself (properly) when it is being collected.
- GC calls Finalize method (if it exists) and reclaims memory when the garbage object is detected.
- When an application creates a new object, the new operator allocates the memory from the heap. If the object’s type contains a Finalize method, then a pointer to the object is placed on the finalization queue.

```csharp
public class BaseObj {
 public BaseObj() {
 }
 protected override void Finalize() {
 // Perform resource cleanup code here...
 // Example: Close file/Close network connection
 Console.WriteLine("In Finalize.");
 }
}
```

Now you can create an instance of this object by calling:

```csharp
BaseObj bo = new BaseObj();
```

### Finalize internals

1. Create New object
2. The new operator allocates the memory from the heap
3. If the object's type contains a Finalize method, then a pointer to the object is placed on the finalization queue.
4. The finalization queue is an internal data structure controlled by the garbage collector.

### Resurrection

If the object requires finalization, the object(no longer accessing a live object) is considered live again until it is actually finalized

### Generations

- performance improvement
- the newer an object is, the shorter lifetime is expected to be.
- collecting compacting generation 0 object (reclaim a significant amount of space)

  survivors are compacted and considered to be in generation 1(promotion)

- then, collect objects from order generations(1, 2-highest)

Generation 0 : youngest generation and contains short-lived objects

Generation 1 : contains short-lived objects and a buffer

Generation 2 : contains long-lived objects

## Reference

[https://docs.microsoft.com/en-us/archive/msdn-magazine/2000/december/garbage-collection-part-2-automatic-memory-management-in-the-microsoft-net-framework](https://docs.microsoft.com/en-us/archive/msdn-magazine/2000/december/garbage-collection-part-2-automatic-memory-management-in-the-microsoft-net-framework)

[GC_in_NET.pdf](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/aaabfaea-36d6-462b-bf74-8d22cf480079/GC_in_NET.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210906%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210906T151609Z&X-Amz-Expires=86400&X-Amz-Signature=65e02f3c46702c446f91acaef426cee72a187ccabae1a94ded9bc5223f7d5c8f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22GC_in_NET.pdf%22)

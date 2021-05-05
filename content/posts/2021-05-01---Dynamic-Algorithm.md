---
title: "Dynamic Algorithm"
date: "2021-05-01T14:14:02.284Z"
template: "post"
draft: false
slug: "dynamic"
category: "Algorithm"
tags:
  - "Dynamic"
  - "Algorithm"
  - "Knapsack"
  - "Python"
description: "Dynamic Algorithm with python"
socialImage: ""
---

문제를 각각의 작은 문제로 나누어 해결한 결과를 저장해뒀다가 큰 문제의 결과와 합하여 풀이하는 알고리즘

**_Optimal Substructure_**

- A problem is said to have optimal substructure if an optimal solution can be constructed efficiently from optimal solutions of its subproblems
- 어떤 문제의 최적해가 그것의 부분의 최적해로부터 효율적으로 구해질 수 있을 때 그 문제는 optimal substructure를 가진다.
- 최적해의 일부분이 그 부분에 대한 최적해인가?(최단경로 문제, 최단경로의 일부분 또한 최단 경로)
- 순환식은 optimal substructure를 표현한다.

## Dynamic Programming

1. 최적화문제 (optimisation problem) 카운킹(counting)에 적용됨
2. 주어진 문제에 대한 **순환식** 정의 (recurrence equation) 정의
3. 순환식을 Memoization(top-down) or Dynamic(bottom-up) 으로 i

### Knapsack Problem

**_Greedy Algorithm_**

1. 가격이 높은 순서로
2. 무게가 가벼운 것부터
3. 단위 무게당 가격이 높은것 부터

**_Dynamic_**

[https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/DynamicProgramming.py](https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/DynamicProgramming.py)

1. 순환식 **(최대 이익)**

   - 배낭 용량 w, 아이템 i 를 선택하여 최대 이득

     아이템 i 를 선택하는 경우 value(i, w) = max(value(i-1, w-**wi**) + vi, value(i-1, w))

     아이템 i 를 선택하지 않는 경우 value(i, w) = value(i-1, w)

   - if wi < w (배낭 용량)

     value(i, w) = value(i-1, w)

   - otherwise

     value(i, w) = max(value(i-1, w-**wi**) + vi, value(i-1, w))

     max(value(전 아이템, 현 배낭 limit - 현 아이템 무게) + 현 아이템 가치, value(전 아이템, 현 배낭 limt)

2. 상향식

   이차원 배열을 채움

   row ← for i range(len(items) +1)

   col ← for w range(len(limit) +1)

   순차적으로 채워짐으로 이전 값 참조 가능

   ```python
   # 예 (price, weight)
   items = [
   	(1, 1),
   	(6, 2),
   	(18, 5),
   	(22, 6),
   	(28, 7)
   ]
   ```

   예) value(0, 1) = value(0, 2) = ... = 0

   value(1, 0) = value(0, 0) = 0

   value(1, 1) = max(value(0, 0) + 1, value(0, 1)) = 1

   value(2, 1) = value (1, 1) = 1

   value(2, 2) = max(value(1, 0) + 6, value(1, 1)) = 6

   value(2, 3) = max(value(1, 1) + 6, value(1, 3))

**_Time Complexity_**

NP-hard

다항시간 복잡도를 가지는 알고리즘이 존재하지 않을것이라고 여겨짐

O(nW)

W ← 입력크기 (if 입력크기 is 0)

log_2W=k, W=2^k

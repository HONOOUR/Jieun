---
title: Greedy Algorithm
date: "2021-04-17T22:40:32.169Z"
template: "post"
draft: false
slug: "greedy"
category: "Algorithm"
tags:
  - "Algorithm"
  - "Python"
  - "Greedy"
description: "Greedy Algorithm with python"
socialImage: ""
---

휴리스틱 문제 해결 알고리즘([https://ko.wikipedia.org/wiki/휴리스틱\_이론](https://ko.wikipedia.org/wiki/%ED%9C%B4%EB%A6%AC%EC%8A%A4%ED%8B%B1_%EC%9D%B4%EB%A1%A0))

합리적인 판단을 할 수 없는 경우 용이하게 구성된 간편추론 방법

- 앞선 선택을 다시 고려하지 않음
- 부분 문제에 대한 최적 해결 방법 → 문제 해결 방법
- 최적에 가까운 답, 그러나 반드시 최적해는 아님

Examples: Dijkstra, Huffman Coding, Decision Tree, UD3

## Compare to Dynamic Algorithm

**Dynamic Algorithm**

하위 문제에 대한 최적 솔루션 찾아 → 전역 최적 솔루션 선택

**Greedy Algorithm**

각 단계마다 로컬 최적해를 찾아 문제의 크기 작게 줄여나감

가장큰 합, 동전 바꾸기 문제를 풀 수 없음

## Knapsack Problem

<br />

## Queue Reconstruction by height

problem: [https://leetcode.com/problems/queue-reconstruction-by-height/](https://leetcode.com/problems/queue-reconstruction-by-height/)
<br /> solution: https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/QueueReconstructionByHeight.py

1.  Sort people by height in decreasing order

    use max heap by adding minus

    maxheap = [[-7, 0], [-6, 1], [-7, 1], [-4, 4], [-5, 0], [-5, 2]]

2.  Use index to push the person into an array(result)

    person[high, index]

3.  Pop person(from maxheap) and push into the new array - use index - [7,0] - [7,0] [7,1] - [7,0] **[6.1]** [7,1] ← 6 with index 1 - [**5,0]\*\* [7,0] [6.1] [7,1] ← 5 with index 0

            it does not harm the array in the past. The people in the array were taller than 5. Index only affects when the rest are taller.

        - [**5,0]** [7,0] [5,2] [6.1] [7,1] ← 5 with index 2
        - [**5,0]** [7,0] [5,2] [6.1] [4,4] [7,1] ← 4 with index 4

 <br />

## Task Scheduler

problem: [https://leetcode.com/problems/task-scheduler/](https://leetcode.com/problems/task-scheduler/)
<br /> solution: https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/TaskScheduler.py

1. Create a dic of word occurrence (counter)
2. Sort tasks with their occurrence (the n most common tasks)

   ```python
   counter.most_common(n+1) # consider idle time
   ```

3. while True until the counter is empty

   add 1 to result and subtract 1 from the task's value

   if the n most common is 1 less than n+1, add 1 to result for idle time

**Counter object**

_for occurrences of words in a list_

**most_common(n)**

_return a list of the n most common elements_

```python
count = Counter()
for word in word_array:
    count[word] += 1
```

 <br />

## Gas Station

problem: [https://leetcode.com/problems/gas-station/](https://leetcode.com/problems/gas-station/)
<br /> solution: https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/GasStation.py

1. Find the start index which the **cost < gas**
2. Anywhere if the sum of the gas is less than the sum of the cost, return -1 (fail fast return)
3. start index to len(station)

   check if it's positive sum of gas - sum of cost

4. 0 to start index

   check if it's positive sum of gas - sum of cost

- use modulo operation ([https://en.wikipedia.org/wiki/Modulo_operation](https://en.wikipedia.org/wiki/Modulo_operation))

  remainder = index % the number of gas station

  use remainder 0, 1, 2, 3, 4 (← reconstructing indexes)

  <br />

## Assign Cookies

problem: [https://leetcode.com/problems/assign-cookies/](https://leetcode.com/problems/assign-cookies/)
<br /> solution: https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/GasStation.py

1. Sort children, cookies by decreasing order of greed factor
2. Use for loop to check the cookie size is greater than or equal to greed factor

   True → remove the size in the size array and return to the next child

- binary search ([https://en.wikipedia.org/wiki/Bisection_method](https://en.wikipedia.org/wiki/Bisection_method))
- ([https://docs.python.org/3/library/bisect.html](https://docs.python.org/3/library/bisect.html))

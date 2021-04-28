---
title: "BFS - Graph Algorithm"
date: "2021-04-27T20:33:02.284Z"
template: "post"
draft: false
slug: ""
category: "Algorithm"
tags:
  - "Graph"
  - "Algorithm"
  - "BFS"
  - "Python"
description: BFS with python"
socialImage: ""
---

## Graph Search

그래프 각 정점(vertex) 를 방문하는 순회

### BFS (Breadth First Search)

- Queue (방문 차례의 기록을 목적으로 사용)
- Array (방문 정보의 기록을 목적으로 사용)
- Dijkstra
- Shortest Path Problem
- 각 간선의 가중치 합이 최소가 되는 두 정점 사이의 경로를 찾는 문제
- It starts at the tree root
- explorers all of the neighbor nodes at the present depth
- Level order traversal (moves on to the next depth level)

## Graph Adjacency Matrix or List

```jsx
graph = {
	1: [2, 3, 4]
	2: [5],
	3: [5],
	4: [],
	5: [6]
}
```

### Network Delay Time

- 모든 노드가 신호를 받는 데 걸리는 시간
- 모든 노드에 도달할 수 있는지 여부

[https://leetcode.com/problems/network-delay-time/](https://leetcode.com/problems/network-delay-time/)

[https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/NetworkDelayTime.py](https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/NetworkDelayTime.py)

1. create graph adjacency list(dictionary)

   graph[vertex] = [(vertex, weitght)]

2. use priority queue (heapq)

   push (time, vertex) to queue

### Word Transform

- get the shortest path
- create graph and traverse it

[https://programmers.co.kr/learn/courses/30/lessons/43163](https://programmers.co.kr/learn/courses/30/lessons/43163)

[https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/WordTransform.py](https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/WordTransform.py)

1. return -1 if the array(words) doesn't contain target
2. let the word begin(e.g. "hit") start root
3. insert it to the first index of the array words

   words[0] = "hit"

   ```jsx
   words = ["hit", "hot", "dot", "dog", "lot", "log", "cog"];
   ```

4. create graph with the array words

   make each word vertex

   adjacent vertices means having a different letter (the rest are the same)

   ```jsx
   graph = [[1], [0, 2, 4], [1, 3, 4], [2, 5, 6], [1, 2, 5], [3, 4, 6], [3, 5]];
   ```

5. create deque

   it starts from the root and finishes when adjacent vertex is the target

   adds its adjacent vertex and pushes to deque

### Target Number

[https://programmers.co.kr/learn/courses/30/lessons/43165](https://programmers.co.kr/learn/courses/30/lessons/43165)

[https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/TargetNumber.py](https://github.com/HONOOUR/Algorithm_Test/blob/main/Algorithm_Python/TargetNumber.py)

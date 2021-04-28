---
title: "Graph Algorithm"
date: "2021-04-20T21:09:02.284Z"
template: "post"
draft: false
slug: ""
category: "Algorithm"
tags:
  - "Graph"
  - "Algorithm"
  - "DFS"
  - "BFS"
description: Graph
socialImage: ""
---

### Graphs

객체의 일부 쌍들이 '연관되어' 있는 객체 집합 구조

- Topology(위상수학): 연속변환에 대해 불변인 기하학적 객체의 특성을 연구함
- Hamiltonian Cycle: 최단거리(외판원문제)

## Definition

- 세순서쌍 (V(G), E(G), psi_G)
- psi: 접속 함수(incidence funcction), unordered 정점쌍에 대응한다.
  - {{u, v}| u → V(G), v → V(G)}
  - $\psi(e) =uv$ edge e는 정점 u, v 를 연결하고 양 끝점으로 갖는다.
  - $\nu(G)$ (nu, 누) 정점의 개수→order(차수)
  - $\epsilon(G)$ (epsilon, 입실론) 간선 개수→size(크기)
- walk: graph 에서 정점과 에지가 번갈아 등장하는 하나의 열
  시작 정점과 끝 정점이 동일한 워크 → 닫힌 웥크 (closed walk)
- trail: 에지가 중복되지 않는 워크
  정점과 에지가 중복되지않는 닫힌 트레일 → 사이클 (cycle)
- path: 정점이 중복되지 않는 워크
- 이분 그래프 (Bipartite Graph) → 길이가 홀수인 사이클이 존재하지 않는다

## Undirected Graph

(무방향) Graph G=(V, E)
V: 노드(node) 혹은 정점(vertex)
E: 노드쌍을 연결하는 에지(edge) 혹은 링크(link)
object 들 간의 이진관계

## Directed Graph

(Directed) Graph G=(V, E)
edge 는 방향을 가짐
(Weighted) Graph
edge 마다 가중치가 지정
Weighted Graph
Adjacency matrix

## BFS (Breath) 너비 우선탐색

BT level order traversal

- 큐 (다익스트라 알고리즘에 사용됨 - 최단거리)
- It starts at the tree root
- explorers all of the neighbor nodes at the present depth
- moves on to the next depth level
- queue (방문 차례의 기록을 목적으로 사용)
- array (방문 정보의 기록을 목적으로 사용)

```csharp
while the queue is not empty do
	remove a node v from queue; // start with start node
	foreach w in unchecked neighbour of v
		check w and insert w into queue // insert all of the neighbor nodes at the present depth
```

## DFS (Depth) 깊이 우선탐색

BT inorder/preorder/postorder traversal

- 스택으로 구현 (+ 재귀)
- It starts from an arbitrary node
- explores as far as possible along each branch before backtracking.

```csharp
DFS(G, V)
	visited[v] is true
	foreach u adjacent node from v do
		if visited[u] is false
			DFS(G, u)
	end.
end.
```

- 그래프가 disconnected 이거나 혹은 방향 그래프라면 DFS에 의해서 모든 노드가 방문되지 않을 수도 있음
- DFS를 반복하여 모든 노드 방문

```csharp
DFS-ALL(G)
{
	foreach u in v
		visited[u] = No
	foreach u in v
		if (visited[v] == No) then
			DFS(G, u)
}
```

## DAG (Directed Acyclic Graph)

- 방향 사이클이 없는 방향 그래프
- outdegree 가 없는 node 부터 마킹됨
- 예: 작업들의 우선순위

### Topological Ordering (위상정렬)

- a directed graph is a linear ordering of its vertices such that for every directed edge uv (from vertex u to vertex v)
- 모든 에지 (Vi, Vj) 에 대해서 i<j
- indegree: the number of edge incoming to the node
- outdegree: the number of edge outgoing from the node
- incoming → vertex(node) → outgoing
- indegree = 0 인 노드가 존재하지 않는다면? ⇒ topologicalSort_2 algorithm

```csharp
// Algorithm #1
// 첫 번째 노드를 먼저 찾음 또는 incoming edge 가 없는 노드
// 1. Find Indgree == 0
// 2. Remove the vertex and edges from Graph or inset it to an Array A[]
// Repeat 1-2

topologicalSort_1(Graph)
{
	for 1 to n
	{
			u = incomingEdge (indgree ==0)
			A[i] = u;
			Remove u and outgoing nodes.
}
```

```csharp
// Algorithm #2
// 마지막 노드를 먼저 찾음 또는 outgoing edge 가 없는 노드

topologicalSort_2(Graph)
{
	foreach u in v
	{
			visited[u] = NO;
	}
	make an empty linked list R;
	foreach u in v
	{
			if (visited[u] == NO)
			{
					DFS-TS(u, R);
			}
	}
}

DFS-TS(u, R)
{
	foreach u in v
		visited[u] = No
	foreach u in v
		if (visited[v] == No) then
			DFS-TS(u, R)
	R.push(v)
}
```

## Bipartite Graph (이분 그래프)

- vertices can be divided into two disjoint (그래프의 모든 정점이 두 그룹으로 나누어짐)
- every edge connects a vertex in U to one in V, coloring of the graph with two colors.
- not contain any odd-length cycles.
- 인접한 정점끼리 서로 다른 색으로 칠해서 모든 정점을 두 가지 색으로만 칠할 수 있는 그래프

### 이분 그래프 확인 (BFS, DFS)

- 탐색하면서 정점을 방문할 때 마다 두가지 색 중 하나로 칠함
- 인접한 정점은 자신과 다른 색으로 칠한다.
- 인접한 정점이 같은 색이면 안됨

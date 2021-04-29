---
title: Divide and Conquer Algorithm
date: "2021-04-24T22:40:32.169Z"
template: "post"
draft: false
slug: "dvide-and-conquer"
category: "Algorithm"
tags:
  - "Algorithm"
  - "Divide and Conquer"
  - "python"
description: "Divide and Conquer with python"
socialImage: ""
---

분할 정복법

- 재귀를 활용
- 유클리드 알고리즘 (최대공약수)
- 문제를 축소해서 정복
- 분할: 문제를 동일한 유형의 여러 하위 문제로 나눔
- 정복: 가장 작은 단위의 하위 문제를 해결
- 조합: 하위 문제에 대한 결과를 원래 문제에 대한 결과로 조합

### Merge Sort (합병 정렬)

- 분할: 데이터가 저장된 절반으로 나눔 (divide)
- 정복: 각각을 순환적으로 정렬 (recursiveley sort)
- 합병: 정렬된 두 개의 배열을 합쳐 전체를 정렬 (merge)
  임시배열을 만들어 작은 원소를 앞으로 넣은다.

### Quick Sort

- 분할: 배열을 다음과 같은 조건인 만족되도록 두 부분으로 나눈다.
  Pivot, elements in lower parts ≤ elements in upper parts
- 정복: 각각의 작은 문제를 순환적으로 해결

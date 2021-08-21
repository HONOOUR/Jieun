---
title: "Algorithm Solution - Greedy"
date: "2021-08-05T20:10:55.284Z"
template: "post"
draft: false
slug: "solution-greedy-2"
category: "Algorithm"
tags:
  - "Algorithm"
  - "Greedy"
  - "Python"
  - "LeetCode"
description: "LeetCode 그리디 알고리즘 풀이"
socialImage: ""
---

### Container with most water

[https://leetcode.com/problems/container-with-most-water/](https://leetcode.com/problems/container-with-most-water/)

→ 최대 사각형 수조 만들기

1 two pointer

시작, 끝을 기억하는 포인터 두 개를 생성한다.

높이는 두 포인터 중 높이가 작은 것을 할당하기 때문에 다음 루프 때는 둘 중 더 작은 값을 중간 방향으로 이동시킨다.

```python

height = [1,8,6,2,5,4,8,25,7]
# v_lines = [1,8,6,2,5,4,8,3,7]
# v_lines = [2, 1]

def getLargestRetangle(height):
    x1 = 0
    x2 = len(height)-1
    max_rec = 0
    while x1 != x2:
        # compute width
        w = x2 - x1
        # compare height
        # decide what to move
        if height[x1] > height[x2]:
            h = height[x2]
            x2 -= 1
        else:
            h = height[x1]
            x1 += 1

        # max rec
        max_rec = max(max_rec, h * w)

    print(max_rec)
```

### Jump Game

[https://leetcode.com/problems/jump-game/](https://leetcode.com/problems/jump-game/)

→ 배열의 각 원소가 다른 원소를 뛰어넘을 수 있는 값일 때 마지막 원소에 도달할 수 있는지

- [0] 은 True로 간주한다.
- nums[i-1] 이 nums[i]로 점프할 수 있는지를 검사한다. (횟수는 중요하지 않음)

```python
def canJump(self, nums: List[int]) -> bool:
    answer = True
    jump_count = 1
    if len(nums) == 1 and nums[0] == 0:
        return answer
    for i in range(len(nums)-2, -1, -1):
        # 이전에 나오는 수가 다음 수로 점프할수 있는것이 가능한지
        if nums[i] >= jump_count:
            jump_count = 1
            answer = True
            continue
        else:
            jump_count += 1
            answer = False
    return answer
```

### Jump Game ii

[https://leetcode.com/problems/jump-game-ii/](https://leetcode.com/problems/jump-game-ii/)

→ 최소 몇번의 점프로 마지막 원소에 도달할 수 있는지

- 시작지점에서 갈수 있는 구간을 (left, right)로 표시한다.
- 다음 구간을 정할 때 left = right + 1, right = farthest = i + nums[i]
- 매구간을 정할 때 + jump count

```python
def getJumpToLast(nums):
    jump_count = 0
    left = right = 0  # start point
    while right < len(nums):
        farthest = 0
        for i in range(left, right+1):
            farthest = max(farthest, i + nums[i])
        left = right + 1
        right = farthest
        jump_count += 1

    return jump_count
```

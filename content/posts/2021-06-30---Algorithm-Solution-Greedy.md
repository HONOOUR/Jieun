---
title: "Algorithm Solution - Greedy"
date: "2021-06-30T21:12:55.284Z"
template: "post"
draft: false
slug: "solution-greedy-1"
category: "Development"
tags:
  - "Algorithm"
  - "Greedy"
  - "Java"
  - "Programmers"
description: "프로그래머스 그리디 알고리즘 풀이"
socialImage: ""
---

### 큰 수 만들기

[https://programmers.co.kr/learn/courses/30/lessons/42883](https://programmers.co.kr/learn/courses/30/lessons/42883)

- 주어진 숫자의 순서는 바꾸지 않는다
- 앞에서 부터 숫자를 새로 만들 숫자 배열에 넣는다.
- 숫자배열에 넣기전에 배열 마지막의 숫자가 넣을 숫자보다 작다면 제거한다. 그리고 현재 숫자를 넣는다
- 단, 제거할 개수를 초과하면 안된다.

```java
public static String getMaxNumber(String number, int k) {
        String answer = "";
        String[] numbers = number.split("");
        int[] temp = new int[number.length()-k];
        int index = -1;
        int remove = 0;
        if (k < 1) {
            for (String s: numbers) {
                answer += s;
            }
            return answer;
        }
        for (int i = 0; i < numbers.length; i ++) {
            int n = Integer.parseInt(numbers[i]);
            while (remove < k && index >= 0) {
                if (temp[index] < n) {
                    temp[index] = 0;
                    ++ remove;
                    -- index;
                } else {
                    break;
                }
            }
            ++ index;
            temp[index] = n;
        }
        for (int i: temp) {
            answer += String.valueOf(i);
        }

        System.out.println(answer);
        return answer;
}
```

### 단속카메라

[https://programmers.co.kr/learn/courses/30/lessons/42884](https://programmers.co.kr/learn/courses/30/lessons/42884)

1. 진입지점이 빠른 순으로 정렬
2. 최솟값과 최댓값을 다른 차량의 진입 진출과 비교해서 업데이트

   다른 차량과 겹치는 부분이 있을 땐 최솟값을 진입이 더 늦은것으로 최댓값은 진출이 더 빠른것으로 업데이트한다.

3. 범위를 벗어날 때마다 카메라 개수를 하나 씩 더한다.

```java
import java.util.*;

class Solution {
    public int solution(int[][] cars) {
        int answer = 1;
        Arrays.sort(cars, (a, b) -> Integer.compare(a[0], b[0]));

        int min = -30000;
        int max = 30000;
        for (int[] car : cars) {
            if (car[0] >= min && car[1] <= max) {
                min = car[0];
                max = car[1];
            } else if (car[0] > max) {
                min = car[0];
                max = car[1];
                answer += 1;
            } else if (car[0] >= min) {
                min = car[0];
            } else if (car[1] <= max) {
                max = car[1];
            }
        }
        return answer;
    }
}
```

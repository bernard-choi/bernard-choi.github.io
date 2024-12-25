---
layout: post
title: "[Python] Leetcode 70. Climbing Stairs"
subtitle: "[Python] Leetcode 70. Climbing Stairs"
categories: Algorithm
tags: 문제풀이 DP
comments: true


---
## [Leetcode 70 Climbing Stairs](https://leetcode.com/problems/climbing-stairs/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

You are climbing a staircase. It takes n steps to reach the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?



![climbing_stairs_1](https://bernard-choi.github.io/assets/img/post_img/climbing_stairs_1.jpg)


## 풀이

- DP로 풀수 있음.
- Stair3은 Stair1과 Stair2를 더하면 됨. 마찬가지로 Stair4은 Stair2와 Stair3의 합
- 굳이 모든 경우의 수를 저정할 필요 없이 prev, current 2개만 가지고 계산하면 공간복잡도 O(1)에 풀 수 있음

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n == 1:
            return 1
        prev, current = 1, 2
        for i in range(n-2):
            prev, current = current, prev + current

        return current
```

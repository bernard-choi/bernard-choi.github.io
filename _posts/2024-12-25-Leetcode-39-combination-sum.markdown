---
layout: post
title: "[Python] Leetcode 39. Combination Sum"
subtitle: "[Python] Leetcode 39. Combination Sum"
categories: Algorithm
tags: 문제풀이 DFS 백트래킹
comments: true


---
## [Leetcode 39 Combination Sum](https://leetcode.com/problems/combination-sum/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given an array of distinct integers candidates and a target integer target, return a list of all unique combinations of candidates where the chosen numbers sum to target. You may return the combinations in any order.

The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the
frequency
 of at least one of the chosen numbers is different.

The test cases are generated such that the number of unique combinations that sum up to target is less than 150 combinations for the given input.

![combination_sum_1](https://bernard-choi.github.io/assets/img/post_img/combination_sum_1.jpg)


## 풀이

- dfs로 풀 수 있음. 중복된 조합(combination)을 방지하기 위해 dfs인자에 start를 넣고 항상 start 이후부터 시작하도록 한다.

```python
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:

        res = []
        def dfs(temp_sum, answer, start):
            if temp_sum > target:
                return
            elif temp_sum == target:
                res.append(answer)
                return
            for i in range(start, len(candidates)):
                dfs(temp_sum+candidates[i], answer + [candidates[i]], i)
        dfs(0, [], 0)

        return res
```

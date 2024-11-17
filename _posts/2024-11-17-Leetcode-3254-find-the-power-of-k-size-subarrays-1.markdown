---
layout: post
title: "[Python] Leetcode 3254. Find the Power of K-Size Subarrays I"
subtitle: "[Python]  Leetcode 3254. Find the Power of K-Size Subarrays I"
categories: Algorithm
tags: 문제풀이 구현
comments: true


---
## [Leetcode 3254 Find the Power of K-Size Subarrays I](https://leetcode.com/problems/find-the-power-of-k-size-subarrays-i/description/?envType=daily-question&envId=2024-11-16)

## 문제

- You are given an array of integers nums of length n and a positive integer k.

- The power of an array is defined as:
    - Its maximum element if all of its elements are consecutive and sorted in ascending order.
    - -1 otherwise.


- You need to find the power of all subarrays of nums of size k.

- Return an integer array results of size n - k + 1, where results[i] is the power of nums[i..(i + k - 1)].





![find-the-power-of-k-size-subarray-1](https://bernard-choi.github.io/assets/img/post_img/find-the-power-of-k-size-subarray-1.jpg)


## 풀이

- n 변수에 consecutive 횟수를 담습니다.
- consecutive하지 않은 경우 n을 다시 1로 초기화합니다.
- n이 k보다 크거나 같아지면 해당 인덱스를, n이 k보다 작으면 -1을 추가합니다.


```python
class Solution:
    def resultsArray(self, nums: List[int], k: int) -> List[int]:
        if k == 1:
            return nums

        answer = []
        n = 1

        for i in range(1, len(nums)):
            if nums[i - 1] == nums[i] - 1:
                n += 1
            else:
                n = 1

            if n >= k:
                answer.append(nums[i])
            else:
                if i >= k:
                    answer.append(-1)
                elif len(answer) == 0 and i != n - 1:
                    answer.append(-1)
```


---  
layout: post
title: "[Python] Leetcode 53. Maximum Subarray"
subtitle: "[Python] Leetcode 53. Maximum Subarray"  
categories: Algorithm
tags: 문제풀이 자료구조 DP
comments: true  
---  

## [Leetcode 53. Maximum Subarray ](https://leetcode.com/problems/maximum-subarray/)

## 1. 문제

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

A subarray is a contiguous part of an array.


![maximum subarray.jpg](https://yunsikus.github.io/assets/img/post_img/maximum_subarray.jpg)

## 설명

- 브루트포스로 풀 경우 시간초과 발생
- a_sum이라는 리스트를 만들고, 차례대로 탐색하며 accumulated sum이 더 커지는지 비교한다. 
  - 만약 accumulated sum이 더 크면 a_sum에 더해준다. 
  - 작으면 그 값을 그대로 a_sum에 넣는다. 
  - max(nums[i], a_sum[-1]+nums[i])

## 풀이

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        a_sum = [nums[0]]
        max_v = nums[0]
        for i in range(1,len(nums)):
            hubo = max(nums[i], a_sum[-1]+nums[i])
            a_sum.append(hubo)
            if hubo > max_v:
                max_v = hubo
        return max_v
```
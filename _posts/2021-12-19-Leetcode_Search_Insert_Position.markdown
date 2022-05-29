---  
layout: post
title: "[Python] Leetcode 35. Search Insert Position"
subtitle: "[Python] Leetcode 35. Search Insert Position"  
categories: Algorithm
tags: 문제풀이 자료구조 binary_search 이분탐색
comments: true  
---  

## [Leetcode 35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

## 1. 문제

Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You must write an algorithm with `O(log n)` runtime complexity.

![search_insert_position.jpg](https://yunsikus.github.io/assets/img/post_img/search_insert_position.jpg)

## 설명

- 이분탐색을 통해 탐색의 시간 복잡도를 `O(logN)`으로 줄일 수 있습니다. 
- 만약 `target`이 없는 경우의 인덱스는 while 반복문이 종료된 후 `lower`값을 리턴하면 됩니다.
    - 반복문을 탈출하는 마지막 조건에서 2가지 종류가 있다. 
    - 1 `mid`가 `target`보다 작은 경우
        - nums = [1,3,5], target = 6인 경우 lower=3, upper=2이 된 후 반복문이 종료된다. 
    - 2 `mid`가 `target`보다 큰 경우
        - nums = [1,3,5], target = 4인 경우 lower=2, upper=1이 된 후 반복문이 종료된다.
    - 모든 경우 insert의 위치는 `lower`가 된다. 

## 풀이

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        lower = 0
        upper = len(nums)-1
        while lower <= upper:
            mid = (lower + upper)//2
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                lower=mid+1
            else:
                upper=mid-1
        return lower
```
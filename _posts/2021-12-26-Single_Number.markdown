---  
layout: post
title: "[Python] Leetcode 136. Single Number"
subtitle: "[Python] Leetcode 136. Single Number"  
categories: Algorithm
tags: 문제풀이 자료구조 Hashmap Hash XOR
comments: true  
---  

## [Leetcode 136. Search Insert Position](https://leetcode.com/problems/single-number/)

## 1. 문제

Given a non-empty array of integers nums, every element appears twice except for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only constant extra space.

![Single_Number.jpg](https://yunsikus.github.io/assets/img/post_img/Single_Number.jpg)

## 2. 풀이

1) Hashmap 사용 - 시간복잡도: `O(N)`, 공간복잡도: `O(N)`
- python set을 사용

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        my_set = set()
        for n in nums: # O(N)
            if n in my_set:
                my_set.discard(n)
            else:
                my_set.add(n)
        return list(my_set)[0]
    
```

2) XOR 사용 - 시간복잡도: `O(N)`, 공간복잡도: `O(1)`
- XOR 연산은 비트 연산을 하여 서로 다르면 1, 같으면 0이 됨

```
1 ^ 0 = 1
0 ^ 1 = 1
0 ^ 0 = 0
1 ^ 1 = 0
```
예를 들어 [2,2,1]이면 10^10 = 0이 되고 0^1은 1 즉 나머지 원소가 남게 됩니다. 
[2,1,2] 처럼 순서가 바뀐 경우에도 XOR연산은 교환법(commutative)이 성립하므로 위와 동일합니다. 

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        result = 0
        for n in nums: 
            result ^= n
        return result
```

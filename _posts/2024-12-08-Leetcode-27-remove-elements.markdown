---
layout: post
title: "[Python] Leetcode 27. Remove Element"
subtitle: "[Python] Leetcode 27. Remove Element"
categories: Algorithm
tags: 문제풀이 투포인터 Two-pointer
comments: true


---
## [Leetcode 27 Remove Element](https://leetcode.com/problems/remove-element/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given an integer array nums and an integer val, remove all occurrences of val in nums in-place. The order of the elements may be changed. Then return the number of elements in nums which are not equal to val.

Consider the number of elements in nums which are not equal to val be k, to get accepted, you need to do the following things:

Change the array nums such that the first k elements of nums contain the elements which are not equal to val. The remaining elements of nums are not important as well as the size of nums.
Return k.
Custom Judge:


![remove_elements_1](https://bernard-choi.github.io/assets/img/post_img/remove_elements.jpg)


## 풀이

- reader와 writer 2개의 포인터를 정의하고 투포인터로 풀 수 있음.
- Time Complexity O(n): i, j 최대 2n 씩 순회
- Complexity O(1)


```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i = 0 # writer
        for j in range(len(nums)): # reader
            if nums[j] != val:
                nums[i] = nums[j]
                i += 1
        return i
```


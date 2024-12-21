---
layout: post
title: "[Python] Leetcode 80. Remove Duplicates from Sorted Array II"
subtitle: "[Python] Leetcode 80. Remove Duplicates from Sorted Array II"
categories: Algorithm
tags: 문제풀이 투포인터 Two-pointer
comments: true


---
## [Leetcode 80 Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same. Then return the number of unique elements in nums.

Consider the number of unique elements of nums to be k, to get accepted, you need to do the following things:

Change the array nums such that the first k elements of nums contain the unique elements in the order they were present in nums initially. The remaining elements of nums are not important as well as the size of nums.
Return k.


![remove_duplicates_from_sorted_array_2](https://bernard-choi.github.io/assets/img/post_img/remove_duplicates_from_sorted_array_2.jpg)


## 풀이

- 가장 간단한 풀이는 string을 뒤집어서 일치하는지 확인
    - Time Complexity는 string s를 순회하므로 O(n).
    - Space Complexity는 뒤집은 s를 담아야하므로 O(n)
- 투 포인터를 이용하면 Space Complexity O(1)으로 풀 수 있음.


```python

class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        i = 1
        count = 1
        val = nums[0]

        for j in range(1,len(nums)):
            if nums[j] != val:
                nums[i] = nums[j]
                i += 1
                val = nums[j]
                count = 1

            elif nums[j] == val and count <= 1:
                nums[i] = nums[j]
                i += 1
                count += 1

        return i
```


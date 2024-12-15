---
layout: post
title: "[Python] Leetcode 88. Merge Sorted Array"
subtitle: "[Python] Leetcode 88. Merge Sorted Array"
categories: Algorithm
tags: 문제풀이
comments: true


---
## [Leetcode 88 Merge Sorted Array](https://leetcode.com/problems/remove-duplicate-letters/description/)

## 문제

You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.




![merge_sorted_array_1](https://bernard-choi.github.io/assets/img/post_img/merge_sorted_array.jpg)


## 풀이

- 간단한 풀이는 두 리스트를 합치고 sort 하는 것. 
    - 그러나 이 풀이는 Time Complexity: O((n+m)log(n+m)), Space Complexity: O(n) 
    - 대부분의 프로그래밍 언어의 built-in sorting algorithm은 O(n)
-  투포인터로 풀면 Time Complexity: O(n+m), Space Complexity: O(m)
   -  m 길이의 추가 배열을 선언하기 때문 (nums1_copy)


```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        i = 0
        j = 0
        nums1_copy = nums1[:m+n]
        for p in range(m+n):
            if j >= n or (i < m and nums1_copy[i] <= nums2[j]):
                nums1[p] = nums1_copy[i]
                i += 1
            else:
                nums1[p] = nums2[j]
                j += 1

        return
```


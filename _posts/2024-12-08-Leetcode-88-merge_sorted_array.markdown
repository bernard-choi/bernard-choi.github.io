---
layout: post
title: "[Python] Leetcode 88. Merge Sorted Array"
subtitle: "[Python] Leetcode 88. Merge Sorted Array"
categories: Algorithm
tags: 문제풀이
comments: true


---
## [Leetcode 125 Valid Parlindrome](https://leetcode.com/problems/remove-duplicate-letters/description/)

## 문제

You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.




![merge_sorted_array_1](https://bernard-choi.github.io/assets/img/post_img/merge_sorted_array.jpg)


## 풀이

- 가장 간단한 풀이는 string을 뒤집어서 일치하는지 확인
    - Time Complexity는 string s를 순회하므로 O(n).
    - Space Complexity는 뒤집은 s를 담아야하므로 O(n)
- 투 포인터를 이용하면 Space Complexity O(1)으로 풀 수 있음.


```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        start = 0
        end = len(s)-1
        while start < end:
            while start < end and not s[start].isalnum():
                start += 1
            while start < end and not s[end].isalnum():
                end -= 1

            if s[start].lower() != s[end].lower():
                return False

            start += 1
            end -= 1
        return True
```


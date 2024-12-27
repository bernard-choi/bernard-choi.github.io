---
layout: post
title: "[Python] Leetcode 28. Find-the-index-of-the-first-occurence-in-a-string"
subtitle: "[Python] Leetcode 28. Find-the-index-of-the-first-occurence-in-a-string"
categories: Algorithm
tags: 문제풀이 Array
comments: true


---
## [Leetcode 28 Find the Index of the First Occurrence in a String](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given two strings needle and haystack, return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

![find_the_index_of_the_first_occurence_in_a_string_1](https://bernard-choi.github.io/assets/img/post_img/find_the_index_of_the_first_occurence_in_a_string_1.jpg)


## 풀이
- 시간복잡도: O((n-m+1)*m) = O(nm)
- 공간복잡도: O(1)

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        m = len(haystack)
        n = len(needle)
        for i in range(m-n+1):
            answer = 0
            for j in range(n):
                if haystack[i+j] != needle[j]:
                    break
                if j == n - 1:
                    return i
        return -1
```

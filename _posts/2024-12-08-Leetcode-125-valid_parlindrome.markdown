---
layout: post
title: "[Python] Leetcode 125. Valid Parlindrome"
subtitle: "[Python] Leetcode 125. Valid Parlindrome"
categories: Algorithm
tags: 문제풀이 투포인터 Two-pointer
comments: true


---
## [Leetcode 125 Valid Parlindrome](https://leetcode.com/problems/remove-duplicate-letters/description/)

## 문제

A phrase is a palindrome if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string s, return true if it is a palindrome, or false otherwise



![valid_parlindrome_1](https://bernard-choi.github.io/assets/img/post_img/valid_parlindrome.jpg)


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


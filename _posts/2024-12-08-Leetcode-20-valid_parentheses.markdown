---
layout: post
title: "[Python] Leetcode 20. Valid Parentheses"
subtitle: "[Python] Leetcode 20. Valid Parentheses"
categories: Algorithm
tags: 문제풀이 스택 Stack
comments: true


---
## [Leetcode 20 Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

Open brackets must be closed by the same type of brackets.
Open brackets must be closed in the correct order.
Every close bracket has a corresponding open bracket of the same type.


![valid_parentheses_1](https://bernard-choi.github.io/assets/img/post_img/valid_parentheses.jpg)


## 풀이

- s를 한번 순회하므로 Time Complexity: O(n)
- 가장 최악의 경우 열리는 괄호를 모두 stack에 넣을 수 있으므로 Space Complexity: O(n)



```python
class Solution:
    def isValid(self, s: str) -> bool:
        my_table = {
            '(':')',
            '{':'}',
            '[':']'
        }
        stack = []
        for char in s:
            if char in my_table:
                stack.append(char)
            elif not stack or my_table[stack.pop()] != char:
                return False

        return len(stack) == 0
```


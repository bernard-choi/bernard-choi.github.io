---
layout: post
title: "[Python] Leetcode 316. Remove Duplicated Letters"
subtitle: "[Python] Leetcode 316. Remove Duplicated Letters"
categories: Algorithm
tags: 문제풀이 구현
comments: true


---
## [Leetcode 316 Remove Duplicated Letters](https://leetcode.com/problems/remove-duplicate-letters/description/)

## 문제

Given a string s, remove duplicate letters so that every letter appears once and only once. You must make sure your result is
the smallest in lexicographical order
 among all possible results.




![remove_duplicate_letters_1](https://bernard-choi.github.io/assets/img/post_img/remove_duplicate_letters_1.jpg)


## 풀이

- counter에서 각 알파벳별 빈도를 저장합니다. stack에서 기존에 쌓인 알파벳을 제거하고 뒤에 붙일 수 있는지 확인용으로 필요합니다.
- seen이라는 set에 이미 사용한 알파벳인 경우 추가합니다.
- stac에는 사용할 알파벳을 저장합니다.
  - 이전에 쌓인 알파벳이 현재 쌓으려는 알파벳보다 크면, 뒤에 쌓는 것이 유리합니다.
  - counter에서 뒤에 쌓을 알파벳이 남아있는지 확인 후, 남아있다면 이전에 쌓인 알파벳을 제거합니다.


```python
from collections import Counter

class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        counter, seen, stack = Counter(s), set(), []
        for char in s:
            counter[char] -= 1
            if char in seen:
                continue
            # 뒤에 붙일 문자가 남아 있다면 스택에서 제거
            while stack and char < stack[-1] and counter[stack[-1]] > 0: # 현재 문자 char가 스택의 마지막 문자보다 더 작고, 스택의 마지막 문자가 뒤에서 다시 나타난다면, 스택에서 마지막 문자를 제거
                seen.remove(stack.pop())
            stack.append(char)
            seen.add(char)
        return ''.join(stack)
```


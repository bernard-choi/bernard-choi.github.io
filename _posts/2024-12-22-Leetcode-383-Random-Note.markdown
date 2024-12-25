---
layout: post
title: "[Python] Leetcode 383. Ransom Mote"
subtitle: "[Python] Leetcode 383. Ransom Note"
categories: Algorithm
tags: 문제풀이 Array
comments: true


---
## [Leetcode 383 Ramsom Note](https://leetcode.com/problems/ransom-note/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given two strings ransomNote and magazine, return true if ransomNote can be constructed by using the letters from magazine and false otherwise.

Each letter in magazine can only be used once in ransomNote.


![ransom_note_1](https://bernard-choi.github.io/assets/img/post_img/ransom_note_1.jpg)


## 풀이

- magazine의 글자를 바탕으로 ransomNote를 작성 가능한지 리턴해야 함.
- magazine의 글자 및 빈도를 hashmap에 담고 ransomNote를 순회하며 hashmap에 키가 있는지 없으면 바로 False를 리턴. 있으면 빈도를 하나 뺀다.

- 시간복잡도는 ransomNote를 순회하며 hashmap을 생성하는데 O(m)
- 공간복잡도는 O(k)인데 k는 최대 26개로 O(1)

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        my_dict = {}
        for i in magazine:
            if i not in my_dict:
                my_dict[i] = 1
            else:
                my_dict[i] += 1


        for i in ransomNote:
            if i not in my_dict or my_dict[i] == 0:
                return False
            else:
                my_dict[i] -= 1

        return True
```

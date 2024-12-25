---
layout: post
title: "[Python] Leetcode 205. Isomorphic Strings"
subtitle: "[Python] Leetcode 205. Isomorphic Strings"
categories: Algorithm
tags: 문제풀이 Array Hashmap
comments: true


---
## [Leetcode 205 Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given two strings s and t, determine if they are isomorphic.

Two strings s and t are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character, but a character may map to itself.


![isomorphic_strings_1](https://bernard-choi.github.io/assets/img/post_img/isomorphic_strings_1.jpg)


## 풀이

- s -> t, t -> s 로 매핑정보를 담고 있는 hashmap 생성 (`mapping_s_t`, `mapping_t_s`)
- 만약 매핑정보가 없다면 각각 추가해줌.
- 매핑 정보가 이미 있다면, 현재 넣으려는 매핑m정보와 같은지 검증. 다르면 false

```python
class Solution:
    def isIsomorphic(self, s: str, t: str) -> bool:
        mapping_s_t = {}
        mapping_t_s = {}

        for s1, s2 in zip(s,t):
            if s1 not in mapping_s_t and s2 not in mapping_t_s:
                mapping_s_t[s1] = s2
                mapping_t_s[s2] = s1
            else:
                if mapping_s_t.get(s1) != s2 or mapping_t_s.get(s2) != s1:
                    return False

        return True
```

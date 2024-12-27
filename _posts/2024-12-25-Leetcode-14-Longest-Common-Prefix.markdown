---
layout: post
title: "[Python] Leetcode 14. Last Common Prefix"
subtitle: "[Python] Leetcode 14. Last Common Prefix"
categories: Algorithm
tags: 문제풀이 Heap
comments: true


---
## [Leetcode 14 Kth Last Common Prefix](https://leetcode.com/problems/longest-common-prefix/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

![longest_common_prefix_1](https://bernard-choi.github.io/assets/img/post_img/longest_common_prefix_1.jpg)


## 풀이

- 시간복잡도: O(S). S는 모든 string의 character 개수
- 공간복잡도: O(1)

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        answer = ''
        for i in range(len(strs[0])):
            for string in strs:
                if i == len(string) or strs[0][i] != string[i]:
                    return answer
            answer += strs[0][i]
        return answer
```

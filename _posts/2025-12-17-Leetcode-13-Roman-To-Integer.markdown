---
layout: post
title: "[Python] Leetcode 13. Roman to Integer"
subtitle: "[Python] Leetcode 13. Roman to Integer"
categories: Algorithm
tags: 문제풀이 
comments: true


---
## [Leetcode 13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/description/)

## 문제

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
For example, 2 is written as II in Roman numeral, just two ones added together. 12 is written as XII, which is simply X + II. The number 27 is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

I can be placed before V (5) and X (10) to make 4 and 9. 
X can be placed before L (50) and C (100) to make 40 and 90. 
C can be placed before D (500) and M (1000) to make 400 and 900.
Given a roman numeral, convert it to an integer.


![roman_to_integer](https://bernard-choi.github.io/assets/img/post_img/roman_to_integer.png)


## 풀이

로마 숫자는 일반적으로 큰 값에서 작은 값 순서로 왼쪽에서 오른쪽으로 배치됩니다. 하지만 IV(4), IX(9)처럼 작은 값이 큰 값 앞에 오면 뺄셈을 의미합니다.

이 규칙을 활용하면:
- 현재 문자의 값이 다음 문자의 값보다 작으면 → 빼기
- 그렇지 않으면 → 더하기

```python
class Solution:
    def romanToInt(self, s: str) -> int:
        roman = {
            'I': 1,
            'V': 5,
            'X': 10,
            'L': 50,
            'C': 100,
            'D': 500,
            'M': 1000
        }

        result = 0
        for i in range(len(s)):
            # 현재 값이 다음 값보다 작으면 빼기 (IV, IX 등의 케이스)
            if i + 1 < len(s) and roman[s[i]] < roman[s[i + 1]]:
                result -= roman[s[i]]
            else:
                result += roman[s[i]]

        return result
```

- 시간복잡도: O(n) - 문자열을 한 번 순회
- 공간복잡도: O(1) - 고정된 크기의 딕셔너리만 사용

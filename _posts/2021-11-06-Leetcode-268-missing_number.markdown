---
layout: post
title: "[Python] Leetcode 268 Missing number"
subtitle: "[Python] Leetcode 268 Missing number"
categories: Algorithm
tags: 문제풀이 구현
comments: true


---
## [Leetcode 268 Missing number](https://leetcode.com/problems/missing-number/)

## 문제

Given an array nums containing n distinct numbers in the range [0, n], return the only number in the range that is missing from the array.

![missing_number1](https://yunsikus.github.io/assets/img/post_img/missing_number1.jpg)


## 풀이

다양한 방법으로 풀이가 가능합니다.

1. 정렬방법

**시간복잡도** : `O(n * log(n))`
**공간복잡도** : `O(1)`

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        # Sort the array and compare the element with index of the given array

        nums.sort()
        for i in range(len(nums)):
            if i != nums[i]:
                return i
        return len(nums)
```

2. 해시 테이블
- False로 된 리스트를 생성하고 nums 리스트의 각 원소 인덱스마다 True로 바꿔준다.
  
**시간복잡도** : `O(n)`
**공간복잡도** : `O(S)` 


```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n = len(nums)+1
        my_list = [False]*n
        for num in nums:
            my_list[num] = True

        for i in range(len(my_list)):
            if not my_list[i]:
                return i
```

3. 가우스 합
- nums의 전체 합에서 가우스 합(n*(n+1)/2)을 뺀다.

**시간복잡도** : `O(n)`
**공간복잡도** : `O(1)` 

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n = len(sum)
        gauss_sum = n*(n+1)/2
        array_sum = sum(nums)
        return gauss_sum - array_sum
```






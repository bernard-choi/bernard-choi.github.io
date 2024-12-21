---
layout: post
title: "[Python] Leetcode 189. Rotate Array"
subtitle: "[Python] Leetcode 189. Rotate Array"
categories: Algorithm
tags: 문제풀이 Array
comments: true


---
## [Leetcode 189 Rotate Array](https://leetcode.com/problems/rotate-array/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given an integer array nums, rotate the array to the right by k steps, where k is non-negative.



![rotate_array_1](https://bernard-choi.github.io/assets/img/post_img/rotate_array_1.jpg)


## 풀이

- 먼저 O(1)로 푸는 방법은, 브루트포스로 k번 len(n)만큼 순회하면서 이동시키는 방법임.
    - 다만 이 방법은 시간 복잡도 O(N x K)

```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        k %= len(nums)
        for i in range(k):
            previous = nums[-1]
            for j in range(len(nums)):
                nums[j], previous = previous, nums[j]
        return
```

- 공간복잡도 O(N)으로 풀 수도 있음. 같은 배열의 크기만큼 추가 배열이 필요. 배열의 길이만큼 추가로 넣어야 하는 시간이 필요하여 시간복잡도 O(N)


```python
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        k %= len(nums)
        if k == 0:
            return

        nums[:k], nums[k:] = nums[-k:], nums[:-k]

        return
```

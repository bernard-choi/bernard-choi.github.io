---
layout: post
title: "[Python] Leetcode 215. Kth Largest Element in an Array"
subtitle: "[Python] Leetcode 215. Kth Largest Element in an Array"
categories: Algorithm
tags: 문제풀이 Heap
comments: true


---
## [Leetcode 215 Kth Largest Element in an Array
](https://leetcode.com/problems/kth-largest-element-in-an-array/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

Given an integer array nums and an integer k, return the kth largest element in the array.

Note that it is the kth largest element in the sorted order, not the kth distinct element.

Can you solve it without sorting?

![ransom_note_1](https://bernard-choi.github.io/assets/img/post_img/kth_largest_element_in_an_array_1.jpg)


## 풀이

- heap 자료구조를 이용하여 정렬없이 풀 수 있음
- nums의 각 원소를 heap 자료구조에 push. 이때 길이가 k 이상이면 다시 pop
- 이 방식으로 진행하면, 가장 크기가 큰 K개의 원소가 남게 된다.
- my_heap의 첫번째 원소가 k번째 큰 원소에 해당한다.
  - 마지막 원소는 가장 큰 원소에 해당.
  - python heapq 모델에서는 default로 minheap을 지원하기 때문.

```python
import heapq
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        my_heap = []
        for n in nums:
            heapq.heappush(my_heap, n)
            if len(my_heap) > k:
                heapq.heappop(my_heap)

        return my_heap[0]

```

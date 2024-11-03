---
layout: post
title: "[Python] Leetcode 26. Remove Duplicated from Sorted Array"
subtitle: "[Python] Leetcode 26. Remove Duplicated from Sorted Array"
categories: Algorithm
tags: 문제풀이 구현
comments: true


---
## [Leetcode 26 Remove Duplicated from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/)

## 문제

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same. Then return the number of unique elements in nums.

Consider the number of unique elements of nums to be k, to get accepted, you need to do the following things:

- Change the array nums such that the first k elements of nums contain the unique elements in the order they were present in nums initially. The remaining elements of nums are not important as well as the size of nums.

- Return k.


![missing_number1](https://bernard-choi.github.io/assets/img/post_img/remove_duplicate_number_1.jpg)


## 풀이

- 업데이트할 인덱스 `j`를 1로 둔다. 두번째 원소부터 duplicate이 발생하기 때문.
- 2번째 원소부터 for 문을 돌며 만약 이전 원소와 값이 다를 경우 nums[j]를 nums[i]로 업뎃해준다.
- 업데이트가 발생할때만 j에 1을 더해준다.


```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        j = 1
        for i in range(1, len(nums)):
            if nums[i] != nums[i-1]:
                nums[j] = nums[i]
                j += 1
        return j
```

## 문제 2

여기소 하나의 중복을 허용하는 문제로 [Leetcode 80 Remove Duplicated from Sorted Array 2](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/description/) 가 있다.

Given an integer array nums sorted in non-decreasing order, remove some duplicates in-place such that each unique element appears at most twice. The relative order of the elements should be kept the same.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the first part of the array nums. More formally, if there are k elements after removing the duplicates, then the first k elements of nums should hold the final result. It does not matter what you leave beyond the first k elements.

Return k after placing the final result in the first k slots of nums.

Do not allocate extra space for another array. You must do this by modifying the input array in-place with O(1) extra memory.

![missing_number2](https://bernard-choi.github.io/assets/img/post_img/remove_duplicate_number_2.jpg)

## 풀이

- 마찬가지로 위 문제와 같이 업데이트할 인덱스 j를 둔다.
- 단순히 이전 원소와 비교를 통해 duplicate을 찾았던 이전 문제와 달리 count 변수를 두고 이 값이 2보다 작을때만 업데이트가 발생하도록 한다.
- 이전 원소와 다르면 count를 1 더해준다. 만약 다르면 1로 초기화한다.
- count가 2보다 작으면 nums[j]를 nums[i]로 업뎃해준다.
- 업데이트가 발생할때만 j에 1을 더해준다.

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        j = 1
        count = 1
        for i in range(1, len(nums)):
            if nums[i] == nums[i-1]:
                count += 1
            else:
                count = 1
            if count <= 2:
                nums[j] = nums[i]
                j += 1

        return k
```

---
layout: post
title: "[Python] Leetcode 274. H-index"
subtitle: "[Python] Leetcode 274. H-index"
categories: Algorithm
tags: 문제풀이 count_sort sort
comments: true


---
## [Leetcode 274. H-index](https://leetcode.com/problems/h-index/description/)

## 문제

Given an array of integers citations where citations[i] is the number of citations a researcher received for their ith paper, return the researcher's h-index.

According to the definition of h-index on Wikipedia: The h-index is defined as the maximum value of h such that the given researcher has published at least h papers that have each been cited at least h times.



![h_index](https://bernard-choi.github.io/assets/img/post_img/h_index.png)


## 풀이

### 1. Sort 방식

배열을 내림차순으로 정렬한 후, 각 위치에서 `인덱스 + 1` 값과 해당 citation 값을 비교합니다.

- 내림차순 정렬 후 i번째 위치의 논문은 "i+1개의 논문 중 가장 적은 인용 수"를 나타냅니다.
- `citations[i] >= i + 1`이면, 최소 i+1편의 논문이 i+1회 이상 인용되었음을 의미합니다.
- 이 조건을 만족하는 최대 i+1 값이 h-index입니다.

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        citations.sort(reverse=True)
        h = 0
        for i, c in enumerate(citations):
            if c >= i + 1:
                h = i + 1
            else:
                break
        return h
```

- 시간복잡도: O(n log n) - 정렬에 의해 결정
- 공간복잡도: O(1) - in-place 정렬 시

### 2. Counting Sort 방식

Counting Sort를 활용하면 O(n) 시간복잡도로 해결할 수 있습니다.

- h-index는 최대 n(논문 수)을 넘을 수 없으므로, 크기 n+1의 bucket 배열을 생성합니다.
- 각 citation 값을 bucket에 카운트하되, n 이상인 값은 모두 bucket[n]에 저장합니다.
- 뒤에서부터 누적합을 계산하며, 누적합이 인덱스 이상이 되는 최대 인덱스가 h-index입니다.

```python
class Solution:
    def hIndex(self, citations: List[int]) -> int:
        n = len(citations)
        bucket = [0] * (n + 1)

        # 각 citation 값을 bucket에 카운트
        for c in citations:
            if c >= n:
                bucket[n] += 1
            else:
                bucket[c] += 1

        # 뒤에서부터 누적합 계산
        total = 0
        for i in range(n, -1, -1):
            total += bucket[i]
            if total >= i:
                return i

        return 0
```

- 시간복잡도: O(n) - 한 번의 순회로 bucket 생성, 한 번의 순회로 h-index 계산
- 공간복잡도: O(n) - bucket 배열 사용

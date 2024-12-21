---
layout: post
title: "[Python] Leetcode 121. Best Time to Buy and Sell Stock"
subtitle: "[Python] Leetcode 121. Best Time to Buy and Sell Stock"
categories: Algorithm
tags: 문제풀이 Array
comments: true


---
## [Leetcode 121 Rotate Array](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/?envType=study-plan-v2&envId=top-interview-150)

## 문제

You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.


![rotate_array_1](https://bernard-choi.github.io/assets/img/post_img/best_time_to_buy_and_sell_stock_1.jpg)


## 풀이

- 한번 순회로 풀 수 있음. min_val과 max_profit을 두고 순회해하면서 해당 값들을 업데이트

- 최대 이익은 이전 최솟값이 쓰일때이기에 최솟값을 항상 업데이트하면 됨.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        max_profit = 0
        min_val = prices[0]

        for i in range(1, len(prices)):
            max_profit = max(max_profit, prices[i] - min_val)
            min_val = min(min_val, prices[i])
        return max_profit
```

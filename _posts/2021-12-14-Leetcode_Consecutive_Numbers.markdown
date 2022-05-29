---  
layout: post
title: "[SQL] Consecutive Numbers"
subtitle: "[SQL] Consecutive Numbers"  
categories: Data
tags: SQL Case Join Window
comments: true  
---  

## [Leetcode Consecutive Numbers](https://leetcode.com/problems/consecutive-numbers/)

## 1. 문제

![Consecutive_Numbers1.jpg](https://yunsikus.github.io/assets/img/post_img/Consecutive_Numbers1.jpg)

Write an SQL query to find all numbers that appear at least three times consecutively.

Return the result table in any order.

The query result format is in the following example. 


## 2. Input Output 예시

![Consecutive_Numbers2.jpg](https://yunsikus.github.io/assets/img/post_img/Consecutive_Numbers2.jpg)

## 설명

- 바로 다음 num값을 컬럼에 이어 붙여서 후에 where절로 각 컬럼값이 같을 경우만 추출하면 됩니다. 

- inner join을 lag를 1일, 2일씩 걸고 2번하는 방법이 있습니다. 
- 윈도우 함수를 사용하여 간편하게 짤 수도 있습니다. (lag)

```
id num num2 num3
1   1   1   1
2   1   1   2
3   1   2   1
4   2   1   2
5   1   2   2
```

## 풀이1. JOIN문으로 풀기

```SQL

SELECT
	DISTINCT l.num AS ConsecutiveNums
FROM
	Logs AS l
INNER JOIN logs AS l_next ON
	l.id + 1 = l_next.id
INNER JOIN logs AS l_next2 ON
	l.id + 2 = l_next2.id
WHERE
	l.num = l_next.num
	and l.num = l_next2.num;
```


## 풀이2. Window 함수 사용하기

```SQL
SELECT
	DISTINCT l.NUM AS ConsecutiveNums
FROM
	(
	SELECT
		Num ,
		LEAD(NUM, 1) OVER (
		ORDER BY ID) AS next ,
		LEAD(NUM, 2) OVER (
		ORDER BY ID) AS afternext
	FROM
		logs ) as l
WHERE
	l.Num = l.next
	and l.Num = l.afternext;
```
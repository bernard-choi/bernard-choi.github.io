---  
layout: post
title: "[SQL] Hackerrank Challenges"
subtitle: "[SQL] Hackerrank Challenges"  
categories: Data
tags: SQL With 
comments: true  
---  

## [Hackerrank Challenges](https://www.hackerrank.com/challenges/challenges/problem?h_r=internal-search)

## 1. 문제

Julia asked her students to create some coding challenges. Write a query to print the hacker_id, name, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by hacker_id. If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.

`Hackers` : The hacker_id is the id of the hacker, and name is the name of the hacker.

`Challenges` : The challenge_id is the id of the challenge, and hacker_id is the id of the student

## 2. Input Output 예시

Input Hackers Table

![Challenges_1](https://yunsikus.github.io/assets/img/post_img/Challenges1.jpg)

Input Challenges Table

![Challenges_2](https://yunsikus.github.io/assets/img/post_img/Challenges2.jpg)

Output Table

![Challenges_3](https://yunsikus.github.io/assets/img/post_img/Challenges3.jpg)

## 설명

challenges created가 최대, 혹은 겹치는 수가 1개인 경우만 hacker_id, name, challenges_created 출력

## 풀이1. With문 없이 풀기

- 먼저 `Hacker Table`과 `Challenge Table`을 join
- challenges_created를 구하기 위해 hacker_id와 name으로 group by
- challenges_created로 내림차순, 만약 challenges_created가 같을 경우 hack_id로 오름차순 정렬

```sql
SELECT -- base query
	h.hacker_id,
	h.name,
	count(*) AS challenges_created
FROM
	Challenges AS c
LEFT JOIN Hackers AS h ON
	c.hacker_id = h.hacker_id
GROUP BY
	h.hacker_id,
	h.name
ORDER BY
	challenges_created DESC,
	h.hacker_id;
```

![Challenges_4](https://yunsikus.github.io/assets/img/post_img/Challenges4.jpg)


이제 다음 경우로 필터링해야한다. 

1) challenges_created가 최대값 
2) challenges_created가 겹치는 값이 없을 경우(=1) 필터링해야한다. 
   

최대값의 경우 다음과 같다. 

```sql
SELECT MAX(challenges_created) FROM -- filtereing query 1
(SELECT
    h.hacker_id,
    h.name,
    count(*) AS challenges_created
FROM
    Challenges AS c
LEFT JOIN Hackers AS h ON
    c.hacker_id = h.hacker_id
GROUP BY
    h.hacker_id,
    h.name
ORDER BY
    challenges_created DESC,
    h.hacker_id) sub;
```

- 위 값을 subquery로 넣어 필터링한다. 

```sql
SELECT -- base query
	h.hacker_id,
	h.name,
	count(*) AS challenges_created
FROM
	Challenges AS c
LEFT JOIN Hackers AS h ON
	c.hacker_id = h.hacker_id
GROUP BY
	h.hacker_id,
	h.name
HAVING
	challenges_created = ( -- filtering query 1
	SELECT
		MAX(challenges_created)
	FROM
		(
		SELECT
			h.hacker_id,
			h.name,
			count(*) AS challenges_created
		FROM
			Challenges AS c
		LEFT JOIN Hackers AS h ON
			c.hacker_id = h.hacker_id
		GROUP BY
			h.hacker_id,
			h.name
		ORDER BY
			challenges_created DESC,
			h.hacker_id) sub)
ORDER BY
	challenges_created DESC,
	h.hacker_id;
```

- 다음으로 challenges_created가 겹치는 값이 없을 경우로 필터링해야한다.
- 다시 한번, challenges_created로 group by후 count(*) 계산하여 1인 경우만 출력

```sql
SELECT -- filtering query2
	sub.challenges_created
FROM
	(
	SELECT
		h.hacker_id,
		h.name,
		count(*) AS challenges_created
	FROM
		Challenges AS c
	LEFT JOIN Hackers AS h ON
		c.hacker_id = h.hacker_id
	GROUP BY
		h.hacker_id,
		h.name
	ORDER BY
		challenges_created DESC,
		h.hacker_id) sub
GROUP BY
	challenges_created
HAVING
	count(*) = 1;
```

- 위 쿼리를 서브쿼리로 필터링하면 최종 쿼리는 다음과 같다. 

```sql
SELECT -- base query
	h.hacker_id,
	h.name,
	count(*) AS challenges_created
FROM
	Challenges AS c
LEFT JOIN Hackers AS h ON
	c.hacker_id = h.hacker_id
GROUP BY
	h.hacker_id,
	h.name
HAVING
	challenges_created = ( -- filtering query1
	SELECT
		MAX(challenges_created)
	FROM
		(
		SELECT
			h.hacker_id,
			h.name,
			count(*) AS challenges_created
		FROM
			Challenges AS c
		LEFT JOIN Hackers AS h ON
			c.hacker_id = h.hacker_id
		GROUP BY
			h.hacker_id,
			h.name
		ORDER BY
			challenges_created DESC,
			h.hacker_id) sub)
	OR challenges_created in ( -- filtering query2
	SELECT
		sub.challenges_created
	FROM
		(
		SELECT
			h.hacker_id,
			h.name,
			count(*) AS challenges_created
		FROM
			Challenges AS c
		LEFT JOIN Hackers AS h ON
			c.hacker_id = h.hacker_id
		GROUP BY
			h.hacker_id,
			h.name
		ORDER BY
			challenges_created DESC,
			h.hacker_id) sub
	GROUP BY
		challenges_created
	HAVING
		count(*) = 1)
ORDER BY
	challenges_created DESC,
	h.hacker_id;
```

- 위 코드를 살펴보면 같은 base query가 중복되어 계속 사용되는 것을 볼 수 있다. 
- 이러한 경우 with 문을 통해 중복을 없엘 수 있다.

## 풀이2. With문 으로 풀기

- 반복되는 쿼리를 counter에 담아둔다. 
- counter를 사용하여 필터링하면 쿼리가 간략해진다. 
  
```sql
WITH counter AS (
SELECT
	hackers.hacker_id,
	hackers.name,
	COUNT(*) AS challenges_created
FROM
	Challenges
INNER JOIN Hackers ON
	Challenges.hacker_id = Hackers.hacker_id
GROUP BY
	hackers.hacker_id,
	hackers.name )
	
SELECT
	*
FROM
	counter
WHERE
	challenges_created = (
	SELECT
		MAX(challenges_created)
	FROM
		counter)
	OR challenges_created in (
	SELECT
		challenges_created
	FROM
		counter
	GROUP BY
		challenges_created
	HAVING
		count(*)= 1)
ORDER BY
	challenges_created DESC,
	hacker_id;
```

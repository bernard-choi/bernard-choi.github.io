---  
layout: post
title: "[SQL] Hackerrank The Report"
subtitle: "[SQL] Hackerrank The Report"  
categories: Data
tags: SQL Case Between
comments: true  
---  

## [Hackerrank The Report](https://www.hackerrank.com/challenges/the-report/problem?h_r=internal-search)

## 1. 문제

You are given two tables: Students and Grades. Students contains three columns ID, Name and Marks.

![report_1](https://yunsikus.github.io/assets/img/post_img/report1.jpg)

Grades contains the following data:

![report_2](https://yunsikus.github.io/assets/img/post_img/report2.jpg)

Ketty gives Eve a task to generate a report containing three columns: Name, Grade and Mark. Ketty doesn't want the NAMES of those students who received a grade lower than 8. The report must be in descending order by grade -- i.e. higher grades are entered first. If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically. Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.

Write a query to help Eve.



## 2. Input Output 예시

Sample Input

![report_3](https://yunsikus.github.io/assets/img/post_img/report3.jpg)

Sample Output

![report_4](https://yunsikus.github.io/assets/img/post_img/report4.jpg)


## 설명

Mark에 해당하는 점수의 Grade를 가져와야 합니다. 이때 Grade가 8보다 작은 경우 Name을 'NULL'로 뽑아야 합니다. 

## 풀이1. CASE문으로 풀기

- Case문으로 Grade가 8보다 작을 시 NULL, 아닐 시 NAME을 출력
- Case문으로 MARKS의 점수를 매핑시킨다. 
- grade와 name으로 정렬

```sql
SELECT
	CASE
	WHEN GRADE < 8 THEN 'NULL'
	ELSE NAME
END AS New_NAME,
CASE
	WHEN 90 <= MARKS THEN 10
	WHEN 80 <= MARKS THEN 9
	WHEN 70 <= MARKS THEN 8
	WHEN 60 <= MARKS THEN 7
	WHEN 50 <= MARKS THEN 6
	WHEN 40 <= MARKS THEN 5
	WHEN 30 <= MARKS THEN 4
	WHEN 20 <= MARKS THEN 3
	WHEN 10 <= MARKS THEN 2
	WHEN 0 <= MARKS THEN 1
END AS New_Marks,
MARKS
FROM
students
ORDER BY
New_Marks desc,
NAME;
```

## 풀이2. join과 between으로 풀기

- MARK를 min_mark와 max_mark의 between과 join
- ID, NAME, MARK, MIN_MARK, GRADE, MAX_MARK를 한번에 뽑을 수 있음

```sql
SELECT
	*
from
	students as s
left join grades as g on
	s.marks BETWEEN g.min_mark and g.max_mark
order by
	g.grade desc,
	s.name;
```
![report_5](https://yunsikus.github.io/assets/img/post_img/report5.jpg)

- 풀이 1처럼 Case문으로 Grade가 8보다 작을 시 NULL, 아닐 시 NAME을 출력 
- grade와 name으로 정렬
- 최종 sql 쿼리는 아래와 같다. 


```sql
SELECT
	CASE
		WHEN g.grade < 8 THEN 'NULL'
		ELSE NAME
	END AS NEW_NAME,
	g.grade,
	s.marks
FROM
	students AS s
LEFT JOIN grades AS g ON
	s.marks BETWEEN g.min_mark AND g.max_mark
ORDER BY
	g.grade DESC,
	s.name;
```

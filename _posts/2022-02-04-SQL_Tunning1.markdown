---  
layout: post
title: "[SQL] SQL문 단순 수정으로 착한 쿼리 만들기"
subtitle: "[SQL] SQL문 단순 수정으로 착한 쿼리 만들기"  
categories: Data
tags: SQL Tunning
comments: true  
---  

## 1. 기본 키를 변형하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	*
FROM
	사원
WHERE
	SUBSTRING(사원번호, 1, 4) = 1100
	AND LENGTH(사원번호) = 5;
```

![SQL_TUNNING1](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning1.jpg)

- type이 `ALL` 즉 테이블 풀 스캔 방식임.
- 인덱스가 걸려있는 컬럼에 가공했기 때문에 인덱스를 타지 않은 것. 
- 필요한 범위에만 접근하면 되는데 끝까지 스캔하기 때문에 비효율적일 수 있음. 


`튜닝 후`

```SQL
SELECT
	*
FROM
	사원
WHERE
	사원번호 >= 11000
	AND 사원번호 <= 11009;
```

![SQL_TUNNING2](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning2.jpg)

- 위와 같이 가공작업 없게 변경함.
- 인덱스를 타게 되고 type이 `range`로 바뀜.

---

## 2. 사용하지 않는 함수를 포함하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	IFNULL(성별, 'NO DATA') AS 성별,
	count(1) 건수
FROM
	사원
GROUP BY
	IFNULL(성별, 'NO DATA');
```

![SQL_TUNNING3](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning3.jpg)

- Extra 항목이 Using temporary이므로 임시 테이블을 생성한다는 것을 알 수 있다. 
- NULL이 없기 때문에 IFNULL 함수가 필요없다. 하지만 IFNULL 함수로 인해 임시 테이블이 생성된다. 

`튜닝 후`

```SQL
SELECT
	성별,
	count(성별) AS 건수
FROM
	사원
GROUP BY
	성별;
```


- 임시 테이블 없이 인덱스만 사용하여 데이터를 추출하기 때문에 수행시간이 0.77에서 0.1초로 줄어든다. 
![SQL_TUNNING4](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning4.jpg)

---

## 3. 형변환으로 인덱스를 활용하지 못하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	COUNT(1)
FROM
	급여
WHERE
	사용여부 = 1;
```

![SQL_TUNNING5](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning5.jpg)

- key 항목이 `I_사용여부`. 즉 사용여부 인덱스를 사용하며, type 항목이 index이므로 인덱스 풀 스캔 방식으로 수행됨. 또한 filtered가 10이므로 MySQL엔진으로 가져온 데이터 중 10%를 추출해서 최종 데이터를 출력.
- `사용 여부` 에는 인덱스가 있고 where절의 조건문으로 작성했으나 인덱스 풀 스캔이 수행됨
- 그 이유는, 사용여부 열은 문자형인 `char(1)` 데이터 유형으로 구성되는데 where 사원번호 = 1과 같이 숫자 유형으로 써서 데이터에 접근했으므로 묵시적 형변환이 발생한 것

`튜닝 후`

```SQL
SELECT 
	COUNT(1)
FROM
	급여
WHERE 
	사용여부 = '1';
```

![SQL_TUNNING6](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning6.jpg)

- type이 `index` -> `ref`로 변함. index가 잘 잘동한것 확인

---

## 4. 열을 결합하여 사용하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	*
FROM
	사원
WHERE
	CONCAT(성별, ' ', 성) = 'M Radwan';
```

![SQL_TUNNING7](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning7.jpg)

- 사원 테이블은 조건절로 데이터에 접근하지만 테이블 풀 스캔으로 데이터를 처음부터 끝까지 스캔하므로 비효율적임
- 전체 데이터에 비해 M Radwan의 비율이 매우 작으므로 Full Scan은 비효율적임

`튜닝 후`

```SQL
SELECT
	*
FROM
	사원
WHERE
	성별 = 'M'
	AND 성 = 'Radwan';
```

![SQL_TUNNING8](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning8.jpg)

- 각 열을 분리하고 열의 변형을 제거
- 원래는 30만건의 데이터에 접근했다면 튜닝 후에는 102건의 데이터에만 접근하므로 액세스 범위가 줄었다는 것을 알 수 있습니다.

---

## 5. 습관적으로 중복을 제거하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
DISTINCT
	사원.사원번호,
	사원.이름,
	사원.성,
	부서관리자.부서번호
FROM
	사원
JOIN 부서관리자 ON
	(사원.사원번호 = 부서관리자.사원번호);
```

![SQL_TUNNING9](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning9.jpg)

- 드라이빙 테이블인 부서관리자 테이블과 드리븐 테이블인 사원 테이블의 id 값이 둘 다 1로 동일하게 나타나므로 서로 조인한다고 해석할 수 있다.
- 부서관리자 type이 `index`이므로 인덱스 풀 스캔 방식으로 수행된다는 것을 알 수 있다.
- 한편 사원 테이블의 type 항목이 `eq_ref`이므로 사원번호라는 기본 키를 사용해서 단 1건의 데이터를 조회하는 방식으로 조인됨을 확인할 수 있다
- 또한, `Distinct`를 수행하고자 별도의 임시 테이블을 만들고 있다. (`Using Temporary`)
- `Distinct` 키워드는 나열된 열들을 정렬한 뒤 중복된 데이터는 삭제한다. 따라서 `Distinct`를 쿼리에 작성하는 것만으로도 정렬 작업이 포함됨을 인지해야 한다.

`튜닝 후`

```SQL
SELECT
DISTINCT
	사원.사원번호,
	사원.이름,
	사원.성,
	부서관리자.부서번호
FROM
	사원
JOIN 부서관리자 ON
	(사원.사원번호 = 부서관리자.사원번호);
```

![SQL_TUNNING10](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning10.jpg)

---

## 6. 다수 쿼리를 UNION 연산자로만 합치는 나쁜 SQL문

`튜닝 전`
```SQL
SELECT
	'M' AS 성별,
	사원번호
FROM
	사원
WHERE
	성별 = 'M'
	AND 성 = 'Baba'

UNION

SELECT
	'F' AS 성별,
	사원번호
FROM
	사원
WHERE
	성별 = 'F'
	AND 성 = 'Baba';
```
![SQL_TUNNING11](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning11.jpg)

- 중복 제거하는 작업을 처리하기 위해 임시 테이블을 생성한다.
- 만약 메모리에 상주하기 어려울 만큼 id가 1인 행과 2인 행의 결과량이 많다면, 메모리가 아닌 디스크에 임시 파일을 생성하여 UNION 작업을 수행하게 된다.
- 이미 사원번호라는 기본 키가 출력되는 SQL문에서 이처럼 중복을 제거하는 과정이 과연 필요한지 고민해봐야 한다.

`튜닝 후`
```SQL
SELECT
	'M' AS 성별,
	사원번호
FROM
	사원
WHERE
	성별 = 'M'
	AND 성 = 'Baba'

UNION ALL

SELECT
	'F' AS 성별,
	사원번호
FROM
	사원
WHERE
	성별 = 'F'
	AND 성 = 'Baba';
```

- 중복을 제거하는 UNION 연산자 대신 결과값을 단순히 합치는 UNION ALL 연산자로 변경해주어야 한다.
- 정렬하여 중복을 제거하는 작업이 제외되면서 불필요한 리소스 낭비를 방지

---

## 7. 인덱스 고려 없이 열을 사용하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	성,
	성별,
	COUNT(1) as 카운트
FROM
	사원
GROUP BY
	성,
	성별;
```

![SQL_TUNNING13](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning13.jpg)

- 성별, 성 인덱스의 결과를 다시 성 열과 성별 열 순으로 재정렬한다.(Using temporary)
- 이미 존재하는 I_성별_성 인덱스를 최대한 활용하려면 인덱스 순서대로 그루핑하면 된다.  그러면 별도의 임시 테이블을 생성하지 않고도 그루핑과 카운트 연산을 수행할 수 있다.
- 성별, 성으로 인덱싱 되어있기 때문에 이 순서대로 group by절에 적어줘야 함.

`튜닝 후`

```SQL
SELECT
	성,
	성별,
	COUNT(1) as 카운트
FROM
	사원
GROUP BY
	성별,
	성;
```

![SQL_TUNNING14](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning14.jpg)

- 임시 테이블을 생성하지 않고도 I_성별_성 인덱스만으로 그루핑 이후의 정렬 작업까지 수행된다.
- 즉, Extra 항목에서 Using temporary가 사라졌다는 걸 확인할 수 있다.

---

## 8. 엉뚱한 인덱스를 사용하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	사원번호
FROM
	사원
WHERE
	입사일자 LIKE '1989%'
	AND 사원번호 > 100000;
```

![SQL_TUNNING15](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning15.jpg)

- 범위 스캔을 수행하여 데이터를 가져온 뒤, 남은 필터 조건인 입사일자로 추출(11%)
- LIKE 절보다 부등호 조건절이 우선하여 인덱스를 사용하므로 데이터 접근 범위를 줄일 수 있다. (사원번호 보다 먼저 수행된다?)
    - 내가 이해한 바로는 부등호가 LIKE보다 먼저 실행되기 때문에 LIKE절에 index를 사용해도 의미 없다. 사원번호 개수가 많아서 인덱스 효과가 작기 때문
    - %가 아닌 부등호 조건절로 사원번호보다 먼저 인덱스로 사용되도록 한다.

`튜닝 후`
```SQL
SELECT
	사원번호
FROM
	사원
WHERE
	사원번호 > 100000
    AND 입사일자 >= '1989-01-01' AND 입사일자 < '1990-01-01';
```

![SQL_TUNNING16](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning16.jpg)

- 입사일자가 range index를 타게 됨

---

## 9. 동등 조건으로 인덱스를 사용하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT *
FROM 사원출입기록
WHERE 출입문 = 'B'
```

![SQL_TUNNING17](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning17.jpg)

- 출입문 B는 총 66만 건의 전체 데이터 중 30만 건을 차지하고 있다.
- 실행 계획에 따르면 I_출입문 인덱스로 인덱스 스캔을 수행
- 랜덤 액세스하는 방식이지만 전체 데이터의 약 50%에 달하는 데이터를 조회하려고 인덱스를 활용하는게 과연 효율적일지 고민

`튜닝 후`

```SQL
SELECT *
FROM 사원출입기록 IGNORE INDEX(I_출입문)
WHERE 출입문 = 'B';
```

![SQL_TUNNING18](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning18.jpg)

- 인덱스를 무시할 수 있도록 IGNORE INDEX라는 힌트를 사용

---

## 10. 범위 조건으로 인덱스를 사용하는 나쁜 SQL문

`튜닝 전`

```SQL
SELECT
	이름,
	성
FROM
	사원
WHERE
	입사일자 BETWEEN STR_TO_DATE('1994-01-01',
	'%Y-%m-%d') AND STR_TO_DATE('2000-12-31',
	'%Y-%m-%d');
```

![SQL_TUNNING19](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning19.jpg)

- I_입사일자 인덱스로 범위 스캔을 수행
- Using MRR을 통해 인덱스가 랜덤 액세스가 아닌 순차 스캔으로 최적화하여 처리됨을 확인
- 결과 건수는 48875건으로 전체 17%에 해당함.
- 인덱스를 사용하지 않도록 변경하여 테이블 풀 스캔을 고정적으로 사용하도록 한다.

`튜닝 후`

```SQL
SELECT
	이름,
	성
FROM
	사원
WHERE
	YEAR(입사일자) BETWEEN '1994' AND '2000';
```

![SQL_TUNNING20](https://yunsikus.github.io/assets/img/post_img/SQL_Tunning20.jpg)

- 인덱스를 사용하지 않도록 변경하여 테이블 풀 스캔을 고정적으로 사용하도록 한다.
- 인덱스 없이 테이블에 직접 접근하여 한 번에 다수의 페이지에 접근하므로 더 효율적으로 SQL문이 수행됨.


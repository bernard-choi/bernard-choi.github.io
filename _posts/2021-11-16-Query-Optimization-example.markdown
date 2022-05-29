---  
layout: post
title: "[DB] 비효율적인 SQL EXAMPLE"
subtitle: "[DB] 비효율적인 SQL EXAMPLE"  
categories: CS
tags: DataBase InnoDB MySQL
comments: true  
---  

## 1. 조회조건 컬럼의 외부적인 변형

- 형변환이 일어나는 경우 인덱스가 무시되고 Full Table Scan이 진행됨

**개선 전**
```SQL
SELECT * FROM EMP
    WHERE SUBSTR(ENAME,1,3) = 'CHOI';
```

**개선 후**
```SQL
SELECT * FROM EMP
    WHERE ENAME LIKE 'JOE%';
```

---

## 2. Data Type 불일치

- Data Type 불일치로 인해 형변환을 하는 경우
- `DEPTNO`: CHAR(3)
- `IN_DEPTNO`: NUMBER형 BIND변수
- 마찬가지로 Full Table Scan이 진행됨

**개선 전**
```SQL
SELECT * FROM DEPT
    WHERE TO_NUMER(DEPTNO) = :IN_DEPTNO
```

**개선 후**
```SQL
SELECT * FROM DEPT
    WHERE DEPTNO = LPAD(TO_CHAR(:IN_DEPTNO),3,'0')
```
---

## 3. Correlated subquery

- 일반적인 subquery는 subquery의 결과를 main query가 이용한다. 그러나, correlated 서브쿼리는 subquery가 main query의 값을 이용하고 그렇게 구해진 subquery의 값을 다시 main query가 다시 이용하게 된다. 
- **where절의 subquery가 main 함수의 행만큼 반복되어 성능이 떨어진다.**
- 아래 쿼리는 제품 정가가 평균 제품 가격보다 15% 이상 낮은 제품의 수를 확인하는 SQL


**개선 전**
```SQL
SELECT COUNT(*) FROM products p
WHERE prod_list_price < -- 다시 main query에 사용됨
    1.15 * (SELECT avg(unit_cost) FROM costs c
            WHERE c.prod_id = p.prod_id) -- 서브 쿼리가 메인 쿼리의 값(p.prod_id)을 사용함, p행만큼 반복됨
```


- 이 쿼리의 경우 Cost가 2654임. p의 행의 개수인 72번 subquery가 반복되었기 때문에 Cost가 높음. 
![sql_example2](https://yunsikus.github.io/assets/img/post_img/SQL_Example2.jpg)

**개선 후**
```SQL
SELECT COUNT(*) FROM products p,
(SELECT prod_id, AVG(unit_cost) ac FROM costs GROUP BY prod_id) c
WHERE p.prod_id = c.prod_id AND
p.prod_list_price < 1,15 * c.ac
```

- correlated subquery를 다음과 같이 main query와 분리하여 cost를 줄일 수 있음
- cost가 80임
![sql_example1](https://yunsikus.github.io/assets/img/post_img/SQL_Example1.jpg)

--- 

## 4. 데이터 모델링 이슈

- join에 필요한 컬럼의 타입이 데이터 모델링 단에서부터 다른 경우
- 형변환을 하면 인덱스를 사용할 수 없다. 
- join에 필요한 컬럼을 묶어서 대체키를 만드는 방식으로 해결할 수 있다. 

```SQL
SELECT * FROM A, B
    WHERE A.REG_YMD = TO_CHAR(B.REG_DATE, 'YYMMDD')

SELECT * FROM job_history jh, employee e
    WHERE substr(to_char(e.employee_id), 2) = 
    substr(to_char(jh.employee_id),2)

SELECT * FROM M, D
    WHERE D.SLIP_NO = 
    M.SLIP_NO_YYYY||M>SLIP_NO_MM||M.SLIP_NO_SEQ
```

---

## 5. Miscellaneous

- `IS NULL` 조건 조회 시 해당 컬럼이 NOT NULL 조건이 아니라면 해당 컬럼에 인덱스가 존재하여도 FULL Table Scan으로 처리됨. 
- NULL 값은 인덱스에 포함되지 않기 때문. 따라서 IS NULL 조건 조회 시 비효율적인 경우가 발생
- NVL 처리와 Function Based Index 생성하여 Index Scan이 되도록 만들 수 있다. 

**개선 전**
```SQL
SELECT * FROM temp
WHERE c3 is NULL;
```

**개선 후**
```SQL
CREATE INDEX ix_temp on temp (nvl(c3,'ISNULL')) -- NVL 함수를 이용한 NULL 처리
SELECT * FROM temp
WHERE nvl(c3, 'ISNULL') = 'ISNULL';
```

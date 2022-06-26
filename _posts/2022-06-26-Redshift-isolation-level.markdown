---
layout: post
title: "Redshift Isolation Level"
subtitle: "Redshift Isolation Level"
categories: AWS
tags: Redshift
comments: true
---

## Serializable Isolation이란?

- Redshift의 default isolation level은 `Serializable임`.(변경 불가)
- 가장 단순하면서 엄격한 격리수준이지만 동시 처리 성능이 가장 낮다.
- `Reapeatable Read`, `Phantom Read`와 같은 현상은 발생하지 않는다.
- 예를 들어, Table1에 write하는 T1과 T2가 동시에 실행된다고 하자.
  - 만약 결과가 모든 시나리오에서 발생할 수 있는 결과(T1 -> T2, T2 -> T1)와 일치한다면 parellel하게 실행 가능
  - 그러나, 다른 경우 `transaction`을 abort처리 하고 `roll back`한다.
  - 예를 들어 Scenario 1은 selct, insert. Scenario2는 insert, select로 되어있다. 모든 scenario의 실행 결과는 다음과 같다.

```
Scenario 1 -> 2
- T1 select a 0 row, insert a 1 row
- T2 insert a 1 row, select 2 rows
- output: T1에서는 0 row T2에서는 2 row

Scenario 2 -> 1
-  T2 insert a 1 row and select 1 row
-  T1 select a 1 row and insert 1 row
-  output: T1에서는  1 row T2 에서는 1 row
```
   - **만약 T2에서 insert a row, T1에서 insert, select한 다음 T2에서 select 하면 output은 t1은 1 row t2는 2 row가 된다.**
   - T1과 T2를 어느 순서로 실행시키더라도 이러한 결과는 나오지 않는다.(`violation`)

- `violation`을 잡아내기 위헤, Redshift는 다음 statement들이 수행될때마다 `snapshot`을 찍어낸다.
  - select, copy, delete, insert, update, truncate, alter table, create table, drop table, truncate table


## 해결 방법
- Select 문을 `transaction block`에서 뺀다.
    - 위 경우처럼, Select 문으로 인해 `violation`이 발생할 수 있음.
    - 보통의 경우, Select문의 `atomicity`까지 보장할 필요는 없다. (write 할때가 중요)

- 수정 전

```sql
# session 1
select * from tab1
insert into tab2 values (1);

```

- 수정 후

```sql
# session 1
select * from tab1
begin;
insert into tab2 values (1);
end;
```

## My case
- S3의 데이터를 Redshift로 migration할때 다음과 같은 프로세스로 진행되었음
- create temp table(staging) -> copy to staging table -> insert into target table
- 전체 프로세스를 하나의 transaction으로 처리하면 violation 자주 발생할 수 있으니 실제로 insert되는 부분만 begin end로 묶어줌.

수정 전
```sql
# my case
begin;
end; -- 임의로 세션을 끊어줌

create temp_table stg_tbl;
copy; -- staging 테이블에 s3데이터를 copy함

begin;
insert into target_tbl; -- stg_tbl의 데이터를  target_tbl로 insert하는 부분만 session 처리함.
end;
```

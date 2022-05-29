---  
layout: post
title: "Join을 통해 update 시간 단축하기"
subtitle: "Bulk Update"  
categories: Development
tags: SQL
comments: true  
---  
## 문제상황

- 여러개의 row를 update하는 모듈의 속도가 매우 느림.
- 16000개의 row를 update하는데 10분 이상이 소요됨.
- 배치성 작업이 이로 인해 필요 이상으로 오래걸림.

## 기존의 update 방식

- mysql 내의 update문을 활용
- where절에 update에 필요한 기준 컬럼을 설정
  - id가 11인 row를 바꾸고 싶을때 id가 해당됨
- set절에 update 대상 컬럼과 데이터 값을 넣어줌
  - weight, height 등등
- row의 수만큼 쿼리 반복하다보니 시간이 오래 걸림

```sql
UPDATE schema.tbl_name

SET weight=80.5, height=180, ...

WHERE id=11
```
---

## Bulk update 방식 - Join Update

- set과 where절의 정보가 담긴 임시 테이블 생성(`temp_table`)
  - `temp_table`이 있으면, delete 한 후 새로운 값을 insert. (drop과 같은 DDL 문은 쿼리 에러시 rollback이 안되서 지양함)
  - `temp_table`이 없으면 create

- 기존 테이블과 join update
- 한번의 join으로 모든 값을 update할 수 있다
- 기존에 16000개의 행을 update 하는데 10분이 걸렸다면, join update를 도입하여 6초로 단축할 수 있었습니다.

```sql
UPDATE shcema.tbl_name A
INNER JOIN schema.temp_table B
ON A.id = B.id
SET A.weight = B.weight, A.height = B.height, ...
```

실제 적용 코드는 Github에 공유하였습니다.  
- [yunsikus/bulk_update_module](https://github.com/yunsikus/db_update_module)

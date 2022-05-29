---  
layout: post
title: "[Mysql] Transaction Model 이해하기(2) - Lock"
subtitle: "[Mysql] Transaction Model 이해하기(2) - Lock"  
categories: CS
tags: DataBase InnoDB Transaction MySQL
comments: true  
---  

MySQL의 InnoDB 엔진은 SQL 표준에 정의된 4가지 트랜잭션 격리수준을 모두 제공합니다.
트랜잭션 격리수준을 이해하기 위한 필수 개념인 lock을 이해해봅시다.

## InnoDB의 lock
InnoDB는 transaction의 ACID 원칙과 동시성을 최대한 보장하기 위해 다양한 종류의 lock을 사용한다.

### Row-level lock
가장 기본적인 lock은 테이블의 row마다 걸리는 row-level lock입니다. 여기에는 크게 shared lock과 exlusive lock 구 종류가 있습니다.

Shared losk(S lock)은 read에 대한 lock입니다. 일반적인 SELECT 쿼리는 lock을 사용하지 않고 DB를 읽어들인다. 하지만 SELECT ... FOR SHARE 등 일부 SELECT 쿼리는 read 작업을 수행할때 InnoDB가 각 row에 S lock을 건다.

Exclusice lock(X lock)은 write에 대한 lock이다. UPDATE DELETE등의 수정 쿼리를 날릴때 각 row에 걸리는 lock이다.

S lock과 X lock을 거는 규칙은 다음과 같다.
- 여러 transaction이 동시에 한 row에 S lock을 걸 수 있다. 즉 여러 transaction이 동시에 한 row를 읽을 수 있다.
- S lock이 걸려있는 row에 다른 transaction이 X lock을 걸 수 없다. 즉, 다른 transaction이 읽고 있는 row를 수정하거나 삭제할 수 없다.
- X lock이 걸려있는 row에 다른 transaction이 S lock과 X lock둘 다 걸 수 없다. 즉 다른 transaction이 수정하거나 삭제하고 있는 row는 읽기, 수정, 삭제가 전부 불가능하다.

요약하자면, S lock을 사용하는 쿼리끼리는 같은 row에 접근 가능하다. 반면 X lock이 걸린 row는 다른 어떠한 쿼리도 접근 불가능하다.

### Record Lock
---
Record lock은 row가 안라 DB의 index record에 걸리는 lock이다. 여기도 마찬가지로 S lock과 X lock이 있다.

Record lock의 예시를 들어보겠다. c1이라는 column을 가진 테이블 t가 있다고 하자. 이때 한 transaction에서
```sql
(Query 1 in transaction A)
SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE
```
라는 쿼리를 실행했다. 그러면 t.c1의 값이 10인 index에 X lock이 걸린다. 이때, 다른 transaction에서
```sql
(Query 2 in transaction B)
DELETE FROM t where c1 = 10;
```
라는 쿼리를 실행하려고 하면, 이 query2는 우선 t.c1 = 10인 index recorf에 X lock을 걸려고 시도한다. 하지만 해당 index record에는 이미 transaction A가 query1을 실행할 때 X lock을 건 상채이다. 따라서 query2는 transaction A가 commit 되거나 rollback되기 전까지 t.c1 = 10인 row를 삭제할 수 없다. 이는 DELETE뿐만 아니라 INSERT나 UPDATE 쿼리도 마찬가지이다.

### Gap lock
---
Gap lock은 DB index record의 gap에 걸리는 lock이다. 여기서 gap이란 index중 DB에 실제 record가 없는 부분이다.

예를 들어, id column만 있는 테이블이 있고, id column에 index가 걸려있다고 하자. 현재 테이블에는 id = 3인 row와 id = 7 인 row가 있다. 그러면 DB와 index table은 아래 그림과 같은 상태일겁니다.

```plain
Index table               Database
-------------------          ---------
| id  | row addr  |          |  id   |
-------------------          ---------
|  3  | addr to 3 |--------->|   3   |
|  7  | addr to 7 |--------->|   7   |
-------------------          ---------
```
그러면 현재 id <= 2, 4 <= id <= 6, 8 <= id에 해당하는 부분에는 index record가 없다. 이 부분이 바로 index record의 gap이다.

그리고 gap lock은 이러한 gap에 걸리는 lock이다. 즉, gap lock은 해당 gap에 접근하려는 다른 쿼리의 접근을 막는다. Record lock이 해당 index를 타려는 다른 쿼리의 접근을 막는 것과 동일하다. 둘의 차이점이라면 record lock이 이미 존재하는 row가 변경되지 않도록 보호하는 반면, gap lock은 조건에 해당하는 새로운 row가 추가되는 것을 방지하기 위함이다.

Gap lock의 예시를 들어보겠다. c1이라는 column 하나가 있는 테이블 t가 있다. 여기에는 c1 = 13, c1 = 17이라는 두 row가 있다. 이 상태에서 한 transaction에서

```sql
(Query 1 in transaction A)
SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
```
라는 쿼리를 실행했다. 그러면 t.c1의 값이 10과 20 사이인 gap에 lock이 걸린다. 즉, 10 <= id <= 12, 14 <= id <= 16, 18 <= id <= 20에 해당하는 gap에 lock이 걸린다. 이 상태에서 다른 transaction이 t.c1 = 15인 row를 삽입하려고 하면 gap lock 때문에 transaction A가 commit 되거나 rollback 될 때까지 삽입되지 않는다. INSERT 뿐만 아니라 UPDATE, DELETE 쿼리도 마찬가지이다.

Gap은 한 index 값일 수도, 여러 index 값일 수도, 혹은 아예 아무 값도 없을 수도 있다.

### Lock이 해제되는 타이밍
---
Transaction이 진행되는 동안 InnoDB 엔진은 위에서 언급한 것처럼 실행되는 쿼리에 맞는 수많은 lock을 DB에 걸게 된다. 이러한 lock은 모두 transaction이 commit되거나 rollback될때 함께 unlock된다.

### Consistent Read
---
Consistent read란 read(=SELECT) operation을 수행할 때 현재 DB의 값이 아닌 특정 시점의 DB snapshot을 읽어오는 것이다. 물론 이 snapshot은 commit된 변화만이 적용된 상태를 의미한다.

Consistent read는 어떤 방법을 통해 이루어질까? 가장 단순한 방법은 읽어온 row에 lock을 걸어 다른 transaction이 할 수 없도록 하는 방법일 것이다. 하지만 InnoDB 엔진은 consistent read를 하기 위해 lock을 사용하지 않는다. 왜냐하면 동시성이 매우 떨어지기 때문이다.

InnoDB 엔진은 실행했던 쿼리의 log를 통해 consistent read를 지원한다. InnoDB 엔진은 각 쿼리를 실행할 때마다 실행한 쿼리의 log를 차곡차곡 저장한다. 그리고 나중에 consistent read를 할 때 이 log를 통해 특정 시점의 DB snapshot을 복구하여 가져온다. 이 방식은 비록 복구하는 비용이 발생하긴 하지만, lock을 활용하는 방식보다 높은 동시성을 얻을 수 있다.

이제 각 transaction isolation level에서 InnoDB가 어떻게 lock을 활용하는지 알아보자.

### Repeatable Read
---
Repeatable Read는 반복해서  read operation을 수행하더라도 읽어 들이는 값이 변화하지 않는 정도의 isolation을 보장하는 level이다.

REPEATABLE READ transaction은 처음으로 read(SELECT) operation을 수행한 시간을 기록한다. 그리고 그 이후에는 모든 read operation마다 해당 시점을 기준으로 consistent read를 수행한다. 그러므로 transaction 도중 다른 transaction이 commit 되더라도 새로이 commit 된 데이터는 보이지 않는다. 첫 read 시의 snapshot을 보기 때문이다.

일반적인 non-locking SELECT 외에 lock을 사용하는 SELECT나 UPDATE, DELETE 쿼리를 실행할 때, REPEATABLE READ transaction은 gap lock을 활용한다*. 즉, 내가 조작을 가하려고 하는 row의 후보군을 다른 transaction이 건들지 못하도록 한다. 여기에 대해서는 아래의 REPEATABLE READ vs READ COMMITTED 항목에서 다시 다룬다.

### Read Commited
---
READ COMMITTED는 commit 된 데이터만 보이는 수준의 isolation을 보장하는 level이다.

REPEATABLE READ transaction이 첫 read operation을 기준으로 consistent read를 수행하는 반면, READ COMMITTED transaction은 read operation 마다 DB snapshot을 다시 뜬다. 그렇기 때문에 다른 transaction이 commit 한 다음에 다시 read operation을 수행하면, REPEATABLE READ와는 다르게 READ COMMITTED transaction은 해당 변화를 볼 수 있다.

“엥? Commit 된 데이터만 보는 건 당연한 거 아니야?” 혹은 “아니, SELECT 쿼리마다 snapshot을 다시 뜨면 다음 read에서 복구할 필요가 없는데 snapshot을 왜 뜨는 거야?”라는 생각이 들 수도 있다. 뒤의 READ UNCOMMITTED 부분에서 추가로 설명하겠지만, 실제 DB에는 아직 commit 되지 않은 쿼리도 적용된 상태다. 따라서 commit 된 데이터만을 읽어오기 위해서는 아직 commit 되지 않은 쿼리를 복구하는 과정이 필요하다. 즉, consistent read를 수행해야 한다.

일반적인 non-locking SELECT 외에 lock을 사용하는 SELECT나 UPDATE, DELETE 쿼리를 실행할 때, READ COMMITTED transaction은 record lock만 사용하고 gap lock은 사용하지 않는다. 따라서 phantom read가 일어날 수 있다.

### REPEATABLE READ vs READ COMMITTED

Phantom read가 일어나는 상황을 자세히 알아보자. c1 column이 있는 table t가 있다. 현재 t에는 t.c1 = 13인 row와 t.c1 = 17인 row가 존재한다. 여기서 READ COMMITED transaction A와 transaction B가 아래와 같이 쿼리를 실행하려고 한다.

```sql
(Transaction A - READ COMMITTEED)
(1) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
(2) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
(3) COMMIT;
```
```SQL
(Transaction B - READ COMMITTED)
(1) INSERT INTO t VALUES(15);
(2) COMMIT;
```

두 transactin이 다음과 같은 순서로 실행되었다고 해보자.
```SQL
(A-1) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
(B-1) INSERT INTO t VALUES(15);
(B-2) COMMIT;
(A-2) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
(A-3) COMMIT;
```

A-1)번 쿼리가 실행된 경우, 당연히 쿼리 결과는 t.c1 = 13인 row와 t.c1 = 17인 row 2개일 것이다. 그렇다면 lock은 어떻게 걸려있을까? READ COMMITTED transaction은 record lock만 걸고 gap lock은 사용하지 않는다. 따라서 (1)번 쿼리가 실행된 직후 걸려있는 lock은 t.c1 = 13과 t.c1 = 17에 대한 record lock이다.

이 때 transaction B가 t.c1 = 15인 row를 삽입하려고 한다((B-1)번 쿼리). Transaction A는 gap lock을 걸지 않았기 때문에 transaction B는 자유롭게 t.c1 = 15인 row를 삽입할 수 있다. 이 상태에서 transaction B는 commit 했다((B-2)번 쿼리).

이제 다시 transaction A가 (2)번 query를 실행한다. 그러면 transaction A의 isolation level은 READ COMMITTED이기 때문에 새롭게 snapshot을 갱신해온다. 이 과정에서 transaction B가 삽입한 t.c1 = 15인 row를 읽어 들인다. 이것이 바로 phantom row이다.

만약 transaction A의 isolation level이 REPEATABLE READ이었다고 하자. 그러면 (B-1)번 쿼리가 실행될 때 t.c1 = 15인 gap에 gap lock이 걸려있었을 것이다. 따라서 transaction B는 transaction A가 commit 되어 lock을 해제할 때까지 기다리고, phantom read는 일어나지 않는다. 즉, 내가 업데이트 하려는 10 <= t.c1 <= 20에 해당하는 row의 후보군이 변화하는 일이 없다.

### Read Uncommitted

READ UNCOMMITTED transaction은 기본적으로 READ COMMITTED transaction과 동일하다. 대신, SELECT 쿼리를 실행할 때 아직 commit 되지 않은 데이터를 읽어올 수 있다. 예를 들어, 다음과 같은 상황이 가능하다.

Transaction A에서 row를 삽입했다.
READ UNCOMMITTED transaction B가 해당 row를 읽는다.
Transaction A가 rollback 된다.
이 경우, transaction B는 실제로 DB에 한 번도 commit 되지 않은, 존재하지 않는 데이터를 읽어 들였다. 이러한 현상을 dirty read라고 한다.

이것이 가능한 이유는 InnoDB 엔진이 transaction을 commit 하는 방법 때문이다. InnoDB 엔진은 일단 실행된 모든 쿼리를 DB에 적용한다. 그것이 아직 commit 되지 않았어도 적용한다. 즉, 특별히 log를 보고 특정 시점의 snapshot을 복구하는 consistent read를 하지 않고 그냥 해당 시점의 DB를 읽으면 dirty read가 된다. 아래는 해당 내용을 언급한 MySQL reference의 내용이다.

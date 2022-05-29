---  
layout: post
title: "[SQLAlchemy] SQLAlchemy Connection 이해하기"
subtitle: "[SQLAlchemy] SQLAlchemy Connection 이해하기"  
categories: Development 
tags: SQLAlchemy MySQL Postgre
comments: true  
---  

SQLAlchemy는 파이썬 데이터베이스 툴킷으로 다양한 기능등을 제공하고 있습니다. 파이썬 프로젝트로 데이터베이스에 접근해야 하는 경우가 많아 SQLAlchemy에 대해 정리해보고자 합니다. 이번에는 그중에서도 `연결 풀링`에 대해서 정리해보겠습니다.

## 연결 풀링 개념

연결 풀링은 차후에 발생할 DB요청에 대비하여 DB연결을 캐싱하는 기법입니다. 빈번한 DB 요청이 여러 사용자에 의해 발생될때, 매번 연결을 생성하고 닫는 과정을 반복하면 이에 대한 비용이 크기 때문에 이 기법을 사용하여 연결 생성 과정을 줄일 수 있습니다.

## SQLAlchemy의 기본 풀 : 큐풀(QueuePool)

SQLAlchemy 역시 연결 풀을 기본적으로 채택하고 있으며, 그중 기본으로 제공하는 것은 `큐풀(QueuePool)`입니다. 큐풀은 `pool_size`와 `max_overflow`를 바탕으로 복수의 연결 풀을 구성해서 운용합니다.

## 큐 풀의 생애주기

1) 큐 풀이 처음부터 연결을 미리 만드는 것은 아닙니다. 일단 0개로 시작합니다.

2) 요청이 들어올 떄, 큐 풀에 유효 연결이 없으면 하나 생성합니다.

3) 설정된 pool_size까지는 더 연결이 필요하지 않은 상황이라도 연결을 종료하지 않습니다.

4) 요청이 들어올 때, pool_size까지 다 찼다 할지라도 유효 연결이 없으면 초과하여 하나 생성합니다.

r) 4번 이후부터는 오버플로 상황이기 때문에, 큐풀은 적극적으로 오버플로를 방지하기 위해 새로 들어오는 연결을 종료하여 pool_size 에 총 연결 수를 맞춥니다.

6) QueuePool이 관리하는 연결이 pool_size + maxoverflow 까지 다 찬 상황에서 요청이 들어오면 일단 기다리게 합니다. 기본값으로는 30초를 기다립니다.

7) 30초를 기다려도 반환되는 연결이 없으면 TimeoutError 예외를 발생시킵니다.

## 적절한 큐 풀 설정값

서비스가 작을 떄는 기본값이면 충분하지만, 서비스 사용량이 많아지고 규모 문제가 발생하게 된다면 현재 상황에 맞춰 바꿔주는게 좋습니다. 보통 QueuePool 관련 위 언급한 2가지 값 `pool_size`, `max_overflow`를 바꿔주는게 좋은데 기본값은 5, 10입니다.

- `pool_size` : 현재 구성에서 연결 생성 부담을 최소화할 수 있는 가장 작은 값이 되어야 합니다.

- `max_overflow` : 현재 구성에서 데이터베이스, 웹 인스턴스가 물리적으로 버틸 수 있는 최댓값이 되어야 합니다.

- `pool_recycle` : 일정 시간 동안 connection이 없을 경우 연결을 종료하는데 특정 시간 간격으로 재연결하여 connection을 오래 유지할 수 있습니다. (wait_timeout보다 작아야함)
  - Database(Mysql)에서는 기본적으로 어플리케이션에서 커넥션 요청이 올따마다 1개의 process가 생성됨. 생성된 프로세스는 설정된 시간(`wait_timeout`)이 지날때까지 유지. 사용이 끝난 프로세스는 Sleep상태에 돌입하며, 마지막 사용 시간 이후 설정된 프로세스 생존주기(`wait_timeout`)까지 살아있다가 시간이 초과됨과 동시에 프로세스가 제거됨.  

`pool_size`가 과하게 설정되어 있으면 데이터베이스 입장에서 너무 많은 연결을 점유하고 있어 비효율적입니다. 그렇다고 너무 적게 설정한다면 오버플로가 자주 발생하여 풀링으로 얻을 수 있는 효율을 누리지 못합니다. 즉 파이썬 측에서 비효율적입니다.

`max_overflow`가 데이터베이스나 웹 인스턴스의 한계치보다 너무 빡빡하게 잡혀있으면 사용자 유입이 늘어도 TimeoutError를 쉽게 만나거나 서비스 속도 저하를 자주 경험하게 됩니다. 그렇다고 무한으로 두면 사용량 폭증시 잏할 수 없는 에러파티를 경험하게 될 것입니다.

결국 서비스마다 그만의 퍼포먼스와 장비 한계치가 있으니만큼 내부에서 스트레스 테스트를 통한 벤치마킹으로 직정값을 뽑아내는 것을 추천합니다.

```python
def connectDB(host, port, user, password, db, engine_type='None', dbtype='postgresql+psycopg2'):
        connect_args={'ssl':{'fake_flag_to_enable_tls': True}}
        db = '{dbtype}://{user}:{password}@{host}:{port}/{db}?charset=utf8&ssl=true'.format(dbtype='mysql+pymysql', host=host, user=user, port=port, password=password, db=db)
		## QueuePool을 사용하지 않을 때
    if engine_type=='NullPool':
        engine = create_engine(db, connect_args=connect_args, poolclass=NullPool, pool_recycle=100)
		## create engine 단계에서 pool_size, max_overflow, pool_recycle을 옵션으로 줄 수 있다.
    else:
        engine = create_engine(db, pool_recycle=100, connect_args=connect_args)
    conn = engine.connect()
    return conn
```

## Mysql DB Connection 예상 문제 및 해결 방법

1. **Too many connection**

    **원인** : Connection 수가 db에 설정된 max값을 초과해서 발생. Connection을 쓰면 사용한 커넥션 인스턴스 자원을 반납을 해야하는데 하지 않았기 때문에 계속 커넥션 인스턴스가 유지되면서 db관련 api 호출시마다 새로운 연결이 계속해서 추가되서 발생.

    **해결방안** :  연결을 빨리 반환하도록 `wait timeout`을 줄여 sleep process를 자주 정리하도록 한다. 

1. **Lost connection to MYSQL server during query**

    **원인** : Connection Pool위에서 관리중인 Connection Queue들이 Mysql Process에서 제거되었는데도(`wait_timeout`이 초과되서) Application(Queue가 저장된 메모리)에는 DB 프로세스와 링크되어 있었고 이를 사용하면서 발생한 문제. 

    **해결방안** : `pool_recycle`을 `wait_timeout`보다 작게 설정. 예를 들어 `wait_timeout=600`, `recycle_time=500`이면 600초가 지나더라도 500초를 지나는 시점에 application단에서 refresh(recycle옵션을 통해) 하기 때문에 삭제된 프로세스를 가져오는 일이 없어짐

Refernce
- [https://spoqa.github.io/2018/01/17/connection-pool-of-sqlalchemy.html](https://spoqa.github.io/2018/01/17/connection-pool-of-sqlalchemy.html)

- [https://jiku90.tistory.com/14](https://jiku90.tistory.com/14)

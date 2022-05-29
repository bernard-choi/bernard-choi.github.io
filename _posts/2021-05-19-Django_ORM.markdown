---  
layout: post
title: "[Django] Django ORM - 기초"
subtitle: "[Django] Django ORM"  
categories: Development Django
tags: Django
comments: true  
---  

## Django ORM
![django_orm1](https://yunsikus.github.io/assets/img/post_img/django_orm1.jpg)

- `ORM` 이란 Object-Relational Mapping의 약자로 객체(Object)와 관계형 데이터베이스(Relational Database)의 데이터를 매핑해주는 것을 의미함.
    - 객체지향 프로그래밍은 클래스를 사용하고, 관계형 데이터베이스는 테이블을 사용한다. (객체지향 프로그래밍은 필드와 메서드등을 묶어서 캡슐화하고 사용하는 것이 목표이고, RDBMS는 데이터를 잘 정규화해서 보관하는것이 목표)
    - 객체 모델과 관계형 모델 간에 불일치가 존재
    - 데이터베이스 데이터 ← 매핑 → Object 필드

ex) 회원에 나이정보를 추가하려고 함.
Member 클래스에 age를 추가 - `ORM`

Insert, Select, Update등 관련된 모든 쿼리에 age정보를 추가. - `Without ORM`

- 객체간의 관계를 바탕으로 SQL을 자동 생성하여 sql 쿼리문 없이도 데이터베이스의 데이터를 다룰수 있게 해줌 .
- 기존 쿼리문을 작성하여 데이터베이스를 조작하는 것을 넘어서서 더 효율적이고 가독성 및 유지보수에 적합한 코드를 만들기 위해 나온 기술

## 장점

- `SQL query`가 아닌 직관적인 코드로 데이터를 조작할 수 있어 개발자가 객체모델로 프로그래밍하는데 집중할 수 있다.
- 각 객체에 대한 코드를 별도로 작성하기 때문에 코드 가독성이 좋다(선언문, 할당, 종료같은 부수적인 코드가 줄어든다)
- SQL의 절차적이고 순차적인 접근이 아닌 객체지향적인 접근으로 생산성이 좋다.
- 재사용 및 유지보수가 용이하고 매핑정보가 명확하며 ERD의존성이 낮다.
- DBMS(DataBase Management System) 종속성이 낮다.

## 단점

- 완벽한 ORM만으로는 서비스 구현이 어렵다. 사용하기는 편하나 설계는 신중히 해야한다.
- 프로젝트 복잡성이 커질 경우 난이도가 높아진다.
- 잘못 구현하게 되면 속도가 저하되고 일관성이 없어질 수 있다.

## 쿼리셋

- 전달받은 모델의 객체 목록.
- 데이터베이스로부터 데이터를 읽고 필터를 걸거나 정렬등을 할 수 있다.
- 리스트와 구조는 같지만 파이썬 기본 자료구조가 아니기에 읽고 쓰기 위해서는 자료형 변환을 해줘야한다. 쿼리셋은 데이터 베이스의 여러 레코드(row)를 나타낸다.
- 사용자 주문 정보가 담긴 Order object로부터 데이터를 읽어 오면 다음과 같은 쿼리셋을 반환한다.

```python
# 주문 정보
from rest.models import Order
Order.objects.all()

# <QuerySet [<Order: [13] None>, <Order: [14] None>, <Order: [15] None>]>
```

- 여기서 objects는 Model Manager이다. DB와 Django Model 사이의 Query Operation(질의 연산)의 인터페이스 역할을 한다. 이 때, objects를 사용해서 다수의 데이터를 갖고오는 함수를 사용할 때, 반환되는 객체가 Queryset이다.

- 이 Manager 각 모델(클래스)가 최소 하나씩 갖고 있다

- Order.objects의 의미는, objects라는 이름의 manager가 Order 데이터베이스를 QuerySet 형태로 만들겠다는 의미이다. 그 QuerySet에서 데이터를 검색하게 만들 수 있다.

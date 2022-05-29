---  
layout: post  
title: "[Algorithm] Linked List란?"  
subtitle: "[Algorithm] Linked List란?"  
categories: Algorithm
tags: DataStructure Array
comments: true  
---  

## Linked List(연결리스트) 특징

- 각 노드가 데이터와 포인터를 가지고 일렬로 연결되어 있는 방식
- 마지막 항목은 Null을 가리키고 있음. (마지막이라 연결한 노드가 없기 때문)
- 프로그램이 수행되는 동안 크기가 동적으로 커지거나 작아짐
- 메모리 공간 낭비가 적지만 포인터 메모리가 필요함. 
- 배열에 비해 데이터의 추가와 삽입에 용이
- 단방향(양방향이라도) 순차적으로 탐색하지 않으면 요소에 접근이 불가하기 때문에 탐색속도가 떨어짐
- 데이터를 추가하는 건 객체 할당임. 


## 연결리스트의 종류

- Singly linked list(일방향 연결 리스트)
  - 각 노드에 자료 공간과 한 개으 ㅣ포인터 공간이 있고, 각 노드의 포인터는 다음 노드를 가리킨다. 
  
![linkedlist1](https://yunsikus.github.io/assets/img/post_img/linkedlist1.jpg)


- Doubly linked list(이중 연결 리스트)
  - 단일 연결 리스트와 비슷하지만, 포인터 공간이 두 개가 있고 각각의 포인터는 앞과 뒤의 노드를 가리킨다. 

![linkedlist2](https://yunsikus.github.io/assets/img/post_img/linkedlist2.jpg)

- Circular linked list(환형 연결 리스트)
  - 일반적인 연결 리스트에 마지막 노드와 처음 노드를 연결시켜 원형으로 만든 구조
  
![linkedlist3](https://yunsikus.github.io/assets/img/post_img/linkedlist3.jpg)

## Linked List 시간 복잡도

- 데이터 조회: O(N)
- 맨 앞/뒤에 데이터 삽입/삭제하기: O(1) (SinglyLinkedList의 경우 맨 뒤의 데이터 삭제 연산 O(n))
- 중간의 원하는 위치에 데이터 삽입/삭제하기: O(N) (원하는 원소까지 데이터를 조회하는 과정이 있으므로 O(n) + O(1))


## 배열 vs 링크드 리스트

배열과 링크드 리스트는 데이터를 나열한 다는 점에서 자주 비교된다. 

1. **데이터 접근**
   - 배열은 index만 알고 있다면 메모리의 바로 접근할 수 있기 때문에 O(1)의 시간 복잡도를 가지지만, 링크드 리스트는 연결되어 있는 노드를 탐색하여 접근해야 하기 때문에 O(n)의 시간복잡도를 가지며 배열에 비해 속도가 느리다. 

2. **데이터 삽입, 제거**
    - 배열은 데이터 삽입, 제거 시 기존의 데이터들의 위치를 이동시키는 경우가 많아 비효율적일 수 있지만, 리크드 리스트는 삽입 제거하려는 데이터의 이전, 이후 노드의 링크만 연결시키면 되기 때문에 효율적
    - 다만 LinkedList는 데이터 삽입/삭제마다 메모리 할당/해제가 일어나므로 시간복잡도는 빠를지라도 시스템콜에 있어서 Array보다 더 시간이 걸린다. 

3. **메모리 공간 활용**
    - 배열은 고정된 크기의 메모리를 할당받아 크기를 변결할 수 없지만 링크드 리스트는 연결되어 있는 노드에 따라 크기가 동적으로 변하므로 효율적으로 메모리를 사용할 수 있다. 

## Reference

[https://velog.io/@gillog/%EC%97%B0%EA%B2%B0-%EB%A6%AC%EC%8A%A4%ED%8A%B8Linked-List](https://velog.io/@gillog/%EC%97%B0%EA%B2%B0-%EB%A6%AC%EC%8A%A4%ED%8A%B8Linked-List)
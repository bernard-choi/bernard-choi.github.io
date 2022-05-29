---  
layout: post
title: "[Python] Leetcode 232. Implement Queue Using Stack"
subtitle: "[Python] Leetcode 232. Implement Queue Using Stack"  
categories: Algorithm
tags: 문제풀이 자료구조 Stack Queue
comments: true  
---  

## [Leetcode 232 Implement Queue Using Stack](https://leetcode.com/problems/implement-queue-using-stacks/)

## 1. 문제

큐를 이용해 다음 연산을 지원하는 스택을 구현하라

- push(x) : 요소 x를 스택에 삽입한다. 
- pop(): 스택의 첫 번째 요소를 삭제한다.
- top(): 스택의 첫 번째 요소를 가져온다. 
- empty(): 스택이 비어 있는지 여부를 리턴한다. 


## 설명

- 스택은 앞에서만 푸시 팝 가능, 큐는 뒤에서 푸시 앞에서는 팝 가능.
- 때문에 뒤에 푸시된 값을 앞으로 보내서 popleft 해야함. 
- 요소 삽입 후 맨 앞에 두는 상태로 재정렬하면 됨. 


## 풀이

```python
class MyStack:

    def __init__(self):
        self.q = collections.deque()
        
    def push(self, x: int) -> None:
        self.q.append(x)
        # 요소 삽입 후 맨 앞에 두는 상태로 재정렬
        for _ in range(len(self.q)-1):
            self.q.append(self.q.popleft())

    def pop(self) -> int:
        return self.q.popleft()
        

    def top(self) -> int:
        return self.q[0]
        

    def empty(self) -> bool:
        return len(self.q) == 0
```
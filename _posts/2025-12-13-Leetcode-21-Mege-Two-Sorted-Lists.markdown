---
layout: post
title: "[Python] Leetcode 21. Merge Two Sorted Lists"
subtitle: "[Python] Leetcode 21. Merge Two Sorted Lists"
categories: Algorithm
tags: 문제풀이 Linked List
comments: true


---
## [Leetcode 21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/description/)

## 문제

You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.



![merge_two_sorted_lists](https://bernard-choi.github.io/assets/img/post_img/merge_two_sorted_lists.png)


## 풀이

- Linked List 자료구조가 익숙하지 않음. 인풋 형식을 주석으로 첨부함.

```python
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

# node4 = ListNode(4)
# node3 = ListNode(3, node4)
# node2 = ListNode(2, node3)
# node1 = ListNode(1, node2)
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        prehead = ListNode(-1)
        prev = prehead
        while list1 and list2:
            if list1.val < list2.val:
                prev.next = list1
                list1 = list1.next
            else:
                prev.next = list2
                list2 = list2.next
            prev = prev.next
            
        prev.next = list1 if list1 is not None else list2
        return prehead.next
```


- linkedlist 구현 코드도 추가

```python
class Node:
    def __init__(self, x):
        self.data = x
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None
    
    def append(self, x):
        if self.head is None:
            self.head = Node(x)
            return
        
        current = self.head
        while current.next:
            current = current.next
        current.next = Node(x)

    def display(self):
        elements = []
        current = self.head
        while current:
            elements.append(str(current.data))
            current = current.next
        print('->'.join(elements))

ll1 = LinkedList()
ll1.append(10)
ll1.append(11)
ll1.append(12)
ll1.display() #  10->11->12
```
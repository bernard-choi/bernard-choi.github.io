---
layout: post
title: "[Python] Leetcode 206. Reverse Linked List"
subtitle: "[Python] Leetcode 206. Reverse Linked List"
categories: Algorithm
tags: 문제풀이 Linked List
comments: true


---
## [Leetcode 206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/description/)

## 문제

Given the head of a singly linked list, reverse the list, and return the reversed list.

![reverse_linked_list_1](https://bernard-choi.github.io/assets/img/post_img/reverse_linked_list_1.jpg)


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
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        my_stack = []
        while node: # [node1, node2, node3, node4]
            my_stack.append(node)
            node = node.next

        dummy = ListNode(-1)
        node = dummy

        while my_stack: # [node4, node3, node2, node1]
            node.next = my_stack.pop()
            node = node.next # dummy -> node4 -> node3 -> node2 -> node1

        node.next = None # node1 -> none

        return dummy.next
```

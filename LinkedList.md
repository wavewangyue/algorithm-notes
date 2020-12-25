# Linked List Questions

## 1. 判断链表有环

`O(n)时间 O(1)空间` 设置一个步长为 2 的快指针和步长为 1 的慢指针，如果有环两个指针必会相遇

```python
def hasCycle(self, head):
    if head is None:
        return False
    fast = head.next
    slow = head
    while fast is not None:
        if fast == slow:
            return True
        if fast.next is None:
            return False
        fast = fast.next.next
        slow = slow.next
    return False
```

## 2. 找到两个链表的交点

`O(n)时间 O(1)空间` 设置两个指针，一个扫描 A+B，一个扫描 B+A，指针相遇则为交点

```python
def getIntersectionNode(self, headA, headB):
    pA = headA
    pB = headB
    while (pA is not None) and (pB is not None):
        if pA == pB:
            return pA
        pA = pA.next
        pB = pB.next
        if pA is None:
            pA, headB = headB, None
        if pB is None:
            pB, headA = headA, None
    return None
```

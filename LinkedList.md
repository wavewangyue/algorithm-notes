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

## 3. LRU缓存器

实现一个 LRU(最近最少使用) 缓存器，缓存器内存储了一个map，且有固定容量。当缓存容量超过上限时，删除最久未使用的数据

需要用用哈希表 + 双向链表实现，本质上相当于实现一个 python 的 OrderedDict
 
```python
class LinkedNode: # 双向链表
    def __init__(self, key=0, value=0):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = dict()
        self.head = LinkedNode() # 设两个虚节点，避免下面写很多 if xxx is None 这种判断
        self.tail = LinkedNode()
        self.head.next = self.tail
        self.tail.prev = self.head
        self.capacity = capacity
        self.size = 0

    def get(self, key: int) -> int:
        if key not in self.cache: return -1
        node = self.cache[key]
        self.moveToTail(node) # 每次使用过，把节点更新到队尾
        return node.value

    def put(self, key: int, value: int) -> None:
        if key not in self.cache:
            node = LinkedNode(key, value)
            self.cache[key] = node
            self.addToTail(node) # 新增节点到队尾
            self.size += 1
            if self.size > self.capacity: # 如果超出容量，删除队头节点
                removed = self.popHead()
                self.cache.pop(removed.key)
                self.size -= 1
        else:
            node = self.cache[key]
            node.value = value
            self.moveToTail(node) # 更新节点到队尾

    def addToTail(self, node):
        self.tail.prev.next = node
        node.next = self.tail
        node.prev = self.tail.prev
        self.tail.prev = node        

    def removeNode(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def moveToTail(self, node):
        self.removeNode(node)
        self.addToTail(node)

    def popHead(self):
        node = self.head.next
        self.removeNode(node)
        return node
```

## 4. Trie字典树

实现一个 Trie(字典树)，包含 insert, search, 和 startsWith 这三个操作

```python
class TreeNode:
    def __init__(self):
        self.k2child = {} # key是单词中的某个字符，value是下一层节点
        self.endpoint = False # 标记此节点是否是一个单词的结束

class Trie:
    def __init__(self):
        self.root = TreeNode()

    def insert(self, word: str) -> None:
        node, i = self.sink(word) # node是最多下沉到哪个节点，i标记滑到了单词第几个字符
        while i < len(word):
            new_node = TreeNode()    
            node.k2child[word[i]] = new_node
            node = new_node
            i += 1
        node.endpoint = True

    def search(self, word: str) -> bool:
        node, i = self.sink(word)
        return i == len(word) and node.endpoint

    def startsWith(self, prefix: str) -> bool:
        node, i = self.sink(prefix)
        return i == len(prefix)

    def sink(self, word: str):
        p = self.root
        i = 0
        while i < len(word) and word[i] in p.k2child:
            p = p.k2child[word[i]]
            i += 1
        return p, i
```

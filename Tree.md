# Tree Questions

## 1. 二叉树中序遍历

递归写法

```python
def dfs(root, res):
    if root is not None:
        if root.left is not None: dfs(root.left, res)
        res.append(root.val)
        if root.right is not None: dfs(root.right, res)
```

非递归写法，即用数组 stack 记录走过的节点，模拟递归栈

```python
def inorderTraversal(root):
    res = []
    stack = []
    p = root
    while not (p is None and stack == []):
        while p is not None:
            stack.append(p)
            p = p.left
        p = stack.pop()
        res.append(p.val)
        p = p.right
```

## 2. 最小堆

```python

# 由数组建堆
def build_heap(nodes):
        # 最后一个叶子节点：m = len(nodes)-1
        # 最后一个非叶子节点：f = father(m) = (m-1) // 2
        # 从最后一个非叶子节点开始往前遍历
        for i in range((len(nodes)-2)//2, -1, -1): 
            tune_down(nodes, i)
            
# 从上向下调整堆
def tune_down(self, nodes, root):
    left = 2 * root + 1 # 左子
    right = 2 * root + 2 # 右子
    if left < len(nodes):
        if right < len(nodes) and nodes[right] < nodes[left]: # 选择左右子中更小的一个
            k = right
        else:
            k = left
        if nodes[root] > nodes[k]:
            nodes[root], nodes[k] = nodes[k], nodes[root]
            self.tune_down(nodes, k) # 递归向下
            
 # 从下向上调整堆
def tune_up(self, nodes, root):
    if root > 0:
        father = (root - 1) // 2
        if nodes[root] < nodes[father]:
            nodes[root], nodes[father] = nodes[father], nodes[root]
            self.tune_up(nodes, father) # 递归向上

# 删除堆顶
def pop(nodes):
    res = nodes.pop(0)
    if len(nodes) > 0:
        nodes.insert(0, nodes.pop()) # 将最后一个叶子节点挪到根
        tune_down(nodes, 0)
    return res

# 新增元素
def insert(nodes, new_node):
    nodes.append(new_node)
    tune_up(nodes, len(nodes)-1)
  
```

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

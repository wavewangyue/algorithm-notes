# Tree & Graph Questions

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

最小（最大）堆是一个完全二叉树，在每个节点上，节点本身的值比左右子节点都小

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

## 3. 并查集

有若干个点，彼此相连，构成图。求一个图中的极大联通子图的个数

如果处理好存在环的情况，每个联通子图可以看成一棵树，每颗树有唯一的根，统计树根的数量就ok

```python
matrix = [[1,0,0,0,1],[0,1,0,1,0],[0,0,1,1,1],[0,1,1,1,0],[1,0,1,0,1]] # 邻接矩阵
n = len(matrix)
roots = list(range(n)) # 记录每个节点的树根
depths = [1] * n # 记录每颗树的深度，防止树过深，提高效率

def find_root(): # 寻找树根
    if roots[i] == i:
        return i
    else:
        roots[i] = find_root(roots[i]) # 为了缩短查询路径，找到树根后，直接把节点挂上去
        return roots[i]

def merge(i, j): # 合并两节点所在的树
    root1 = find_root(i)
    root2 = find_root(j)
    if root1 != root2:
        if depths[root1] == depths[root2]: # 如果深度相同，随便选一棵挂到另一棵上，新树深度加一
            roots[root2] = root1
            depths[root1] += 1
        elif depths[root1] > depths[root2]: # 如果深度不同，浅的树挂深的树上，深度不变
            roots[root2] = root1
        else:
            roots[root1] = root2

# 遍历邻接矩阵，建树
for i in range(n):
    for j in range(i + 1, n):
        if matrix[i][j] == 1:
            merge(i, j)

# 统计树根的数量
print(len(set([find_root(i) for i in range(n)])))
```
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

非递归写法，即用数组 stack 记录走过的结点，模拟递归栈

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

最小堆是一类完全二叉树（即从上到下从左到右没有空结点），在每个结点上，结点本身的值比左右子结点都小

```python
# 由数组建堆
def build_heap(nodes):
    # 最后一个叶子结点：m = len(nodes)-1
    # 最后一个非叶子结点：f = father(m) = (m-1) // 2
    # 从最后一个非叶子结点开始往前遍历
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
        nodes.insert(0, nodes.pop()) # 将最后一个叶子结点挪到根
        tune_down(nodes, 0)
    return res

# 新增元素
def insert(nodes, new_node):
    nodes.append(new_node)
    tune_up(nodes, len(nodes)-1)
```

## 3. 二叉搜索树

#### a. 普通二叉搜索树

针对每个结点，左子树上所有结点比它小，右子树上所有结点比它大，查找插入删除操作都是`O(logn)`

```python
# 查找
def search(val, root):
    if val == root.val:
        return root
    elif val < root.val:
        return search(val, root.left) if root.left else None
    else:
        return search(val, root.right) if root.right else None

# 新增结点
def insert(val, root):
    if val < root.val:
        root.left = insert(val, root.left) if root.left else Node(val)
    elif val > root.val:
        root.right = insert(val, root.right) if root.right else Node(val)
    return root

# 删除结点
def delete(val, root):
    if val < root.val:
        if root.left: root.left = delete(val, root.left)
    elif val > root.val:
        if root.right: root.right = delete(val, root.right)
    else:
        if root.left is None: # 若没有左子树，则右子树直接上位
            root = root.right if root.right else None
        elif root.right is None: # 若没有右子树，则左子树直接上位
            root = root.left if root.left else None
        else: # 若左右子树都在，则在左子树找一个最大的顶替自己
            target = root.left
            while target.right:
                target = target.right
            root = delete(root, target.val)
            root.val = target.val
    return root
```

#### b. 平衡二叉树

一类二叉搜索树，左右子树深度差不超过 1，防止最差情况下二叉搜索树深度过大退化成一个链表。其查找效率稳定，但插入删除操作繁琐，涉及一些旋转操作

#### c. 红黑树

是一类不严格的平衡二叉树，通过给结点加不同颜色，来仅保证一个子树的深度不会超过另一个子树的 2 倍，减少了旋转操作。目前最广泛使用

## 4. 并查集

有若干个点，彼此相连，构成图。求一个图中的极大联通子图的个数

如果处理好存在环的情况，每个联通子图可以看成一棵树，每颗树有唯一的根，统计树根的数量就ok

```python
matrix = [[1,0,0,0,1],[0,1,0,1,0],[0,0,1,1,1],[0,1,1,1,0],[1,0,1,0,1]] # 邻接矩阵
n = len(matrix)
roots = list(range(n)) # 记录每个结点的树根
depths = [1] * n # 记录每颗树的深度，防止树过深，提高效率

def find_root(i): # 寻找树根
    if roots[i] == i:
        return i
    else:
        roots[i] = find_root(roots[i]) # 为了缩短查询路径，找到树根后，直接把结点挂上去
        return roots[i]

def merge(i, j): # 合并两结点所在的树
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

## 5. 图上是否有环（拓扑排序）

如果是无向图
1. 统计每个结点的度，忽略度为 0 的结点
2. 将度为 1 的结点扔掉, 所有相连结点度减 1
3. 重复第 2 步，直到没有度为 1 的结点
4. 判断是否所有点都被扔掉了，如果不是则有环

如果是有向图
1. 只统计入度
2. 改成扔入度为 0 的结点

```python
n = 4 # 结点个数
edges = [[1,0],[1,2],[0,2],[2,3]] # 边缘列表表示的图，即图上所有边组成的列表
degrees = [0] * n # 每个结点的度
for edge in edges:
    degrees[edge[0]] += 1
    degrees[edge[1]] += 1
# 寻找度为 1 的结点入栈
visited = 0
stack = []
for i in range(n):
    if degrees[i] == 0:
        visited += 1
    elif degrees[i] == 1:
        stack.append(i)
# 循环出栈度为 1 的结点
while stack != []:
    node = stack.pop(0)
    visited += 1
    for edge in edges:
        if edge[0] == node or edge[1] == node:
            node2 = edge[1] if edge[0] == node else edge[0]
            degrees[node2] -= 1
            if degrees[node2] == 1:
                stack.append(node2)        
# 如果有结点未访问到，则有环
print(visited != n)
```

## 6. 二分图最大匹配（匈牙利算法）

二分图：两组节点，节点只能跨组相连，组内节点均不互连

最大匹配：两个相连的节点凑成一对，多对不能共享节点，最多能凑几对

匈牙利算法：
1. 遍历组 S1 中的每个节点 i，执行第 2 步
2. 遍历组 S2 中与节点 i 相连的每个节点 j，如果 j 还没被配对，直接和 i 配对；如果 j 被配对了，执行第 3 步
3. 对于 j 已经配对的对象 k，迭代执行第 2 步（相当于给 k 换个对象，如果能换就换，不能换就当无事发生，让 i 继续单身）

```python

s1 = ["a", "b", "c", "d"] # 第一组节点
s2 = ["A", "B", "C", "D"] # 第二组节点
m = [[1,1,0,0], [0,1,0,1], [1,0,1,0], [0,0,0,1]] # 邻接矩阵，m[i][j]表示组1的第i个节点和组2的第j个节点的连接关系
partners = [-1] * len(s2) # 记录第二组每个节点的匹配对象
flags = [False] * len(s2) # 访问记录标记

def matching(i):
    for j in range(len(s2)):
        if (not flags[j]) and (m[i][j] == 1):
            flags[j] = True
            if partners[j] == -1 or matching(partners[j]): # 如果j还没配对，或者j的对象可以换个对象
                partners[j] = i
                return True
    return False

ct = 0
for i in range(len(s1)):
    flags = [False] * len(s2) # 每次从一个新的i出发，重置一下访问记录
    if matching(i):
        ct += 1
```

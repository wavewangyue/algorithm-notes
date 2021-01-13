# Array Questions

## 1. 寻找中位数

#### a. 单个无序数组

寻找中位数，或者寻找第 K 小的数

`O(n) < O < O(nlogn)` 快排思想（二分思想），先随机找基准，将数分为大小两部分，再分治，直到基准值下标在所需位置

```python
n = len(nums)
if n % 2 == 1: # 长度是奇数，中位数是中间数
    return find(nums, (n+1)//2)
else: # 长度是偶数，中位数是中间两数的平均数
    return (find(nums, n//2) + find(nums, n//2+1)) / 2.0

def find(nums, k): # 寻找第 K 小的数
    left = 0
    right = len(nums) - 1
    while left < right:
        tmp = nums[(left + right) // 2]
        i, j = left, right
        while (i < j) and (nums[i] < tmp): i += 1
        while (i < j) and (nums[j] > tmp): j -= 1
        if i < j:
            if nums[i] == nums[j]:
                i += 1
            else:
                nums[i], nums[j] = nums[j], nums[i] # 交换nums[i]与nums[j]
        if i < k: # 整个数组里 i 左边的数都比 tmp 小
            left = i + 1
        elif i > k:
            right = i - 1
        else:
            return tmp
```

#### b. 两个有序数组

两个递增数组，合并到一起寻找中位数

`O(logn)` 也是二分思想，二分的不是下标，而是 k。两个数组分别设指针从左往右滑，每次滑动不超过 k/2

```python
def find(self, nums1, nums2, k): # 在两个有序数组中寻找第 K 小的数
    n1, n2 = len(nums1), len(nums2)
    i1 = i2 = 0
    while True:
        if i1 == n1: # nums1 到头了，需要在 nums2 再找 k-1 个数
            return nums2[i2 + k - 1]
        if i2 == n2: # nums2 到头了，需要在 nums1 再找 k-1 个数
            return nums1[i1 + k - 1]
        if k == 1: # 此时只需要找最小的数了
            return min(nums1[i1], nums2[i2])
        # 两个指针往右滑动，不超过 k/2
        half_k = k // 2
        j1 = min(i1 + half_k, n1) - 1
        j2 = min(i2 + half_k, n2) - 1
        if nums1[j1] <= nums2[j2]:
            k -= j1 + 1 - i1
            i1 = j1 + 1
        else:
            k -= j2 + 1 - i2
            i2 = j2 + 1
```

#### c. 有序矩阵

矩阵中每行每列都是递增的，寻找矩阵中的中位数

`O(n) < O < O(nlogn)` 也是二分思想，但是二分的不是下标，而是数值。每次找到基准后，需要统计整个矩阵中小于基准值的元素个数

```python
matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]
] # 方阵

def find(matrix, k): # 在矩阵中寻找第 K 小的数
    n = len(matrix)
    left = matrix[0][0]
    right = matrix[n-1][n-1]
    while left < right:
        mid = (left + right) // 2
        ct = self.count(mid, matrix, n)
        if ct < k:
            left = mid + 1
        # 如果 ct=k 时不能直接返回mid，因为mid有可能不存在于矩阵中，只有mid的ct=k且mid-1的ct<k的时，mid才一定在矩阵中
        else:
            right = mid
    return left

def count(mid, matrix, n): # 利用矩阵递增规律，从矩阵左下往右上统计
    i = n - 1
    j = 0
    ct = 0
    while i >= 0 and j < n:
        if matrix[i][j] <= mid:
            ct += i + 1
            j += 1 # 遇到小数，往右走
        else:
            i -= 1 # 遇到大数，往上走
    return ct
```

#### d. 数据流

给定一串操作序列，每个时刻的操作包含两种可能：（1）新增元素（2）查看此时的中位数

三种方案：
- `O(n)` 插入排序思想，维护一个有序数组，通过二分查找将新元素插入到合适位置。由于需要向数组中插数，数组平移，插入操作复杂度略高
- `O(logn)` 维护两个堆，一个最大堆，一个最小堆，同时维持两个堆的平衡，当一个堆较大时就往另一个堆传数，维持中位数始终在堆顶
- `O(logn)` 平衡二叉树，维持根节点是中位数


## 2. 盛最多水的容器

若干柱子列一排，哪两个柱子之间组成的空间最大

`O(n)` 左右出发向中间相遇，每次较短的一端向前走一步

```python
def maxArea(self, heights):
    i = 0
    j = len(heights) - 1
    res = (j - i) * min(heights[i],heights[j])
    while i < j:
        if (heights[i] <= heights[j]):
            i += 1
            if res < (j - i) * min(heights[i],heights[j]):
                res = (j - i) * min(heights[i],heights[j])
        else:
            j -= 1
            if res < (j - i) * min(heights[i],heights[j]):
                res = (j - i) * min(heights[i],heights[j])
    return res
```

## 3. 缺失的第一个正数

找出数组中没有出现的最小的正整数

`O(n)时间 O(1)空间` 假设将所有数 x 调整到 nums[x-1] 的位置，调整后数组应当有 [1,2,...,N] 的形式，但其中有若干个位置上的数是错误的，第一个错误的位置就代表了一个缺失的正数。实现上，循环把 nums[i] 放到 nums[nums[i]-1] 即可

```python
def firstMissingPositive(self, nums):
    for i in range(len(nums)):
        while (nums[i] > 0) and (nums[i] <= i+1) and (nums[i] != nums[nums[i]-1]):
            nums[nums[i]-1], nums[i] = nums[i], nums[nums[i]-1]
    for i in range(len(nums)):
        if nums[i] != i+1:
            return i+1
    return len(nums)+1
```

## 4. 寻找重复数

给定一个包含 n+1 个整数的数组，数值都在 1 到 n 之间，存在一个重复数

`O(nlogn)时间 O(1)空间` 二分法，选定基准值 1<=p<=n，如果 nums 中小于 p 的数大于 p 个，那么重复数字出现在 1<=q<=p

```python
def findDuplicate(self, nums):
    l = 0
    r = len(nums) - 1
    while l <= r:
        p = (l + r) / 2
        cnt = 0
        for num in nums:
            if num <= p:
                cnt += 1
        if cnt <= p:
            l = p + 1
        else:
            r = p - 1
    return l
```

## 5. 拼接数字组成的最大数

给定一组正整数 nums，重新排列它们的顺序，然后按顺序拼接，能组成的最大数

把数组转字符串，字符串排序，需要新定义一个排序规则用来比较 concat(s1,s2) 与 concat(s2,s1)

```python
import functools

def largestNumber(nums):
    return ''.join(sorted(map(str, nums), key=functools.cmp_to_key(compare), reverse=True))

def compare(a, b):
    ab = a + b
    ba = b + a
    if ab < ba:
        return -1
    elif ab > ba:
        return 1
    else:
        return 0
```

## 6. 柱状图中的最大矩形

给定一个正整数组，用来表示柱状图中各个柱子的高度。每个柱子宽度为 1 且彼此相邻。求柱状图中能圈出来的最大矩形的面积

> 举例，假设给定数组 [2,1,5,4,2,3]，画出来的柱状图是这样的：  
> □ □ ■ □ □ □  
> □ □ ■ ■ □ □  
> □ □ ■ ■ □ ■  
> ■ □ ■ ■ ■ ■  
> ■ ■ ■ ■ ■ ■  
> 图中能圈出来的最大矩形面积是 8

维护两个数组 left 与 right，其中 left[i] 和 right[i] 分别表示：按 i 这个位置这个高度往左右扩，能达到的左边界和右边界

所以 `heights[j] >= heights[i], for j in range(left[i], i)`，即：left[i] 是从 i 开始往左离它最近的、高度小于它的柱子

为了提升效率，通过**单调栈**来辅助完成，当遍历到 i 时，将栈中 `heights[v] > heights[i]` 的所有元素 v 出栈，i 再进栈

```python
def largestRectangleArea(self, heights: List[int]) -> int:
    left = [None] * len(heights)
    right = [None] * len(heights)
    # 从左到右遍历一遍记录 left[]
    stack = []
    for i in range(len(heights)):
        while stack != [] and heights[stack[-1]] >= heights[i]:
            stack.pop()
        left[i] = stack[-1] + 1 if stack != [] else 0
        stack.append(i)
    # 从右到左遍历一遍记录 right[]
    stack = []
    for i in range(len(heights)-1, -1, -1):
        while stack != [] and heights[stack[-1]] >= heights[i]:
            stack.pop()
        right[i] = stack[-1] - 1 if stack != [] else len(heights)-1
        stack.append(i)
    # 最后遍历一次统计结果
    max_size = 0
    for i in range(len(heights)):
        size = (right[i] - left[i] + 1) * heigh ts[i]
        if size > max_size: max_size = size
    return max_size
```

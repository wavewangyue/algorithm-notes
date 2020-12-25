# Array Questions

## 1. 寻找中位数

或者寻找第K大的数

`O(n) < O < O(nlogn)` 快排思想，先随机找基准，将数分为大小两部分，再分治，直到基准值下标在所需位置

```python
def sub(self, nums, start, end, k):
        i, j = start, end
        tmp = nums[(start + end) // 2]
        while i < j:
            while (i < j) and (nums[i] < tmp):
                i += 1
            while (i < j) and (nums[j] > tmp):
                j -= 1
            if i < j:
                if nums[i] == nums[j]:
                    i += 1
                else:
                    nums[i], nums[j] = nums[j], nums[i]
        maxK_now = end + 1 - i
        if maxK_now == k:
            return tmp
        elif maxK_now > k:
            return self.sub(nums, i+1, end, k)    
        else:
            return self.sub(nums, start, i-1, k-maxK_now)
```

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

# Array Questions

### 寻找中位数

或者寻找第K大的数

O(n)：快排思想，先随机找基准，将数分为大小两部分，再分治，直到基准值下标在所需位置
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

### 盛最多水的容器

若干柱子列一排，哪两个柱子之间组成的空间最大

O(n)：左右出发向中间相遇，每次较短的一端向前走一步

```python
def maxArea(self, height: List[int]) -> int:
    if len(height) <= 1:
        return 0
    else:
        i = 0
        j = len(height) - 1
        res = (j - i) * min(height[i],height[j])

        while i < j:
            if (height[i] <= height[j]):
                i += 1
                if res < (j - i) * min(height[i],height[j]):
                    res = (j - i) * min(height[i],height[j])
            else:
                j -= 1
                if res < (j - i) * min(height[i],height[j]):
                    res = (j - i) * min(height[i],height[j])
        return res
```

### 寻找第一个缺失的正数

找出数组中没有出现的最小的正整数）：O(n)时间，O(1)空间，循环把nums[i]放到nums[nums[i]]即可

### 寻找重复数

（数组中数字在1-n间）：O(n^2)时间，O(1)空间，二分法，如果1~m中数目超过m，那么重复数字出现在1~m

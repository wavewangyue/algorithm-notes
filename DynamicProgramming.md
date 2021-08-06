# Dynamic Programming Questions

## 1. 连续子数组

#### a. 连续子数组的最大和

给定一个数组，求所有的连续子数组中，和最大的那个

`O(n)` 直接从左到右滑，假设当前到数字 num，已经积累的最大和 max_now，如果 max_num+num < num，直接抛弃之前的数。状态转移函数 `max_now = max(max_now + num, num)`

```python
max_overall = max_now = nums[0]  
for num in nums[1:]:  
   max_now = max(max_now + num, num)  
   if max_now > max_overall: max_overall = max_now
```

#### b. 连续子数组的最大积

给定一个数组，求所有的连续子数组中，乘积最大的那个

`O(n)` 类似上面，但是由于乘积有正负，目前积累的最大正数乘一个负数后会变成一个最小负数，所以需要两个数字分别记录目前的最大和最小值，状态转移函数 `max_now = max(max_now * num, min_now * num, num)`

```python
max_overall = max_now = min_now = nums[0]
for num in nums[1:]:
    max_now, min_now = max(max_now * num, min_now * num, num), min(max_now * num, min_now * num, num)
    if max_now > max_overall: max_overall = max_now
```

## 2. 背包问题

背包问题：有一个背包，背包承重有限。有一堆物品，每个物品有各自的重量与价值。求能放进背包里面的物品的最大总价值

#### a. 完全背包问题

每种物品可以使用无限次

`O(n*m)` 假设把第 i 件物品放进空间 j 的包中，状态转移函数 `f[j] = max(f[j], f[j-weight[i]] + value[i])`

```python
# n = 物品个数
# m = 背包空间
# weight[] = 物品尺寸列表
# value[] = 物品价值列表
for i in range(1, n+1):
    for j in range(weight[i], m+1):
        f[j] = max(f[j], f[j-weight[i]] + value[i])
```

#### b. 0-1背包问题

每种物品仅可使用一次

`O(n*m)` 二维数组，通过第一维来防止重复使用，状态转移函数 `f[i][j] = max(f[i-1][j], f[i-1][j-weights[i]] + values[i])`

```python
for i in range(n+1):
    for j in range(m+1):
        if weights[i] > j:
            f[i][j] = f[i-1][j]
        else:
            f[i][j] = max(f[i-1][j], f[i-1][j-weights[i]] + values[i])
```

也可以写成完全背包那种简洁的一维数组形式，仅需要把 j 循环倒过来，具体原因难以言表

```python
for i in range(1, n+1):
    for j in range(m, weight[i]-1, -1):
        f[j] = max(f[j], f[j-weight[i]] + value[i])
```

#### c. 多重背包问题

每种物品可使用有限次，转化成01背包问题即可

## 3. 编辑距离

求两个字符串的编辑距离，编辑分三种：增加，删除，修改

`O(n*m)` 状态转移函数 `dp[i,j] = min(dp[i-1,j] + 1, dp[i,j-1] + 1, dp[i-1,j-1] + 1)`

```python
def Levenshtein_Distance(str1, str2):
    dp = [[i + j for j in range(len(str2)+1)] for i in range(len(str1)+1)]
    for i in range(1, len(str1)+1):
        for j in range(1, len(str2)+1):
            if(str1[i-1] == str2[j-1]):
                d = 0
            else:
                d = 1
            dp[i][j] = min(dp[i-1][j]+1, dp[i][j-1]+1, dp[i-1][j-1]+d)
    return dp[len(str1)][len(str2)]
```

## 4. 买卖股票

给定一个数组，表示一支股票一段时间来每天的价格，无限次买卖，求最大收益

#### a. 无冷冻期

只要后一天比前一天价格高，当天买隔天卖，就算赚

```python
profit = 0
for i in range(1, len(prices)):
    profit += max(price[i] - price[i-1], 0)
```

#### b. 有冷冻期

每次卖完，需要冷冻期一天，才能再买

对于每天，共有三个可能状态：买、卖、冷冻，分别用数组表示，其中 buy[i], sell[i], cool[i] 分别表示在第 i 天执行买、卖、冷冻操作可带来的最大收益

随着时间推进，三种状态互相跳转，真正的**状态转移**

```python
buy, sell, cool = [0] * len(prices), [0] * len(prices), [0] * len(prices)
buy[0] = -prices[0]
for i in range(1, len(prices)):
    buy[i] = max(cool[i-1] - prices[i], buy[i-1]) # 冷冻状态 -> 买状态
    sell[i] = max(buy[i-1] + prices[i], sell[i-1]) # 买状态 -> 卖状态
    cool[i] = sell[i-1] # 卖状态 -> 冷冻状态
```

## 5. 最长上升子序列

找到数组中最长的数值递增的子序列（子序列 ≠ 子串，不一定是连续的）

`O(n*logn)` 从左往右 O(n) 扫描，设当前最长子序列为 LIS，如果 num 比 LIS[-1] 大则直接入队，如果 num 比 LIS[-1] 小则 O(logn) 二分查找 LIS 中第一个比 num 大的数替换为 num

```python
def lengthOfLIS(nums):
    b = []
    for num in nums:
        if (b == []) or (num > b[-1]):
            b.append(num)
        elif num < b[-1]:
            b[find(b, num)] = num
    return len(b)

def find(nums, obj):
    l = 0
    r = len(nums) - 1
    while l <= r:
        mid = (l + r) / 2
        if nums[mid] < obj:
            l = mid + 1
        else:
            r = mid - 1
    return l
```

## 6. 最长回文子串

给定字符串 s，找到 s 中最长的回文子串

`O(n)` **马拉车算法**，从左往右走，设 center 与 right 为已经发现的最靠右的回文串的中心与右边界，i 关于 center 的镜像点为 2\*center-i，则 `p[i]=p[2*center-i]`，如果 i 超出了 right，则从 i 开始重新扩张找回文串

```python
def longestPalindrome(self,s):
    s_plus = "#"
    for c in s:
        s_plus += c + "#" # 用 # 将字符串 s 用补全为奇数长度的 s_plus
    p = [1] * len(s_plus)
    center = 0
    right = 0
    max_center = 0
    for i in range(len(s_plus)):
        p[i] = min(p[2 * center - i], right - i) if right > i else 1
        while (i + p[i] < len(s_plus)) and (i - p[i] >= 0) and (s_plus[i + p[i]] == s_plus[i - p[i]]):
            p[i] += 1
        if i + p[i] > right:
            right = i + p[i]
            center = i
        if p[i] > p[max_center]:
            max_center = i
    max_left = max_center - p[max_center] + 1
    max_right = max_center + p[max_center] - 1
    return s_plus[max_left:max_right+1].replace("#", "")
```

## 7. 和为K的连续子数组

给定一个数组，在长度任意的所有连续子数组中，有多少个子数组的和正好为K

`O(n)` 不用遍历所有子数组。设 `f[i]=sum(0,i)` 则 `sum(i,j)=f[j]-f[i-1]` 那么只需要找 `f[j]-k=f[i]` 即可以，其中 j 是滑动指针，i是之前滑动过的位置

```python
def subarraySumK(nums, k):
   sum2count = {0: 1}
   sum, ans = 0, 0
   for num in nums:
      sum += num
      if sum - k in sum2count:
         ans += sum2count[sum-k]
      sum2count[sum] = sum2count[sum] + 1 if sum in sum2count else 1
   return ans
```

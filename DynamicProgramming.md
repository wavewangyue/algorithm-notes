# Dynamic Programming Questions

* 拥有最大和的连续子串：O(n) 扫描，s=max(s+a[i], a[i])
* 最长回文子串：马拉车算法，O(n)，将字符串 s 用 len(s)+1 个 # 补全为奇数长度，设 id 与 mx 为已经发现的最靠右的回文串，p[i]=p[2*id-i]，然后扫描扩张 p[i]
* 零钱兑换（不同面值硬币组成规定总额，硬币无限）：O(n*m)，dp[i]=min(dp[i-coin]+1, dp[i])
* 零钱兑换（不同面值硬币组成规定总额，硬币一次）：O(n*m)，dp[i,j]=dp[i-1, j] if c[i]>j else dp[i,j-[c[i]]] or dp[i-1,j]
* 最长上升子序列：O(n*logn)，其中 O(n) 扫描，O(logn) 二分查找，LIS = LIS+[num] if num > LIS[-1] else LIS[find(>num)] = num
* 最小M段和（N个数分M段，段和最大值最小）：O(n*m*n)，d[i,j]=min{max{dp[i,1]-dp[k,1],dp[k,j-1]}}

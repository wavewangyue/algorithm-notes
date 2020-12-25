# Other Questions

## 1. 计算质数个数

找到 n 以内的质数个数

`O(n)` 埃氏筛，每找到一个质数，把它的所有倍数标记

```python
flags = [True] * n
num = 1
for i in range(3, n, 2):
    if flags[i]:
        num += 1
        i_go = i * i
        while i_go < n:
            flags[i_go] = False
            i_go += i
```

## 2. 计算是否是 3 的幂

是否能被 3^19（int 范围最大的 3 的幂）整除

```python
return 1162261467 % n == 0
```

## 3. 汉明距离

异或计算后统计 1 的个数

```python
sum(bin(x ^ y))
```

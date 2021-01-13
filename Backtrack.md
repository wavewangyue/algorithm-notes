# Backtrack Questions

# 1. 八皇后

n*n 的棋盘放 n 个皇后，每个皇后所在的行、列、对角线上不能有其他皇后

```python
def n_queens(n):
    queens = [None] * n # 每个皇后的站位，queens[i]=j即第i行的皇后站在第j列
    columns = [False] * n # 列占位标记
    diagonals1 = [False] * (2 * n - 1) # 对角线占位标记，方向左下到右上，一共2n-1条
    diagonals2 = [False] * (2 * n - 1) # 对角线占位标记，方向左上到右下，一共2n-1条
    solutions = []
    backtrack(queens, n, 0, columns, diagonals1, diagonals2, solutions)
    return solutions

def backtrack(queens, n, i, columns, diagonals1, diagonals2, solutions):
    if i == n:
        solutions.append(queens.copy())
    else:
        for j in range(n):
            if columns[j]: continue
            if diagonals1[i - j + n - 1]: continue
            if diagonals2[i + j]: continue
            # 更改当前状态
            queens[i] = j
            columns[j] = True
            diagonals1[i - j + n - 1] = True
            diagonals2[i + j] = True
            # 进下一层
            backtrack(queens, n, i+1, columns, diagonals1, diagonals2, solutions)
            # 恢复原来状态
            queens[i] = None
            columns[j] = False
            diagonals1[i - j + n - 1] = False
            diagonals2[i + j] = False
```

# 2. 单词搜索

给定一个二维字符网格和一个单词，找出单词是否在网格中出现

```python
board = [
    ["b","a","e"],
    ["h","t","r"],
    ["f","l","v"]
]
word = "eat"

def findWord(self, board, word):
    height = len(board)
    width = len(board[0])
    flags = [[False for _ in range(width)] for _ in range(height)]
    for i in range(height):
        for j in range(width):
            if dfs(board, word, i, j, height, width, flags):
               return True    
    return False

def dfs(self, board, word, i, j, height, width, flags):
    if board[i][j] == word[0]:
        flags[i][j] = True # 标记占用
        res = ((len(word) == 1) or
               ((i > 0) and (not flags[i-1][j]) and dfs(board, i-1, j, word[1:])) or # 向上走
               ((i < height-1) and (not flags[i+1][j]) and dfs(board, i+1, j, word[1:])) or # 向下走
               ((j > 0) and (not flags[i][j-1]) and dfs(board, i, j-1, word[1:])) or # 向左走
               ((j < width-1) and (not flags[i][j+1]) and dfs(board, i, j+1, word[1:]))) # 向右走
        flags[i][j] = False # 恢复占用
        return res
    else:
        return False
```

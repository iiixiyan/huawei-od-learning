# 📘 Day 29：图·DFS — 从零到精通

> 🎯 **学习目标**：掌握图的深度优先搜索（DFS）核心思想，能解决连通分量计数、可达性判断、包围区域检测、图克隆等经典题型
> 📅 **华为OD备考 · 第5周 · 第1天** | 开始冲刺OD真题阶段！从此告别基础知识，正式进入OD 100分真题实战
> 💡 **核心套路一句话**：图的DFS = visited去重 + 递归/栈 + 遍历所有邻居，网格图的DFS还要加上方向数组和边界检查

---

## 📑 目录

- [一、图是什么？小白也能懂](#一图是什么小白也能懂)
- [二、图的DFS核心知识](#二图的dfs核心知识)
- [三、常见操作大全](#三常见操作大全)
- [四、核心套路详解（4板斧）](#四核心套路详解4板斧)
- [五、高频题精讲](#五高频题精讲)
- [六、性能优化与常见坑](#六性能优化与常见坑)
- [七、OD机考实战指南](#七od机考实战指南)
- [八、今日作业](#八今日作业)

---

## 一、图是什么？小白也能懂

### 🗺️ 生活中的图

想象你是一个快递员，需要在城市里送快递。城市里的每个**路口**就是一个**节点（Node）**，连接路口的**道路**就是**边（Edge）**。整张城市地图就是一个**图（Graph）**。

```
    路口A ———— 路口B
       |          |
    路口C ———— 路口D ———— 路口E
```

### 图的两种表示方式

**方式1：邻接表（最常用）**
```python
# 每个节点记录它有哪些邻居
graph = {
    'A': ['B', 'C'],      # A路口可以走到B和C
    'B': ['A', 'D'],      # B路口可以走到A和D
    'C': ['A', 'D'],      # ...
    'D': ['B', 'C', 'E'],
    'E': ['D']
}
```

**方式2：邻接矩阵**
```python
# 用二维数组表示节点之间是否有边
#     A  B  C  D  E
graph = [
    [0, 1, 1, 0, 0],  # A
    [1, 0, 0, 1, 0],  # B
    [1, 0, 0, 1, 0],  # C
    [0, 1, 1, 0, 1],  # D
    [0, 0, 0, 1, 0],  # E
]
```

### 图 vs 树

| 特点 | 树 | 图 |
|------|-----|-----|
| 有没有环 | ❌ 无环 | ✅ 可能有环 |
| 连接方式 | 父子关系（方向明确） | 任意连接 |
| 根节点 | ✅ 有根 | ❌ 没有根 |
| 访问去重 | 不需要（无环） | ✅ 必须用visited避免死循环 |

### 图的三种出题形式

在OD考试中，图有三种长相：

1. **显式邻接表**：直接给你一个 `graph[node] = [neighbor1, neighbor2, ...]` 的结构
2. **邻接矩阵**：给你一个 `n x n` 的矩阵 `isConnected[i][j] = 1` 表示i和j相连
3. **隐式网格图**：给你一个二维网格（如 `grid[i][j] = '1'`），每个格子是节点，上下左右是边

> 💡 **OD考试最爱考的是第3种（网格图）**！岛屿问题、被围绕的区域都是网格图DFS的经典题。

---

## 二、图的DFS核心知识

### 什么是DFS？

**DFS（Depth-First Search，深度优先搜索）** 就像你在迷宫里探险：**一条路走到黑，走不通了就回头**。

```
从A出发，DFS遍历顺序：
A → 走第一条路到B → 走到D → 走到E（到尽头）→ 回头到D → 走另一条到C（到尽头）
                                            → 回到B → 已经没有未走的路了
                                      → 回到A → 走另一条到C
```

DFS的遍历结果：**A, B, D, E, C**（先递归深入，再回溯）

### DFS基础模板

```python
def dfs(node, visited):
    """图DFS通用模板"""
    if node in visited:        # 已经访问过，跳过
        return
    visited.add(node)          # 标记已访问
    for neighbor in graph[node]:  # 遍历所有邻居
        dfs(neighbor, visited)     # 递归访问邻居
```

### 为什么需要visited？

小白问：「为什么树遍历不需要visited，但图需要？」
答：树没有环，从根走到叶子不会绕回。但图可能有环，比如：
```
A - B
|   |
C - D
```
从A出发走A→B→D→C→A…就陷入了死循环！visited就是我们的"记路本"，走过的路口打个勾 ✅，就不会重复走了。

### 网格图的DFS特殊模板

当图是以二维网格形式出现时（如岛屿问题），DFS长这样：

```python
def dfs(grid, i, j):
    """网格图DFS模板"""
    # 1️⃣ 边界检查：不能越界
    if i < 0 or i >= len(grid) or j < 0 or j >= len(grid[0]):
        return
    # 2️⃣ 是否已访问或不可通行
    if grid[i][j] != '1':    # 按题目条件判断
        return
    
    grid[i][j] = '0'        # 3️⃣ 标记已访问（原地修改，节省空间）
    
    # 4️⃣ 四方向递归
    dfs(grid, i+1, j)       # 下
    dfs(grid, i-1, j)       # 上
    dfs(grid, i, j+1)       # 右
    dfs(grid, i, j-1)       # 左
```

---

## 三、常见操作大全

### 🎯 图DFS的8种常用操作

| 操作 | 代码 | 说明 | 复杂度 |
|------|------|------|--------|
| DFS遍历（邻接表） | `dfs(node, visited): for nei in graph[node]: dfs(nei, visited)` | 标准图DFS | O(V+E) |
| DFS遍历（邻接矩阵） | `dfs(i): for j in range(n): if matrix[i][j]==1: dfs(j)` | 矩阵表示时的遍历 | O(V²) |
| DFS遍历（网格图） | `dfs(i,j): grid[i][j]='0'; dfs(i±1,j); dfs(i,j±1)` | 二维网格四方向 | O(m×n) |
| 连通分量计数 | `count=0; for each node: if not visited: count+=1; dfs(node)` | 数有多少个连通块 | O(V+E) |
| 可达性判断 | `dfs(start, target): return True if found` | 从start能否到target | O(V+E) |
| 环检测 | 在递归栈中标记：`in_stack[node]=True; dfs(nei); in_stack[node]=False` | 检测有向图是否有环 | O(V+E) |
| 拓扑排序（后序） | `dfs后序追加: order.append(node); 最后reverse` | 有向无环图排序 | O(V+E) |
| 带权图路径搜索 | 边存权重，DFS累计乘积/和 | 如除法求值 | O(V+E) |

### 🎯 图的三种表示对比

| 表示方式 | 适用场景 | 空间 | 查邻居 | 查是否有边 |
|----------|---------|------|--------|-----------|
| 邻接表（dict/set） | ⭐ 最常用，适合稀疏图 | O(V+E) | O(degree) | O(degree) |
| 邻接矩阵（list[list]） | 稠密图、节点少(<1000) | O(V²) | O(V) | O(1) |
| 网格数组 | 地图、迷宫类问题 | O(m×n) | O(1)方向 | 直接下标O(1) |

---

## 四、核心套路详解（4板斧）

### 板斧一：连通分量计数法 🗡️

**识别特征**：题目让你计算「有多少个连接在一起的团体/岛屿/省份/朋友圈」

**思路**：遍历所有节点，每遇到一个未被访问的节点就启动DFS，把它的整个连通区域都标记为已访问，然后计数+1。

**模板代码**：
```python
def count_components(graph, n):
    """
    计算连通分量数
    graph: 邻接表或邻接矩阵
    n: 节点数
    """
    visited = [False] * n
    count = 0
    
    def dfs(node):
        visited[node] = True
        # 🅰️ 邻接表形式
        for neighbor in graph[node]:
            if not visited[neighbor]:
                dfs(neighbor)
        # 🅱️ 邻接矩阵形式（可选）
        # for j in range(n):
        #     if graph[node][j] == 1 and not visited[j]:
        #         dfs(j)
    
    for i in range(n):
        if not visited[i]:
            count += 1
            dfs(i)
    
    return count
```

**适用范围**：省份数量、岛屿数量、朋友圈、连通域数

**手把手推演**（以省份数量 `isConnected = [[1,1,0],[1,1,0],[0,0,1]]`为例）：

```
初始化：visited = [F, F, F], count = 0

i=0: visited[0]==F → count=1, dfs(0)
  dfs(0): visited[0]=T，检查j: 
    j=0: isConnected[0][0]=1, visited[0]==T(已访问)跳过
    j=1: isConnected[0][1]=1, visited[1]==F → dfs(1)
      dfs(1): visited[1]=T，检查j:
        j=0: isConnected[1][0]=1, visited[0]==T跳过
        j=1: isConnected[1][1]=1, visited[1]==T跳过
        j=2: isConnected[1][2]=0 跳过
      dfs(1)结束
    j=2: isConnected[0][2]=0 跳过
  dfs(0)结束

i=1: visited[1]==T 跳过
i=2: visited[2]==F → count=2, dfs(2)
  dfs(2): visited[2]=T，检查j:
    j=0: isConnected[2][0]=0 跳过
    j=1: isConnected[2][1]=0 跳过
    j=2: isConnected[2][2]=1, visited[2]==T跳过
  dfs(2)结束

结果：count = 2 ✅
```

---

### 板斧二：网格图淹没法 🌊

**识别特征**：题目给一个二维网格（含'0'/'1'或'O'/'X'），要求在网格上做标记/修改

**思路**：遍历每个格子，遇到符合条件的格子就DFS「淹没」整个连通区域。用**原地修改**代替visited集合（把'1'改成'0'，或把'O'改成'#'）

**模板代码**：
```python
def grid_dfs(grid, i, j, target, marker):
    """
    网格DFS模板：把target区域标记为marker
    常用于「淹没」整个连通分量
    """
    m, n = len(grid), len(grid[0])
    
    # 1️⃣ 边界检查
    if i < 0 or i >= m or j < 0 or j >= n:
        return
    # 2️⃣ 只处理目标值
    if grid[i][j] != target:
        return
    
    grid[i][j] = marker  # 3️⃣ 标记（原地修改即visited）
    
    # 4️⃣ 四方向继续淹没
    for di, dj in [(1,0), (-1,0), (0,1), (0,-1)]:
        grid_dfs(grid, i+di, j+dj, target, marker)
```

**适用范围**：岛屿数量、被围绕的区域、图像渲染、飞地统计

**手把手推演**（岛屿数量 `grid = ["11000","11000","00100","00011"]`）：

```
grid:            遍历过程：
1 1 0 0 0        (0,0)='1' → count=1, 淹没
1 1 0 0 0          dfs(0,0): grid[0][0]='0'
0 0 1 0 0            dfs(1,0): grid[1][0]='0'
0 0 0 1 1              dfs(1,1): grid[1][1]='0'
                        dfs(0,1): grid[0][1]='0'
                        回溯...
        (2,2)='1' → count=2, 淹没
          dfs(2,2): grid[2][2]='0'
        (3,3)='1' → count=3, 淹没
          dfs(3,3): grid[3][3]='0'
            dfs(3,4): grid[3][4]='0'
        
结果：3个岛屿 ✅
```

---

### 板斧三：边界扩散法 📡

**识别特征**：题目说「被包围的」区域或「不接触边界的」区域，需要区分边界连通和内部隔离

**思路**：**从边界出发**DFS，只标记与边界连通的区域。然后遍历全图，剩下的就是要操作的内部区域。

**模板代码**：
```python
def boundary_dfs(board):
    """从边界出发DFS标记，再处理内部"""
    if not board:
        return
    m, n = len(board), len(board[0])
    
    def dfs(i, j):
        """从边界向内DFS，标记与边界连通的O"""
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != 'O':
            return
        board[i][j] = '#'  # 临时标记：与边界连通
        for di, dj in [(1,0), (-1,0), (0,1), (0,-1)]:
            dfs(i+di, j+dj)
    
    # 1️⃣ 从四条边界出发DFS
    for i in range(m):
        dfs(i, 0)         # 左边界
        dfs(i, n-1)       # 右边界
    for j in range(n):
        dfs(0, j)         # 上边界
        dfs(m-1, j)       # 下边界
    
    # 2️⃣ 遍历全图，处理结果
    for i in range(m):
        for j in range(n):
            if board[i][j] == 'O':
                board[i][j] = 'X'   # 内部O → X
            elif board[i][j] == '#':
                board[i][j] = 'O'   # 边界连通O → 还原O
```

**适用范围**：被围绕的区域、太平洋大西洋水流、边界可达性判断

> 💡 **核心思路反转**：不直接找「被包围的」，而是找「没被包围的」（与边界连通的），剩下的就是被包围的。

---

### 板斧四：带权图路径搜索 🧭

**识别特征**：图的边上带有权重/比值，需要搜索从A到B的路径并累计值

**思路**：建带权图（双向），DFS搜索路径，累乘/累加边的权重。每次搜索用visited防止走回头路。

**模板代码**：
```python
def weighted_graph_search(graph, start, end):
    """带权图中的路径搜索"""
    # graph = {node: {neighbor: weight, ...}, ...}
    
    def dfs(current, target, visited):
        if current not in graph or target not in graph:
            return -1.0
        if current == target:
            return 1.0
        
        visited.add(current)
        for neighbor, weight in graph[current].items():
            if neighbor not in visited:
                result = dfs(neighbor, target, visited)
                if result != -1.0:
                    return weight * result   # 累乘
        return -1.0
    
    return dfs(start, end, set())
```

**适用范围**：除法求值、汇率兑换、变量关系推理

---

## 五、高频题精讲

### 题1：岛屿数量（Number of Islands）⭐⭐

**题目描述**：
给你一个由 `'1'`（陆地）和 `'0'`（水）组成的二维网格，计算网格中岛屿的数量。岛屿由水平/垂直相邻的陆地组成。

```
输入: grid = [
  ['1','1','0','0','0'],
  ['1','1','0','0','0'],
  ['0','0','1','0','0'],
  ['0','0','0','1','1']
]
输出: 3
```

**解题思路**：
这就是「网格图淹没法」的经典应用。遍历每个格子，遇到 `'1'` 就计数+1，然后用DFS把整个岛屿的所有 `'1'` 都变成 `'0'`（相当于淹掉这个岛）。

**代码实现**：
```python
def numIslands(grid):
    if not grid:
        return 0
    
    m, n = len(grid), len(grid[0])
    count = 0
    
    def dfs(i, j):
        # 边界检查 + 只有'1'才处理
        if i < 0 or i >= m or j < 0 or j >= n or grid[i][j] == '0':
            return
        # 淹掉当前格子
        grid[i][j] = '0'
        # 四方向递归淹没
        dfs(i+1, j)
        dfs(i-1, j)
        dfs(i, j+1)
        dfs(i, j-1)
    
    for i in range(m):
        for j in range(n):
            if grid[i][j] == '1':
                count += 1
                dfs(i, j)   # 淹掉整个岛屿
    
    return count
```

**复杂度分析**：
- 时间复杂度：O(m×n) — 每个格子最多被访问一次
- 空间复杂度：O(m×n) — 最坏情况下递归栈深度（整个网格都是'1'）

**手把手推演**：
```
grid = [
  ['1','1','0'],
  ['1','0','1'],
  ['0','1','0']
]

遍历过程：
(i=0,j=0): '1' → count=1, dfs(0,0)
  grid[0][0]='0'
  dfs(1,0): grid[1][0]='0'
    dfs(0,0): 已淹跳过
    dfs(2,0): grid[2][0]=='0'跳过
    dfs(1,1): grid[1][1]=='0'跳过
    dfs(1,-1): 越界
  dfs(-1,0): 越界
  dfs(0,1): grid[0][1]=='1' → grid[0][1]='0'
    ... 继续淹没
  
(i=0,j=1): 已为'0'跳过
(i=0,j=2): '0'跳过
(i=1,j=1): '0'跳过
(i=1,j=2): '1' → count=2, dfs(1,2)
  grid[1][2]='0'
  dfs(2,2): '0'跳过
  dfs(0,2): '0'跳过
  ...

(i=2,j=0): '0'跳过
(i=2,j=1): '1' → count=3, dfs(2,1)
  ...

结果: count = 3 ✅
```

---

### 题2：被围绕的区域（Surrounded Regions）⭐⭐

**题目描述**：
给你一个 m×n 的矩阵，由 `'X'` 和 `'O'` 组成。将所有被 `'X'` 包围的 `'O'` 变为 `'X'`。注意：在边界上的 `'O'` 不会被包围。

```
输入: board = [
  ['X','X','X','X'],
  ['X','O','O','X'],
  ['X','X','O','X'],
  ['X','O','X','X']
]
输出: [
  ['X','X','X','X'],
  ['X','X','X','X'],
  ['X','X','X','X'],
  ['X','O','X','X']
]
```

**解题思路**：
使用「边界扩散法」。从四条边的 `'O'` 出发，把与边界连通的 `'O'` 都标记为 `'#'`。然后遍历全图，剩下的 `'O'` 就是被包围的，改成 `'X'`，`'#'` 改回 `'O'`。

**代码实现**：
```python
def solve(board):
    if not board:
        return
    
    m, n = len(board), len(board[0])
    
    def dfs(i, j):
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != 'O':
            return
        board[i][j] = '#'  # 标记为与边界连通
        for di, dj in [(1,0), (-1,0), (0,1), (0,-1)]:
            dfs(i+di, j+dj)
    
    # 从边界开始DFS
    for i in range(m):
        dfs(i, 0)      # 左边界
        dfs(i, n-1)    # 右边界
    for j in range(n):
        dfs(0, j)      # 上边界
        dfs(m-1, j)    # 下边界
    
    # 处理结果
    for i in range(m):
        for j in range(n):
            if board[i][j] == 'O':
                board[i][j] = 'X'   # 被包围的O → X
            elif board[i][j] == '#':
                board[i][j] = 'O'   # 边界连通的O → 还原
```

**复杂度分析**：
- 时间复杂度：O(m×n) — 每个格子最多访问两次
- 空间复杂度：O(m×n) — 递归栈深度

**手把手推演**：
```
初始:
X X X X
X O O X
X X O X
X O X X

第1步: 从边界出发DFS标记与边界连通的O
① 左边界: (1,0)是X跳过, (3,0)是O → dfs标记为#
   dfs(3,0): board[3][0]='#'
   下: 越界; 上: (2,0)是X跳过; 右: (3,1)是X跳过; 左: 越界
② 上边界: 全是X
③ 右边界: 全是X
④ 下边界: 只有(3,0)是O,已被标记

标记结果:
X X X X
X O O X
X X O X
X # X X

第2步: 遍历全图,剩余的O→X, #→O
(1,1): O → X
(1,2): O → X
(2,2): O → X
(3,1): # → O

最终:
X X X X
X X X X
X X X X
X O X X ✅
```

---

### 题3：克隆图（Clone Graph）⭐⭐

**题目描述**：
给你一个无向连通图的引用，返回该图的深拷贝（克隆）。每个节点有 `val` 和 `neighbors` 列表。

```
输入: adjList = [[2,4],[1,3],[2,4],[1,3]]
（节点1的邻居是2和4，节点2的邻居是1和3，以此类推）
输出: 一个全新的图，结构和原图完全相同
```

**解题思路**：
使用哈希表记录「原节点 → 克隆节点」的映射。DFS递归：先克隆当前节点，再递归克隆所有邻居。如果邻居已经被克隆过（在哈希表中），直接返回已有克隆，避免重复克隆和死循环。

**代码实现**：
```python
def cloneGraph(node):
    if not node:
        return None
    
    visited = {}  # 原节点 → 克隆节点的映射
    
    def dfs(n):
        if n in visited:
            return visited[n]  # 已克隆过，直接返回
        
        # 创建当前节点的克隆
        clone = Node(n.val)
        visited[n] = clone
        
        # 递归克隆所有邻居
        for neighbor in n.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

**复杂度分析**：
- 时间复杂度：O(V+E) — 每个节点和每条边都被访问一次
- 空间复杂度：O(V) — visited哈希表和递归栈

**手把手推演**：
```
原图:
  1 ── 2
  |    |
  4 ── 3

visited = {}  (空)

dfs(1):
  1不在visited → visited[1] = Node(1)  # 创建克隆节点1
  邻居: [2,4]
    dfs(2):
      2不在visited → visited[2] = Node(2)
      邻居: [1,3]
        dfs(1): 1在visited! → 返回 visited[1] (克隆1)
        dfs(3):
          3不在visited → visited[3] = Node(3)
          邻居: [2,4]
            dfs(2): 2在visited! → 返回来 visited[2] (克隆2)
            dfs(4):
              4不在visited → visited[4] = Node(4)
              邻居: [1,3]
                dfs(1): 已克隆 → 返回克隆1
                dfs(3): 已克隆 → 返回克隆3
              clone4.neighbors = [clone1, clone3]
            ← 返回 clone4
          clone3.neighbors = [clone2, clone4]
        ← 返回 clone3
      clone2.neighbors = [clone1, clone3]
    ← 返回 clone2
    dfs(4): 4在visited! → 返回visited[4] (克隆4)
  clone1.neighbors = [clone2, clone4]
← 返回 clone1 ✅
```

---

### 题4：省份数量（Number of Provinces）⭐⭐

**题目描述**：
有 n 个城市，给你一个 n×n 的矩阵 isConnected，`isConnected[i][j] = 1` 表示城市 i 和 j 直接相连。返回省份（直接或间接相连的城市组）的数量。

```
输入: isConnected = [[1,1,0],[1,1,0],[0,0,1]]
输出: 2
```

**解题思路**：
「连通分量计数法」的直接应用。用 visited 数组记录已访问的城市。遍历每个城市，如果未访问过，就DFS标记它所有相连的城市，同时计数+1。

**代码实现**：
```python
def findCircleNum(isConnected):
    n = len(isConnected)
    visited = [False] * n
    count = 0
    
    def dfs(i):
        visited[i] = True
        for j in range(n):
            if isConnected[i][j] == 1 and not visited[j]:
                dfs(j)
    
    for i in range(n):
        if not visited[i]:
            count += 1
            dfs(i)
    
    return count
```

**复杂度分析**：
- 时间复杂度：O(n²) — 邻接矩阵遍历
- 空间复杂度：O(n) — visited数组 + 递归栈

> 💡 **OD考试提示**：邻接矩阵的DFS和邻接表的DFS的区别在于遍历邻居的方式：
> - 邻接表：`for nei in graph[node]` — O(V+E)
> - 邻接矩阵：`for j in range(n): if matrix[i][j]==1: dfs(j)` — O(V²)
>
> 当 n ≤ 200 时，两者差距不大。OD考试中常用邻接矩阵形式。

---

### 题5：除法求值（Evaluate Division）⭐⭐⭐

**题目描述**：
已知 `Ai / Bi = values[i]`，给你一系列查询 `Cj / Dj = ?`，请计算出结果。如果无法确定，返回 -1.0。

```
输入: equations = [["a","b"],["b","c"]], values = [2.0, 3.0]
      queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
输出: [6.0, 0.5, -1.0, 1.0, -1.0]
```

**解题思路**：
建带权有向图：`a/b=2.0` → 边 a→b 权重2.0, b→a 权重 0.5。每次查询从 start 出发DFS搜索到 end 的路径，累乘路径上的权重。

**代码实现**：
```python
def calcEquation(equations, values, queries):
    # 建带权图
    graph = {}
    for (a, b), v in zip(equations, values):
        if a not in graph:
            graph[a] = {}
        if b not in graph:
            graph[b] = {}
        graph[a][b] = v       # a/b = v
        graph[b][a] = 1.0 / v # b/a = 1/v
    
    def dfs(start, end, visited):
        if start not in graph or end not in graph:
            return -1.0
        if start == end:
            return 1.0
        
        visited.add(start)
        for neighbor, val in graph[start].items():
            if neighbor not in visited:
                result = dfs(neighbor, end, visited)
                if result != -1.0:
                    return val * result  # 累乘
        return -1.0
    
    return [dfs(q[0], q[1], set()) for q in queries]
```

**复杂度分析**：
- 时间复杂度：O(Q × V) — 每个查询最多遍历整个图
- 空间复杂度：O(V+E) — 存储图

**手把手推演**（查询 `a/c`）：
```
图: a → b:2.0, b → a:0.5, b → c:3.0, c → b:1/3

dfs('a', 'c', set()):
  start='a', end='c'
  visited = {'a'}
  邻居: b → val=2.0
    dfs('b', 'c', {'a'}):
      visited = {'a','b'}
      邻居: a(跳过已访问), c → val=3.0
        dfs('c', 'c', {'a','b'}):
          start == end → 返回 1.0
        ← 返回 3.0 * 1.0 = 3.0
    ← 返回 2.0 * 3.0 = 6.0 ✅
```

---

### 题6：钥匙和房间（Keys and Rooms）⭐⭐

**题目描述**：
有 n 个房间，从 0 号房间开始。进入房间 i 后可以拿到 `rooms[i]` 中的钥匙（钥匙编号即房间号）。判断是否能进入所有房间。

```
输入: rooms = [[1],[2],[3],[]]
输出: true (0→拿钥匙1→1→拿钥匙2→2→拿钥匙3→3)

输入: rooms = [[1,3],[3,0,1],[2],[0]]
输出: false (进不了2号房间)
```

**解题思路**：
DFS模拟开锁过程。从 0 号房间开始，用 visited 记录已进入的房间。每进入一个房间，获取该房间里的所有钥匙，即可以访问的邻居。最后检查是否所有房间都进过。

**代码实现**：
```python
def canVisitAllRooms(rooms):
    n = len(rooms)
    visited = set()
    
    def dfs(room):
        if room in visited:
            return
        visited.add(room)
        for key in rooms[room]:  # 获取房间里的钥匙
            dfs(key)             # 去开新的房间
    
    dfs(0)
    return len(visited) == n
```

**复杂度分析**：
- 时间复杂度：O(n + k) — n个房间，k把钥匙总数
- 空间复杂度：O(n) — visited集合 + 递归栈

---

## 六、性能优化与常见坑

### ⚠️ 常见坑清单

#### 坑1：忘记visited导致死循环 ❌

```python
# ❌ 错误 — 没有visited，有环就死循环
def dfs(node):
    for nei in graph[node]:
        dfs(nei)  # 有环就无限递归了！

# ✅ 正确 — 用visited去重
def dfs(node, visited):
    if node in visited:
        return
    visited.add(node)
    for nei in graph[node]:
        dfs(nei, visited)
```

#### 坑2：网格DFS边界检查顺序错误 ❌

```python
# ❌ 错误 — 先检查grid[i][j]可能已经越界
def dfs(grid, i, j):
    if grid[i][j] != '1' or i < 0 or i >= m or j < 0 or j >= n:
        return  # 访问grid[i][j]时已经越界了！

# ✅ 正确 — 先检查边界
def dfs(grid, i, j):
    if i < 0 or i >= m or j < 0 or j >= n:
        return
    if grid[i][j] != '1':
        return
```

#### 坑3：递归深度超限 ❌

对于 m×n=200×200 的网格，递归最深可能 40000 层！Python默认递归深度是1000。有两个解决方案：

**方案A：设置递归限制**
```python
import sys
sys.setrecursionlimit(1000000)  # 放在代码开头
```

**方案B：用栈代替递归（最推荐）**
```python
def dfs_iterative(grid, start_i, start_j):
    stack = [(start_i, start_j)]
    while stack:
        i, j = stack.pop()
        if not (0 <= i < m and 0 <= j < n) or grid[i][j] != '1':
            continue
        grid[i][j] = '0'
        stack.append((i+1, j))
        stack.append((i-1, j))
        stack.append((i, j+1))
        stack.append((i, j-1))
```

#### 坑4：原地修改影响后续遍历 ❌

```python
# 如果DFS过程中原地修改了图结构，后续遍历可能受影响
# ✅ 正确做法：要么用visited集合，要么确保修改不会破坏后续遍历
# 岛屿问题中淹掉grid[i][j]='0'是安全的，因为不会再访问
```

### 🚀 性能优化表

| 优化前 | 优化后 | 提升 |
|--------|--------|------|
| 用visited集合 | 原地修改网格 | 省O(m×n)空间 |
| 检查4个方向逐个写 | 方向数组 `[(1,0),(-1,0),(0,1),(0,-1)]` | 代码更简洁 |
| 字符串拼接 | list收集再join | 从O(n²)降到O(n) |
| 递归DFS | 迭代栈DFS | 避免栈溢出 |
| 递归函数内判断网格条件 | 调用前先判断 | 减少函数调用次数 |

---

## 七、OD机考实战指南

### 📊 图的DFS在OD中的出题特点

| 难度等级 | 分值 | 出现频率 | 代表题型 |
|----------|------|---------|----------|
| ⭐⭐ 中等 | 100分 | 🔥🔥🔥🔥🔥 极高 | 岛屿数量、省份数量、钥匙与房间 |
| ⭐⭐⭐ 较难 | 100/200分 | 🔥🔥🔥 高 | 被围绕的区域、除法求值、克隆图 |

### ⏰ 时间分配建议

- **读题 + 确定算法**：2分钟 — 看到"连通"、"被包围"、"可达"等词 → DFS
- **写核心DFS函数**：3分钟 — 套模板
- **处理输入/输出**：2分钟 — 建图、循环调用
- **调试**：3分钟 — 检查visited、边界条件
- **总计**：10-15分钟（100分题）

### 🎯 OD 100分题高频模式

在OD 100分题中，**网格图DFS** 是出现频率最高的模式。出题形式可能包装成：
- 🌾 **农田灌溉问题**：给一块地，水从边界流入，求哪些区域会被淹
- 🗺️ **区域分割问题**：给一个地图，求分隔成几个区域
- 🏭 **工厂连接问题**：工厂之间有管道，求连通子网数
- 💻 **网络连通问题**：服务器之间的连接关系

> 核心不变：**本质就是DFS找连通分量！**

### 📝 考场小技巧

1. **先写框架再填细节**：先写 `def dfs():` + `visited` + 循环
2. **网格DFS四方向先确认**：很多同学只写上下左右三个方向
3. **注意题目给的边界**：如 m,n 范围、值类型（字符串'1'还是数字1）
4. **如果超时**：检查是不是忘记visited了！
5. **Python递归深度**：如果网格很大，用 `sys.setrecursionlimit(1000000)`

---

## 八、今日作业

### ⭐ 必做题（简单）

**题目1：图像渲染**
> [LeetCode 733](https://leetcode.cn/problems/flood-fill/)
> 给一个二维网格，从(sr,sc)开始，将所有与起点颜色相同且连通的格子都改为新颜色。
> **提示**：这就是"网格图淹没法"的变体，套用模板即可。

**题目2：相同的树**
> [LeetCode 100](https://leetcode.cn/problems/same-tree/)
> 不是图但练习DFS遍历思路
> **提示**：同时遍历两棵树，检查每个节点是否相同

### ⭐⭐ 中等题

**题目3：太平洋大西洋水流问题**
> [LeetCode 417](https://leetcode.cn/problems/pacific-atlantic-water-flow/)
> 从边界出发反向DFS（边界扩散法的进阶应用）
> **提示**：用两个visited矩阵分别记录能流入太平洋和大西洋的格子

**题目4：课程表**
> [LeetCode 207](https://leetcode.cn/problems/course-schedule/)
> 判断有向图是否有环（DFS环检测的经典应用）
> **提示**：在递归栈中标记节点，如果DFS遇到已在栈中的节点说明有环

### ⭐⭐⭐ 挑战题

**题目5：单词搜索 II**
> [LeetCode 212](https://leetcode.cn/problems/word-search-ii/)
> 在网格中找所有出现在字典中的单词
> **提示**：DFS + Trie前缀树剪枝，OD 200分题方向

---

> 💪 **Day 29 打卡成功！** 今天我们从零开始学习了图的DFS，掌握了4大核心套路。这是第5周的第1天，也是OD真题实战的第一站。从今天开始，每一道题都可能是OD原题！继续加油！🔥

> 📖 **明日预告**：Day 30 — 图·BFS（广度优先搜索），我们将学习用队列逐层遍历图，解决最短路径、腐烂的橘子等经典问题！

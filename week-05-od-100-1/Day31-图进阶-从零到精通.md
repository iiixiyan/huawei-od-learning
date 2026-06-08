# 📘 Day 31：图进阶 — 从零到精通

> 🎯 **学习目标**：掌握图进阶核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第5周 · 第31天** | [返回目录](../README.md)

---

## 📑 目录

- [一、核心知识点详解](#一核心知识点详解)
- [二、核心套路/模型](#二核心套路模型)
- [三、高频题精讲](#三高频题精讲)
- [四、面试追问与进阶](#四面试追问与进阶)
- [五、OD机考实战指南](#五od机考实战指南)
- [六、今日作业](#六今日作业)

---

## 一、核心知识点详解

- **多源BFS**: 多个起点同时入队,适合求每个位置到最近起点的距离(01-Matrix).
- **二分图判定**: DFS/BFS二染色,相邻节点不同色.
- **隐式图BFS**: 状态作为节点,合法转换作为边(Open the Lock).
- **DAG所有路径**: DFS回溯求所有从源到汇的路径.

### 操作速查表

| 操作 | 写法 | 说明 | 复杂度 |
|------|------|------|--------|
| 核心操作1 | `func(x)` | 说明 | O(n) |
| 核心操作2 | `func(x, y)` | 说明 | O(n) |
| 核心操作3 | `func(z)` | 说明 | O(1) |

### 常见API大全

```python
# 该主题常用的Python操作汇总
# 每个操作附示例和结果
```

---

## 二、核心套路/模型

### 套路1：标准模板

```python
# 核心代码模板（可背诵）
def template(args):
    # 初始化
    result = []
    # 主逻辑
    return result
```

**手把手推演**：
```
输入 → 步骤1 → 步骤2 → ... → 输出
```

### 套路2：进阶变体

对于更复杂的场景，可以基于标准模板做以下调整：
- 变体1：...
- 变体2：...

---

## 三、高频题精讲

### 1. Reorder Routes（⭐⭐）
**来源**：[L75](https://leetcode.cn/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)
**难度**：中等
**题目**：`n` 座城市，从 `0` 到 `n-1` 编号，其间共有 `n-1` 条路线。因此，要想在两座不同城市之间旅行只有唯一一条路线可供选择（路线网形成一颗树）。去年，交通运输部决定重新规划路线，以改变交通拥堵的状况。

路线用 `connections` 表示，其中 `connections[i] = [a, b]` 表示从城市 `a` 到 `b` 的一条有向路线。

今年，城市 0 将会举办一场大型比赛，很多游客都想前往城市 0 。

请你帮助重新规划路线方向，使每个城市都可以访问城市 0 。返回需要变更方向的最小路线数。

题目数据 **保证** 每个城市在重新规划路线方向后都能到达城市 0 。

**示例 1：**
```
*****
输入：n = 6, connections = [[0,1],[1,3],[2,3],[4,0],[4,5]]
输出：3
解释：更改以红色显示的路线的方向，使每个城市都可以到达城市 0 。
```
**示例 2：**
```
*****
输入：n = 5, connections = [[1,0],[1,2],[3,2],[3,4]]
输出：2
解释：更改以红色显示的路线的方向，使每个城市都可以到达城市 0 。
```
**示例 3：**
```
输入：n = 3, connections = [[1,0],[2,0]]
输出：0
```
**提示：**

- `2
**思路**：建双向图,标记边的原始方向.从0出发DFS,若遇到反向走的边则需要反转(计数+1).
**代码**：
```python
def minReorder(self, n, connections):
    graph = [[] for _ in range(n)]
    for a, b in connections:
        graph[a].append((b, 1))   # 正向
        graph[b].append((a, 0))   # 反向

    def dfs(node, parent):
        count = 0
        for nei, direction in graph[node]:
            if nei == parent:
                continue
            if direction == 1:  # 需要反转
                count += 1
            count += dfs(nei, node)
        return count

    return dfs(0, -1)
```
### 2. Nearest Exit from Entrance in Maze（⭐⭐）
**来源**：[L75](https://leetcode.cn/problems/nearest-exit-from-entrance-in-maze/)
**难度**：中等
**题目**：给你一个 `m x n` 的迷宫矩阵 `maze` （**下标从 0 开始**），矩阵中有空格子（用 `'.'` 表示）和墙（用 `'+'` 表示）。同时给你迷宫的入口 `entrance` ，用 `entrance = [entrancerow, entrancecol]` 表示你一开始所在格子的行和列。

每一步操作，你可以往 **上**，**下**，**左**或者**右** 移动一个格子。你不能进入墙所在的格子，你也不能离开迷宫。你的目标是找到离 `entrance` *最近** 的出口。**出口** 的含义是 `maze` *边界** 上的 **空格子**。`entrance` 格子 **不算** 出口。

请你返回从 `entrance` 到最近出口的最短路径的 **步数** ，如果不存在这样的路径，请你返回 `-1` 。

**示例 1：**
```
*
输入：maze = [["+","+",".","+"],[".",".",".","+"],["+","+","+","."]], entrance = [1,2]
输出：1
解释：总共有 3 个出口，分别位于 (1,0)，(0,2) 和 (2,3) 。
一开始，你在入口格子 (1,2) 处。
- 你可以往左移动 2 步到达 (1,0) 。
- 你可以往上移动 1 步到达 (0,2) 。
从入口处没法到达 (2,3) 。
所以，最近的出口是 (0,2) ，距离为 1 步。
```
**示例 2：**
```
*
输入：maze = [["+","+","+"],[".",".","."],["+","+","+"]], entrance = [1,0]
输出：2
解释：迷宫中只有 1 个出口，在 (1,2) 处。
(1,0) 不算出口，因为它是入口格子。
初始时，你在入口与格子 (1,0) 处。
- 你可以往右移动 2 步到达 (1,2) 处。
所以，最近的出口为 (1,2) ，距离为 2 步。
```
**示例 3：**
```
*
输入：maze = [[".","+"]], entrance = [0,0]
输出：-1
解释：这个迷宫中没有出口。
```
**提示：**

- `maze.length == m`

- `maze[i].length == n`

- `1 row col
**思路**：BFS从入口走迷宫,遇到在边界的'.'即为出口(入口本身不算).返回最短步数.
**代码**：
```python
def nearestExit(self, maze, entrance):
    m, n = len(maze), len(maze[0])
    q = deque([(entrance[0], entrance[1], 0)])
    visited = set([(entrance[0], entrance[1])])

    while q:
        i, j, steps = q.popleft()
        if (i == 0 or i == m-1 or j == 0 or j == n-1) and [i, j] != entrance:
            return steps
        for di, dj in [(1,0),(-1,0),(0,1),(0,-1)]:
            ni, nj = i+di, j+dj
            if 0 <= ni < m and 0 <= nj < n and maze[ni][nj] == '.' and (ni, nj) not in visited:
                visited.add((ni, nj))
                q.append((ni, nj, steps+1))
    return -1
```
### 3. All Paths From Source to Target（⭐⭐）
**来源**：[O](https://leetcode.cn/problems/all-paths-from-source-to-target/)
**难度**：中等
**题目**：给你一个有 `n` 个节点的 **有向无环图（DAG）**，请你找出从节点 `0` 到节点 `n-1` 的所有路径并输出（**不要求按特定顺序**）

`graph[i]` 是一个从节点 `i` 可以访问的所有节点的列表（即从节点 `i` 到节点 `graph[i][j]`存在一条有向边）。

**示例 1：**
```
*
输入：graph = [[1,2],[3],[3],[]]
输出：[[0,1,3],[0,2,3]]
解释：有两条路径 0 -> 1 -> 3 和 0 -> 2 -> 3
```
**示例 2：**
```
*
输入：graph = [[4,3,1],[3,2,4],[3],[4],[]]
输出：[[0,4],[0,3,4],[0,1,3,4],[0,1,2,3,4],[0,1,4]]
```
**提示：**

- `n == graph.length`

- `2
**思路**：DAG上DFS回溯.从0出发,记录当前路径,到达n-1时保存路径.
**代码**：
```python
def allPathsSourceTarget(self, graph):
    n = len(graph)
    res = []

    def dfs(node, path):
        if node == n - 1:
            res.append(path[:])
            return
        for nei in graph[node]:
            path.append(nei)
            dfs(nei, path)
            path.pop()

    dfs(0, [0])
    return res
```
### 4. 01 Matrix（⭐⭐）
**来源**：[O](https://leetcode.cn/problems/01-matrix/)
**难度**：中等
**题目**：给定一个由 `0` 和 `1` 组成的矩阵 `mat` ，请输出一个大小相同的矩阵，其中每一个格子是 `mat` 中对应位置元素到最近的 `0` 的距离。

两个相邻元素间的距离为 `1` 。

**示例 1：**
```
*
输入：mat = [[0,0,0],[0,1,0],[0,0,0]]
输出：[[0,0,0],[0,1,0],[0,0,0]]
```
**示例 2：**
```
*
输入：mat = [[0,0,0],[0,1,0],[1,1,1]]
输出：[[0,0,0],[0,1,0],[1,2,1]]
```
**提示：**

- `m == mat.length`

- `n == mat[i].length`

- `1 4`

- `1 4`

- `mat[i][j] is either 0 or 1.`

- `mat` 中至少有一个 `0 `
**思路**：多源BFS.所有0入队(距离0),BFS逐层扩散更新1的距离.每个位置第一次被访问时距离最短.
**代码**：
```python
def updateMatrix(self, mat):
    m, n = len(mat), len(mat[0])
    q = deque()
    dist = [[-1] * n for _ in range(m)]

    for i in range(m):
        for j in range(n):
            if mat[i][j] == 0:
                dist[i][j] = 0
                q.append((i, j))

    while q:
        i, j = q.popleft()
        for di, dj in [(1,0),(-1,0),(0,1),(0,-1)]:
            ni, nj = i+di, j+dj
            if 0 <= ni < m and 0 <= nj < n and dist[ni][nj] == -1:
                dist[ni][nj] = dist[i][j] + 1
                q.append((ni, nj))
    return dist
```
### 5. Is Graph Bipartite?（⭐⭐）
**来源**：[O](https://leetcode.cn/problems/is-graph-bipartite/)
**难度**：中等
**题目**：存在一个 **无向图** ，图中有 `n` 个节点。其中每个节点都有一个介于 `0` 到 `n - 1` 之间的唯一编号。给你一个二维数组 `graph` ，其中 `graph[u]` 是一个节点数组，由节点 `u` 的邻接节点组成。形式上，对于 `graph[u]` 中的每个 `v` ，都存在一条位于节点 `u` 和节点 `v` 之间的无向边。该无向图同时具有以下属性：

- 不存在自环（`graph[u]` 不包含 `u`）。

- 不存在平行边（`graph[u]` 不包含重复值）。

- 如果 `v` 在 `graph[u]` 内，那么 `u` 也应该在 `graph[v]` 内（该图是无向图）

- 这个图可能不是连通图，也就是说两个节点 `u` 和 `v` 之间可能不存在一条连通彼此的路径。

**二分图**定义：如果能将一个图的节点集合分割成两个独立的子集 `A` 和 `B` ，并使图中的每一条边的两个节点一个来自 `A` 集合，一个来自 `B` 集合，就将这个图称为**二分图** 。

如果图是二分图，返回 `true`  *；否则，返回 `false` 。

**示例 1：**
```
*
输入：graph = [[1,2,3],[0,2],[0,1,3],[0,2]]
输出：false
解释：不能将节点分割成两个独立的子集，以使每条边都连通一个子集中的一个节点与另一个子集中的一个节点。
```
**示例 2：**
```
*
输入：graph = [[1,3],[0,2],[1,3],[0,2]]
输出：true
解释：可以将节点分成两组: {0, 2} 和 {1, 3} 。
```
**提示：**

- `graph.length == n`

- `1
**思路**：二染色法.DFS对每个未染色节点染色0,邻接点染为1-当前色.若邻接点已染同色则不是二分图.
**代码**：
```python
def isBipartite(self, graph):
    n = len(graph)
    color = [-1] * n

    def dfs(node, c):
        if color[node] != -1:
            return color[node] == c
        color[node] = c
        for nei in graph[node]:
            if not dfs(nei, 1 - c):
                return False
        return True

    for i in range(n):
        if color[i] == -1:
            if not dfs(i, 0):
                return False
    return True
```
### 6. Open the Lock（⭐⭐⭐）
**来源**：[O](https://leetcode.cn/problems/open-the-lock/)
**难度**：中等
**题目**：你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'` 。每个拨轮可以自由旋转：例如把 `'9'` 变为 `'0'`，`'0'` 变为 `'9'` 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 `'0000'` ，一个代表四个拨轮的数字的字符串。

列表 `deadends` 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 `target` 代表可以解锁的数字，你需要给出解锁需要的最小旋转次数，如果无论如何不能解锁，返回 `-1` 。

**示例 1:**
```
输入：deadends = ["0201","0101","0102","1212","2002"], target = "0202"
输出：6
解释：
可能的移动序列为 "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202"。
注意 "0000" -> "0001" -> "0002" -> "0102" -> "0202" 这样的序列是不能解锁的，
因为当拨动到 "0102" 时这个锁就会被锁定。
```
**示例 2:**
```
输入: deadends = ["8888"], target = "0009"
输出：1
解释：把最后一位反向旋转一次即可 "0000" -> "0009"。
```
**示例 3:**
```
输入: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
输出：-1
解释：无法旋转到目标数字且不被锁定。
```
**提示：**

- `1 deadends[i].length == 4`

- `target.length == 4`

- `target` *不在** `deadends` 之中

- `target` 和 `deadends[i]` 仅由若干位数字组成
**思路**：隐式图BFS.每个密码状态(0000~9999)为节点,每次转动一位(±1).deadends不可访问,求从0000到target的最短步数.
**代码**：
```python
def openLock(self, deadends, target):
    dead = set(deadends)
    if "0000" in dead:
        return -1

    q = deque([("0000", 0)])
    visited = set(["0000"])

    while q:
        state, steps = q.popleft()
        if state == target:
            return steps

        for i in range(4):
            for d in [-1, 1]:
                nxt = list(state)
                nxt[i] = s

---

## 四、面试追问与进阶

### Q&A
**Q1：这个算法的核心思路是什么？**
A：...

**Q2：时间复杂度如何分析？**
A：...

**Q3：空间复杂度能否优化？**
A：...

### 性能优化
1. ❌ 错误写法：...
2. ✅ 正确写法：...

---

## 五、OD机考实战指南

| 题型 | 出现频率 | 分值 |
|------|---------|------|
| 基础题 | ⭐⭐⭐⭐ | 100分 |
| 进阶题 | ⭐⭐⭐ | 200分 |

### 考场策略
1. **优先写模板**：先写出标准框架
2. **注意边界**：空输入、单元素、极端值
3. **测试验证**：至少测试3个用例
4. **时间分配**：20分钟思考 + 20分钟编码 + 10分钟测试

---

## 六、今日作业

### 必做题（⭐⭐）
1. 题目1 — 链接
2. 题目2 — 链接

### 选做题（⭐⭐⭐）
3. 题目3 — 链接

### 挑战题
4. 题目4 — 链接

> 💪 **第5周第31天，坚持就是胜利！**

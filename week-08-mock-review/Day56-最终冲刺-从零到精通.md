# 📘 Day 56：最终冲刺 — 从零到精通

> 🎯 **学习目标**：掌握最终冲刺核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第8周 · 第56天** | [返回目录](../README.md)

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

# Day 56 — 🏁 终极冲刺
## 11大算法模板 + 高频易错 + Python速查 + 考前Checklist

---

# 目录
1. [11核心算法模板速查](#1-11核心算法模板速查)
2. [OD高频易错点](#2-od高频易错点)
3. [Python速查](#3-python速查)
4. [考前Checklist](#4-考前checklist)
5. [复杂度速查表](#5-复杂度速查表)

---

# 1. 11核心算法模板速查

## 1.1 DFS (深度优先搜索)

### 树/图的DFS
```python
def dfs(node, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor, visited)
```

### 网格DFS (岛屿问题)
```python
def dfs(grid, i, j):
    if i < 0 or i >= len(grid) or j < 0 or j >= len(grid[0]) or grid[i][j] != 1:
        return
    grid[i][j] = 0  # 标记已访问
    for di, dj in [(1,0), (-1,0), (0,1), (0,-1)]:
        dfs(grid, i + di, j + dj)
```

### 排列/组合 DFS (回溯)
```python
def permute(nums):
    res, path, used = [], [], [False] * len(nums)
    def dfs():
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
              

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

### 树/图的DFS
```python
def dfs(node, visited):
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(neighbor, visited)
```

### 网格DFS (岛屿问题)
```python
def dfs(grid, i, j):
    if i < 0 or i >= len(grid) or j < 0 or j >= len(grid[0]) or grid[i][j] != 1:
        return
    grid[i][j] = 0  # 标记已访问
    for di, dj in [(1,0), (-1,0), (0,1), (0,-1)]:
        dfs(grid, i + di, j + dj)
```

### 排列/组合 DFS (回溯)
```python
def permute(nums):
    res, path, used = [], [], [False] * len(nums)
    def dfs():
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                path.append(nums[i])
                dfs()
                path.pop()
                used[i] = False
    dfs()
    return res
```

---

## 1.2 BFS (广度优先搜索)

### 最短路径 (无权图)
```python
from collections import deque

def bfs(start, target):
    q = deque([start])
    visited = {start}
    steps = 0
    while q:
        for _ in range(len(q)):
            cur = q.popleft()
            if cur == target:
                return steps
            for neighbor in graph[cur]:
                if neighbor not in visited:
                    visited.add(neighbor)
                    q.append(neighbor)
        steps += 1
    return -1
```

### 多源BFS (如感染/腐烂问题)
```python
def multi_source_bfs(grid):
    q = deque()
    fresh = 0
    for i in range(n):
        for j in range(m):
            if grid[i][j] == 2:
                q.append((i, j, 0))
            elif grid[i][j] == 1:
                fresh += 1
    # ... BFS 同标准模板
```

---

## 1.3 二分查找

### 标准二分
```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

### 左边界/右边界
```python
def lower_bound(nums, target):  # 第一个 ≥ target 的位置
    left, right = 0, len(nums)
    while left < right:
        mid = (left + right) // 2
        if nums[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left

def upper_bound(nums, target):  # 第一个 > target 的位置
    left, right = 0, len(nums)
    while left < right:
        mid = (left + right) // 2
        if nums[mid] > target:
            right = mid
        else:
            left = mid + 1
    return left
```

### 浮点数二分
```python
def float_binary_search():
    lo, hi = 0.0, 1e9
    for _ in range(100):  # 固定迭代次数
        mid = (lo + hi) / 2
        if check(mid):
            hi = mid
        else:
            lo = mid
    return lo
```

---

## 1.4 滑动窗口

### 定长窗口
```python
def fixed_window(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    return max_sum
```

### 可变窗口 (求满足条件的最长/最短)
```python
def min_window(s, t):
    """最小覆盖子串"""
    from collections import Counter
    need = Counter(t)
    missing = len(t)
    left = 0
    min_len = float('inf')
    start = 0

    for right, ch in enumerate(s):
        if ch in need:
            need[ch] -= 1
            if need[ch] >= 0:
                missing -= 1

        while missing == 0:
            if right - left + 1 < min_len:
                min_len = right - left + 1
                start = left
            left_ch = s[left]
            if left_ch in need:
                need[left_ch] += 1
                if need[left_ch] > 0:
                    missing += 1
            left += 1

    return s[start:start + min_len] if min_len != float('inf') else ""
```

### 滑动窗口通用框架
```python
left = 0
for right in range(len(arr)):
    # 右移窗口，加入 arr[right]
    while not_ok():  # 窗口不满足条件
        # 移除 arr[left]
        left += 1
    # 更新答案 (此时窗口满足条件)
```

---

## 1.5 动态规划 (4大模式)

### 模式1: 线性DP (最长递增子序列)
```python
def length_of_LIS(nums):
    dp = [1] * len(nums)
    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```

**优化 (二分)**: `tails[k]` 表示长度为 k+1 的 LIS 的最小结尾值。

```python
def length_of_LIS_optimized(nums):
    tails = []
    for num in nums:
        i = 0
        j = len(tails)
        while i < j:
            mid = (i + j) // 2
            if tails[mid] < num:
                i = mid + 1
            else:
                j = mid
        if i == len(tails):
            tails.append(num)
        else:
            tails[i] = num
    return len(tails)
```

### 模式2: 区间DP (回文/合并)
```python
def longest_palindrome_subseq(s):
    n = len(s)
    dp = [[0] * n for _ in range(n)]
    for i in range(n - 1, -1, -1):
        dp[i][i] = 1
        for j in range(i + 1, n):
            if s[i] == s[j]:
                dp[i][j] = dp[i + 1][j - 1] + 2
            else:
                dp[i][j] = max(dp[i + 1][j], dp[i][j - 1])
    return dp[0][n - 1]
```

### 模式3: 背包DP
```python
# 0-1背包 (每个物品取一次)
def zero_one_knapsack(weights, values, W):
    dp = [0] * (W + 1)
    for w, v in zip(weights, values):
        for j in range(W, w - 1, -1):  # 从大到小
            dp[j] = max(dp[j], dp[j - w] + v)
    return dp[W]

# 完全背包 (每个物品取无限次)
def complete_knapsack(weights, values, W):
    dp = [0] * (W + 1)
    for w, v in zip(weights, values):
        for j in range(w, W + 1):  # 从小到大
            dp[j] = max(dp[j], dp[j - w] + v)
    return dp[W]

# 多重背包 (每个物品有限次)
def multiple_knapsack(weights, values, counts, W):
    dp = [0] * (W + 1)
    for w, v, c in zip(weights, values, counts):
        # 二进制优化
        k = 1
        while k <= c:
            for j in range(W, k * w - 1, -1):
                dp[j] = max(dp[j], dp[j - k * w] + k * v)
            c -= k
            k <<= 1
        if c > 0:
            for j in range(W, c * w - 1, -1):
                dp[j] = max(dp[j], dp[j - c * w] + c * v)
    return dp[W]
```

### 模式4: 状态压缩DP (TSP/子集)
```python
# 示例: 最短Hamilton路径 (TSP)
def tsp(dist, n):
    dp = [[float('inf')] * n for _ in range(1 << n)]
    dp[1][0] = 0  # 从0出发，状态为只访问了0
    for mask in range(1, 1 << n):
        for u in range(n):
            if not (mask >> u) & 1:
                continue
            for v in range(n):
                if (mask >> v) & 1:
                    continue
                new_mask = mask | (1 << v)
                dp[new_mask][v] = min(dp[new_mask][v], dp[mask][u] + dist[u][v])
    return min(dp[(1 << n) - 1][i] + dist[i][0] for i in range(1, n))
```

---

## 1.6 并查集 (Union-Find)

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.size = [1] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # 路径压缩
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        # 按秩合并
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        self.size[px] += self.size[py]
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

    def connected(self, x, y):
        return self.find(x) == self.find(y)

    def get_size(self, x):
        return self.size[self.find(x)]
```

**应用**: 连通分量、最小生成树(Kruskal)、冗余连接

---

## 1.7 前缀树 (Trie)

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False
        self.count = 0  # 经过该节点的单词数

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
            node.count += 1
        node.is_end =

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

> 💪 **第8周第56天，坚持就是胜利！**

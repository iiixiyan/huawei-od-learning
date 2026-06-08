# 📘 Day 35：第五周复习 — 从零到精通

> 🎯 **学习目标**：掌握第五周复习核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第5周 · 第35天** | [返回目录](../README.md)

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

```
Week 5: 图/回溯/Trie
├── D29 图-DFS       连通分量 | 判环 | 带权图 | 可达性
├── D30 图-BFS       拓扑排序 | 最短路径 | 双向BFS | 多源BFS
├── D31 图进阶       多源BFS | 二分图 | 隐式图 | DAG路径
├── D32 回溯基础     组合 | 排列 | 去重 | 括号生成
├── D33 回溯进阶     子集 | 组合和 | N皇后 | 网格搜索 | 分割
└── D34 Trie         前缀树 | 通配符搜索 | Trie+回溯 | 容错
```

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

（具体题目请参考原题链接）

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

> 💪 **第5周第35天，坚持就是胜利！**


## 本周知识点总览

### 第5周学习路线

| Day | 主题 | 核心内容 |
|-----|------|---------|
| Day29 | 图·DFS | 网格DFS、连通分量、岛屿问题、DFS遍历 |
| Day30 | 图·BFS | BFS遍历、拓扑排序、多源BFS、最短路径 |
| Day31 | 图进阶 | 并查集、Dijkstra、Floyd、二分图判定 |
| Day32 | 回溯基础 | 全排列、子集、组合、回溯三要素 |
| Day33 | 回溯进阶 | 去重技巧、N皇后、解数独、IP地址 |
| Day34 | Trie字典树 | 前缀树实现、单词搜索、模糊匹配 |

### 四大核心套路总结

#### 🔷 套路1：DFS/BFS模板
```python
# DFS递归
def dfs(node, visited):
    visited.add(node)
    for nei in graph[node]:
        if nei not in visited:
            dfs(nei, visited)

# BFS队列
from collections import deque
def bfs(start):
    q = deque([start])
    visited = {start}
    while q:
        node = q.popleft()
        for nei in graph[node]:
            if nei not in visited:
                visited.add(nei)
                q.append(nei)
```

#### 🔷 套路2：并查集
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0]*n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
```

#### 🔷 套路3：回溯模板
```python
def backtrack(path, choices):
    if 结束条件:
        res.append(path[:])
        return
    for c in choices:
        path.append(c)
        backtrack(path, new_choices)
        path.pop()
```

#### 🔷 套路4：Trie模板
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.is_end = True
    
    def search(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                return False
            node = node.children[c]
        return node.is_end
```

### 高频易错点TOP5

1. **DFS/BFS的visited初始化**：BFS必须在入队时标记visited（不是在出队时）
2. **回溯路径拷贝**：`res.append(path[:])`不是`res.append(path)`（引用陷阱）
3. **Trie的search/startWith**：前者需要检查is_end，后者不需要
4. **并查集的路径压缩**：忘记写`self.parent[x] = self.find(self.parent[x])`会导致性能退化
5. **去重条件**：`i > start and nums[i] == nums[i-1]`（子集去重）vs `i > 0 and nums[i] == nums[i-1] and not used[i-1]`（排列去重）

### 综合限时测验

**第1题**：给定一个网格，求连通岛屿数量（DFS/BFS均可，10分钟）
**第2题**：给一组课程依赖关系，判断能否完成（拓扑排序，10分钟）
**第3题**：给一个含重复数字的数组，返回所有不重复的全排列（回溯去重，15分钟）

### 下周预告

**第6周（Day36-42）**：二分查找、堆、位运算、数学、OD100分综合
- 二分查找的各种变体（旋转数组、寻找峰值）
- 堆的应用（前K高频、数据流中位数）
- 位运算技巧（异或、状态压缩）
- OD100分高频题实战演练

### 刷题建议
- 本周重点：图DFS/BFS（⭐⭐⭐⭐⭐）、回溯（⭐⭐⭐⭐）
- 建议复习：Day29-34的作业题各选2道重做
- 预习：Day36-37的二分查找

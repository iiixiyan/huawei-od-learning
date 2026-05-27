# 📘 Day 17：队列 + 设计 — 从零到精通

> 🎯 **学习目标**：彻底掌握队列（Queue）与双端队列（Deque）的核心思想，学会用队列解决滑动窗口、BFS层序遍历、轮询调度三类经典问题，并掌握队列在「设计题」中的灵活运用。
>
> 📌 **OD备考周次**：第3周 · 栈·队列·链表（Day 15-21）
>
> 💡 **核心套路一句话总结**：队列 = 排队办事（FIFO先进先出） + BFS层序专用 + 滑动窗口过期清理；**deque** 是 Python 队列之王！

---

## 📑 目录

1. [队列是什么？—— 用生活例子理解](#1-队列是什么-用生活例子理解)
2. [Python 中的队列操作大全](#2-python-中的队列操作大全)
3. [核心套路详解](#3-核心套路详解)
   - [套路一：滑动窗口 + 过期清理](#套路一滑动窗口--过期清理)
   - [套路二：BFS 层序遍历](#套路二bfs-层序遍历)
   - [套路三：双队列轮询调度](#套路三双队列轮询调度)
   - [套路四：候选队列设计模式](#套路四候选队列设计模式)
4. [高频题精讲](#4-高频题精讲)
5. [性能优化与常见坑](#5-性能优化与常见坑)
6. [OD机考实战指南](#6-od机考实战指南)
7. [今日作业](#7-今日作业)

---

## 1️⃣ 队列是什么？—— 用生活例子理解

### 🚶 排队买奶茶

想象你去奶茶店排队：

```
队伍顺序：  小明 → 小红 → 小刚 → 小美
             ↑                  ↑
           队头（先来的）     队尾（后来的）
```

- **新来的** → 站到队尾（入队 `enqueue`）
- **买完的** → 从队头离开（出队 `dequeue`）
- **特点**：**先进先出（FIFO, First In First Out）**

这就是队列最朴素的思想！

### 🤔 队列 VS 栈

| 特性 | 队列（Queue） | 栈（Stack） |
|------|-------------|------------|
| 原则 | FIFO（先进先出） | LIFO（后进先出） |
| 比喻 | 排队买票 | 叠盘子（后放的先拿） |
| 插入 | 队尾（`append`） | 栈顶（`push`） |
| 删除 | 队头（`popleft`） | 栈顶（`pop`） |

### 🔄 双端队列（Deque）

**Deque = Double-Ended Queue**，两头都能操作！

```
      ← 从左边出（popleft）      从右边出（pop）→
         ↓                          ↓
   左边 ← [ 数据1 | 数据2 | 数据3 ] → 右边
         ↑                          ↑
      ← 从左边进（appendleft）   从右边进（append）→
```

Python 中 `collections.deque` 就是双端队列的超级实现。

### 🧩 队列的现实应用

- 🛒 电商秒杀排队
- 🖨️ 打印机任务队列
- 🌐 网络数据包处理
- 📱 消息队列（Kafka、RabbitMQ）
- 🔄 CPU 任务调度

---

## 2️⃣ Python 中的队列操作大全

### ⭐ 推荐：`collections.deque`

```python
from collections import deque

# 创建队列
q = deque()                # 空队列
q = deque([1, 2, 3])       # 从列表创建

# 基本操作
q.append(4)                # 右边入队 → deque([1, 2, 3, 4])
q.appendleft(0)            # 左边入队 → deque([0, 1, 2, 3, 4])
x = q.popleft()            # 左边出队 → x=0, deque([1, 2, 3, 4])
x = q.pop()                # 右边出队 → x=4, deque([1, 2, 3])

# 访问
q[0]                       # 看队头（不删除）
q[-1]                      # 看队尾（不删除）

# 常用方法
len(q)                     # 队列长度
q.clear()                  # 清空队列
list(q)                    # 转为列表

# 注意：deque 支持 in 操作，但 O(n)
3 in q                     # True/False
```

### ❌ 不推荐：用 `list` 当队列

```python
q = []
q.append(1)                # 入队 OK
x = q.pop(0)               # 出队 ❌ O(n)！后面的元素要全部前移！
```

> **小白问**：为什么 `list.pop(0)` 很慢？
> **答**：想象一排人站在队伍里，第一个人走了，后面所有人都要往前挪一步！`list.pop(0)` 就是让后面所有元素索引 -1，复杂度 O(n)。而 `deque.popleft()` 内部用链表实现，挪动只需 O(1)。

### 📊 操作复杂度一览

| 操作 | `deque` | `list` |
|------|---------|--------|
| 右边入队 `append` | O(1) ✅ | O(1) ✅ |
| 左边出队 `popleft` / `pop(0)` | **O(1)** ✅ | O(n) ❌ |
| 左边入队 `appendleft` | O(1) ✅ | O(n) ❌ |
| 右边出队 `pop` | O(1) ✅ | O(1) ✅ |
| 访问中间元素 | O(n) | O(1) ✅ |

---

## 3️⃣ 核心套路详解

### 套路一：滑动窗口 + 过期清理

**识别特征** 🔍
- 题目说"过去 N 秒内""最近的 K 个""窗口大小为 K"
- 数据按时间顺序源源不断到来
- 需要维护一个**动态窗口**，旧的过期就丢掉

**模板代码** 📝

```python
from collections import deque

def sliding_window(data_stream, window_size):
    """
    滑动窗口通用模板
    data_stream: 数据流（列表或逐个输入）
    window_size: 窗口大小（时间窗口或数量窗口）
    """
    window = deque()     # 存窗口内的数据
    result = []
    
    for item in data_stream:
        window.append(item)                    # ① 新数据入队
        
        while window and 窗口已过期条件:        # ② 过期清理
            window.popleft()
        
        # ③ 计算当前窗口的结果
        result.append(计算(window))
    
    return result
```

**适用范围** 🎯
- 最近的请求次数（L933）
- 数据流中的移动平均值（L346）
- 滑动窗口最大值（L239，用单调队列）
- 时间窗口类统计题

---

### 套路二：BFS 层序遍历

**识别特征** 🔍
- 二叉树/图的"层""行""最底层""每层最大"
- "宽度优先""最短路径"（无权图）
- "按层次输出"

**模板代码** 📝

```python
from collections import deque

def bfs_level_order(root):
    """
    BFS 层序遍历通用模板
    适用：二叉树和图的最短路径、层相关题目
    """
    if not root:
        return []
    
    q = deque([root])
    result = []
    
    while q:
        level_size = len(q)          # ① 当前层的节点数
        level_vals = []              # ② 收集当前层结果
        
        for _ in range(level_size):
            node = q.popleft()       # ③ 出队一个节点
            level_vals.append(node.val)  
            
            # ④ 左右子节点入队（下一层）
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        
        result.append(处理当前层(level_vals))  # ⑤ 处理本层
    
    return result
```

**变体技巧** 💡

```python
# 变体1：找每层最大值
def largestValues(root):
    if not root: return []
    q = deque([root])
    res = []
    while q:
        level_max = float('-inf')
        for _ in range(len(q)):
            node = q.popleft()
            level_max = max(level_max, node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level_max)
    return res

# 变体2：找最底层最左边（从右向左BFS）
def findBottomLeftValue(root):
    q = deque([root])
    while q:
        node = q.popleft()
        if node.right: q.append(node.right)  # 先右后左！
        if node.left: q.append(node.left)
    return node.val  # 最后一个节点就是答案

# 变体3：从右向左看（右视图）
def rightSideView(root):
    if not root: return []
    q = deque([root])
    res = []
    while q:
        for i in range(len(q)):
            node = q.popleft()
            if i == len(q) - 1:  # 最后一个节点 = 最右边
                res.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
    return res
```

**适用范围** 🎯
- 每行最大值（L515）
- 找树左下角的值（L513）
- 二叉树的层序遍历（L102）
- 二叉树的右视图（L199）
- 二叉树的最大/最小深度

---

### 套路三：双队列轮询调度

**识别特征** 🔍
- 两个阵营/队伍互相竞争
- "按顺序轮流""淘汰对手"
- 需要知道谁先谁后

**模板代码** 📝

```python
from collections import deque

def round_robin_two_queues(elements, n):
    """
    双队列轮询调度通用模板
    elements: 输入序列，每个元素带阵营标识
    n: 输入长度
    比较两个队列队首的下标，下标小的先执行
    执行的议员进入下一轮（下标 + n）
    """
    q_a = deque()   # 阵营 A 的队列
    q_b = deque()   # 阵营 B 的队列
    
    # 初始化：按阵营入队
    for i, elem in enumerate(elements):
        if belongs_to_a(elem):
            q_a.append(i)
        else:
            q_b.append(i)
    
    # 轮询对抗
    while q_a and q_b:
        a_idx = q_a.popleft()
        b_idx = q_b.popleft()
        
        if a_idx < b_idx:       # A 先行动
            q_a.append(a_idx + n)   # A 进入下一轮
            # B 被淘汰（不入队）
        else:                    # B 先行动
            q_b.append(b_idx + n)   # B 进入下一轮
            # A 被淘汰
    
    return "A" if q_a else "B"
```

**关键理解** 🔑
- **为什么用下标比较**：下标代表原始顺序中的位置，谁下标小谁先行动
- **为什么 +n 再入队**：一轮结束后，存活者排到队尾，+n 保持相对顺序
- **为什么淘汰等于不入队**：Popleft 出来再不入队 = 永远失去投票权

**适用范围** 🎯
- Dota2 参议院（L649）
- 任何"两个群体按轮次对抗"的题目

---

### 套路四：候选队列设计模式

**识别特征** 🔍
- "设计一个数据结构" + "插入并保持某种性质"
- 插入位置需要按规则选择
- 每次插入后还需要能快速找到下一个可插入位置

**模板代码** 📝

```python
from collections import deque

class PerfectBinaryTreeInserter:
    """
    设计一个数据结构，向某种结构中插入新元素
    并维持该结构的某种性质（如完全二叉树性质）
    
    核心思路：BFS 预筛选候选位置
    """
    def __init__(self, root):
        self.root = root
        self.candidates = deque()  # 存"还可以插入子节点"的节点
        
        # BFS 初始化候选队列
        q = deque([root])
        while q:
            node = q.popleft()
            if not node.left or not node.right:
                self.candidates.append(node)  # 有空位！
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
    
    def insert(self, val):
        """插入新节点"""
        parent = self.candidates[0]  # 取第一个有空位的节点
        new_node = TreeNode(val)
        
        if not parent.left:
            parent.left = new_node        # 优先插左边
        else:
            parent.right = new_node       # 左边满了插右边
            self.candidates.popleft()     # 左右都满了，移出候选
        
        self.candidates.append(new_node)  # 新节点也有空位
        return parent.val
```

**适用范围** 🎯
- 完全二叉树插入器（L919）
- 设计循环队列（L622）
- 任何"按规则插入"的设计题

---

## 4️⃣ 高频题精讲

### 题1：最近的请求次数 ⭐ 简单

**📝 题目**：实现 `RecentCounter` 类，统计过去 3000 毫秒内的请求数。每次 `ping(t)` 添加一个请求，返回 `[t-3000, t]` 范围内的请求数。

**💡 思路**：
用队列存储所有 ping 的时间戳。每次 ping 时：
1. 将新时间入队
2. 把队头中 `< t-3000` 的过期时间全部弹出
3. 返回队列长度

```
t=1:    队列=[1]          → 1个请求
t=100:  队列=[1,100]      → 2个请求
t=3001: 队列=[1,100,3001] → 3个请求（1仍在范围内，因为 3001-3000=1）
t=3002: 队列=[100,3001,3002] → 3个请求（1 < 2 被弹出了）
```

**🧑‍💻 代码**：
```python
from collections import deque

class RecentCounter:
    def __init__(self):
        self.q = deque()
    
    def ping(self, t: int) -> int:
        self.q.append(t)                          # ① 新请求入队
        
        while self.q and self.q[0] < t - 3000:   # ② 过期清理
            self.q.popleft()
        
        return len(self.q)                        # ③ 返回窗口大小
```

**⏱ 时空复杂度**：
- 时间复杂度：O(1) 均摊（每个 ping 最多进出一次）
- 空间复杂度：O(N) N 为窗口内请求数

---

### 题2：Dota2 参议院 ⭐⭐ 中等

**📝 题目**：Dota2 两个阵营 R（天辉）和 D（夜魇）按席位顺序轮流投票。每个议员可以「禁止对方一名议员」或「宣布胜利」。双方都最优策略，谁赢？

**💡 思路**：
用两个队列分别存 R 和 D 的**下标**（位置）。比较队首下标谁更小：
- 更小的先投票 → 可以禁止对方 → 自己被保留（下标 + len(senate) 重新入队，排到下一轮队尾）
- 更大的被淘汰 → 不入队

```
例子 "RDD":
初始：R=[0], D=[1,2]

第1轮：
  R的0 vs D的1 → 0 < 1，R存活→R=[0+3=3]，D=[2]（1被淘汰）
  R的3 vs D的2 → 3 > 2，D存活→D=[2+3=5]，R=[]（3被淘汰）
  
D 获胜！
```

**🧑‍💻 代码**：
```python
from collections import deque

class Solution:
    def predictPartyVictory(self, senate: str) -> str:
        n = len(senate)
        q_r = deque()
        q_d = deque()
        
        for i, ch in enumerate(senate):
            if ch == 'R':
                q_r.append(i)
            else:
                q_d.append(i)
        
        while q_r and q_d:
            r = q_r.popleft()
            d = q_d.popleft()
            
            if r < d:          # R 先投票
                q_r.append(r + n)  # R 存活到下一轮
            else:               # D 先投票
                q_d.append(d + n)  # D 存活到下一轮
        
        return "Radiant" if q_r else "Dire"
```

**⏱ 时空复杂度**：
- 时间复杂度：O(N) 每个议员最多进出队列一次
- 空间复杂度：O(N)

> **小白问**：为什么是 +n 不是 +1？
> **答**：+n 模拟「进入下一轮」！n 是总人数，加上 n 后这个下标在新一轮中仍能保持相对顺序。比如第1轮的 0 变成第2轮的 3，比第1轮的 2 变成的 5 小 → 说明第1轮 0 的位置先于第1轮 2 的位置。

---

### 题3：完全二叉树插入器 ⭐⭐ 中等

**📝 题目**：设计 `CBTInserter`，向完全二叉树中插入新节点，保持完全二叉树性质（每层从左到右填充）。返回父节点的值。

**💡 思路**：
BFS 找出所有「还有空位」的节点（缺左或缺右孩子）作为候选队列。插入时取第一个候选，插左边或右边。插满的移出，新节点加入候选。

```
    初始树：   插入3：       插入4：
       1          1            1
      /          / \          / \
     2          2   3        2   3
                            /
                           4

候选队列演变：
初始化：BFS找到[1]（缺右孩子）
插入3： 1的左已满、右插3 → 1满，移出 → 候选队列加[3]（缺左右） → 候选=[3]
插入4： 3的左插4 → 候选队列加[4] → 候选=[3, 4]
```

**🧑‍💻 代码**：
```python
class CBTInserter:
    def __init__(self, root: TreeNode):
        self.root = root
        self.candidates = deque()
        q = deque([root])
        
        while q:
            node = q.popleft()
            if not node.left or not node.right:
                self.candidates.append(node)   # 有空位的
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
    
    def insert(self, v: int) -> int:
        parent = self.candidates[0]
        new_node = TreeNode(v)
        
        if not parent.left:
            parent.left = new_node
        else:
            parent.right = new_node
            self.candidates.popleft()          # 左右都满了
        
        self.candidates.append(new_node)       # 新节点也有空位
        return parent.val
    
    def get_root(self) -> TreeNode:
        return self.root
```

**⏱ 时空复杂度**：
- 初始化：O(N) BFS 遍历所有节点
- 插入：O(1) 候选队列操作
- 空间：O(N)

---

### 题4：找树左下角的值 ⭐⭐ 中等

**📝 题目**：给定二叉树，找到最底层最左边节点的值。

**💡 思路**：
**法一（巧妙版）**：BFS 从右向左遍历！最后一个访问的节点就是最底层最左节点。
**法二（直观版）**：BFS 从左向右，记录每层第一个节点，最后一层的第一个就是答案。

```
       1
      / \
     2   3
    /   / \
   4   5   6
      /
     7

法一（从右向左BFS）：1→3→6→2→5→7→4  → 最后一个=4 ✅
法二（从左向右，每层第一个）：第1层=1，第2层=2，第3层=4 → 最后记录=4 ✅
```

**🧑‍💻 代码**：
```python
# 法一：从右向左（最简洁）
def findBottomLeftValue(self, root: TreeNode) -> int:
    q = deque([root])
    while q:
        node = q.popleft()
        if node.right: q.append(node.right)   # 先右后左！
        if node.left:  q.append(node.left)
    return node.val  # 最后一个节点

# 法二：每层第一个（更直观）
def findBottomLeftValue(self, root: TreeNode) -> int:
    q = deque([root])
    res = root.val
    while q:
        for i in range(len(q)):
            node = q.popleft()
            if i == 0:
                res = node.val  # 每层第一个
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
    return res
```

**⏱ 时空复杂度**：
- 时间复杂度：O(N) 每个节点访问一次
- 空间复杂度：O(N) 队列最宽处

**🎯 法一为什么有效？**
```
普通BFS（从左到右）：      从右向左BFS：
   1                        1
  / \                      / \
 2   3                    3   2
/ \ / \                  / \ / \
4 5 6 7                  7 6 5 4
    ↓                        ↓
最后一层最后=7            最后一个=4 ✅
```

---

### 题5：数据流中的移动平均值 ⭐ 简单

**📝 题目**：实现 `MovingAverage`，计算数据流中最近 size 个数的移动平均值。

**💡 思路**：队列 + 累加和。新数据入队、旧数据出队，维护一个 sum 避免重复求和。

**🧑‍💻 代码**：
```python
class MovingAverage:
    def __init__(self, size: int):
        self.size = size
        self.q = deque()
        self.sum = 0
    
    def next(self, val: int) -> float:
        self.q.append(val)
        self.sum += val
        
        if len(self.q) > self.size:
            self.sum -= self.q.popleft()  # 窗口溢出，移除旧数据
        
        return self.sum / len(self.q)
```

**⏱ 时空复杂度**：
- 时间复杂度：O(1) 每次操作
- 空间复杂度：O(size)

---

## 5️⃣ 性能优化与常见坑

### 🚨 常见坑

**坑1：用 `list` 当队列做 `pop(0)`**
```python
# ❌ 错误：O(n) 复杂度
q = [1, 2, 3]
first = q.pop(0)  # 后面所有元素要前移！

# ✅ 正确：O(1)
from collections import deque
q = deque([1, 2, 3])
first = q.popleft()
```

**坑2：BFS 忘记记录每层大小**
```python
# ❌ 错误：分不清层
while q:
    node = q.popleft()
    # ... 处理节点
    if node.left: q.append(node.left)

# ✅ 正确：用 len(q) 确定当前层边界
while q:
    for _ in range(len(q)):  # 关键！
        node = q.popleft()
        # ...
```

**坑3：BFS 空节点判断**
```python
# ❌ 错误：None 也入队了
q.append(node.left)   # 如果 node.left 是 None...

# ✅ 正确：只入队非空节点
if node.left: q.append(node.left)
```

**坑4：deque 中间操作慢**
```python
# ❌ 错误：deque 不适合随机访问
q = deque(range(10000))
x = q[5000]  # O(n)，要遍历到中间

# ✅ 正确：deque 只适合头尾操作
x = q[0]     # O(1)，队头
x = q[-1]    # O(1)，队尾
```

**坑5：忘记处理空树**
```python
# ❌ 错误：BFS 前空检查
def bfs(root):
    q = deque([root])  # 如果 root 是 None，deque([None]) 长度为 1！

# ✅ 正确：先判空
def bfs(root):
    if not root: return []
    q = deque([root])
```

### 📊 性能对比

| 场景 | `list` | `deque` | 选择 |
|------|--------|---------|------|
| 队列（FIFO） | ❌ `pop(0)` O(n) | ✅ `popleft()` O(1) | **deque** |
| 栈（LIFO） | ✅ `pop()` O(1) | ✅ `pop()` O(1) | 两者皆可 |
| 随机访问 | ✅ `q[i]` O(1) | ❌ O(n) | **list** |
| 双端操作 | ❌ `insert(0, x)` O(n) | ✅ `appendleft()` O(1) | **deque** |

---

## 6️⃣ OD机考实战指南

### 🎯 本主题出题特点

队列在 OD 机考中出现频率 **中等偏高**，主要在以下场景：

| 题型 | 出现频率 | 难度 |
|------|---------|------|
| 滑动窗口类（过期清理） | ⭐⭐⭐⭐⭐ | 简单~中等 |
| BFS 二叉树类 | ⭐⭐⭐⭐ | 中等 |
| 队列设计题 | ⭐⭐⭐ | 中等 |
| 轮询调度类 | ⭐⭐ | 中等~较难 |

### ⏱ 时间分配建议

```
队列/设计题（20-25分钟）

读题+理解：      2分钟  → 确认是队列场景还是设计模式
思路+选模板：    3分钟  → 选择上述四个套路之一
编码：          10分钟 → deque 操作 + 边界处理
测试+调试：      5分钟  → 重点测边界（空、满、单元素）
```

### 🏆 OD 实战经验

1. **优先用 `deque`**：面试官看到你用 `list.pop(0)` 大概率扣分
2. **BFS 模板要背熟**：层序遍历是 OD 二叉树题的绝对核心
3. **设计题关键**：不要纠结于"完美设计"，先把功能跑通再优化
4. **滑动窗口的边界**：注意 `>` 还是 `>=`，窗口大小是 N 还是 N-1

### 🔥 OD 最可能考的队列题

根据历年 OD 考情，以下题型出现概率最高：
1. **滑动窗口统计类**（类似最近请求次数）
2. **BFS 二叉树层操作**（每层最大/最小/平均值）
3. **双端队列操作题**（回文检查、窗口最值）
4. **按规则模拟题**（类似 Dota2 参议院、任务调度）

---

## 7️⃣ 今日作业

### ⭐ 简单

**1️⃣ 最近的请求次数**
自己手写实现 `RecentCounter`，确保用 `deque` 而不是 `list`。
> [L933](https://leetcode.cn/problems/number-of-recent-calls/)

**2️⃣ 数据流中的移动平均值**
实现 `MovingAverage` 类，用队列 + 累计和优化。
> [L346](https://leetcode.cn/problems/moving-average-from-data-stream/)

### ⭐⭐ 中等

**3️⃣ Dota2 参议院**
理解双队列轮询的机制，自己推导 "RDR" 的输出是什么。
> [L649](https://leetcode.cn/problems/dota2-senate/)

**4️⃣ 找树左下角的值**
分别用「从右向左」和「每层第一个」两种方法实现，理解为什么从右向左更简洁。
> [L513](https://leetcode.cn/problems/find-bottom-left-tree-value/)

### ⭐⭐⭐ 进阶

**5️⃣ 完全二叉树插入器**
不抄代码，重新手写一遍 `CBTInserter`，理解候选队列的维护过程。
> [L919](https://leetcode.cn/problems/complete-binary-tree-inserter/)

---

> 💪 **每日一提醒**：队列的核心就两个字——**排队**。无论是排队等待过期（滑动窗口）、排队按层访问（BFS）、还是排队竞争（双队列），本质都是"按顺序处理"。掌握好 `deque`，队列题就成功了一半！
>
> 📌 **面试默写**：BFS 层序遍历模板 + deque 四种操作（append/popleft/appendleft/pop）是必背的肌肉记忆！

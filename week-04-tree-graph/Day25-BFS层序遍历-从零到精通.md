# 📘 Day 25：BFS + 层序遍历 — 从零到精通

> 🎯 **学习目标**：从零掌握二叉树BFS层序遍历及其六大变体 — 标准层序、右视图、每层平均值、锯齿形遍历、最大层和、最底最左节点。  
> 📍 **OD备考**：第4周 二叉树·图 | Day 25/56  
> 💡 **核心套路一句话**：**队列 + for循环控制层 = BFS万能模板**，所有变体都在这两个要素上做文章！

---

## 📋 目录

1. [🚀 什么是BFS？用生活例子秒懂](#1-什么是bfs用生活例子秒懂)
2. [📦 准备工作：队列（Queue）入门](#2-准备工作队列queue入门)
3. [🧩 BFS层序遍历万能模板](#3-bfs层序遍历万能模板)
4. [🎯 六大核心套路详解](#4-六大核心套路详解)
   - [套路1：标准层序遍历](#套路1标准层序遍历)
   - [套路2：二叉树的右视图](#套路2二叉树的右视图)
   - [套路3：每层平均值](#套路3每层平均值)
   - [套路4：锯齿形遍历（Zigzag）](#套路4锯齿形遍历zigzag)
   - [套路5：最大层和](#套路5最大层和)
   - [套路6：最底层最左节点](#套路6最底层最左节点)
5. [📝 高频题精讲](#5-高频题精讲)
6. [⚠️ 性能优化与常见坑](#6-性能优化与常见坑)
7. [🎮 OD机考实战指南](#7-od机考实战指南)
8. [📚 今日作业](#8-今日作业)

---

## 1. 🚀 什么是BFS？用生活例子秒懂

### 🏢 想象你在爬一栋楼

```
         🏢 一栋楼
   ┌──────────────────┐
   │     第3层         │  ← 最后看这一层
   │  ┌───┬───┬───┐  │
   │  │ G │ H │ I │  │
   ├──────────────────┤
   │     第2层         │  ← 然后看这一层
   │  ┌───┬───┬───┐  │
   │  │ D │ E │ F │  │
   ├──────────────────┤
   │     第1层         │  ← 先看这一层
   │  ┌───┬───┬───┐  │
   │  │ A │ B │ C │  │
   └──────────────────┘
```

**BFS（广度优先搜索）** 就像**逐层检查一栋楼**：
- 先看第1层所有房间 → 再看第2层所有房间 → 再看第3层所有房间
- 一层看完才去下一层，绝不像「坐电梯直冲顶楼」

**DFS（深度优先搜索）** 则像**钻进一个地道**：
- 从A房间钻进地下室，一路挖到最深处，才返回来探索B房间
- 一条路走到黑，先往深了走

> 🧠 **小白问**：为什么叫「广度优先」？  
> **答**：因为优先扩展「广度」——先把当前层所有节点扫完，再往下一层走，像水面涟漪一圈一圈扩散。

### 🌊 BFS就像水面涟漪

```
        ● ← 石子投入
        ↓
   ╭────○────╮       ← 第1圈（第1层）
   │    │    │
   ○────○────○       ← 第2圈（第2层）
   │    │    │
   ○────○────○       ← 第3圈（第3层）
```

**在二叉树中**，BFS就是：
1. 从根节点出发
2. 先访问根节点（第1层）
3. 再访问根的所有子节点（第2层）
4. 再访问所有孙节点（第3层）
5. ...以此类推

---

## 2. 📦 准备工作：队列（Queue）入门

### 什么是队列？

```
排队买奶茶 🧋
┌────┬────┬────┬────┬────┐
│ 我 │ 🧑 │ 👩 │ 👨 │ 🧔 │ ← 先到先得！
└────┴────┴────┴────┴────┘
  ↑                 ↑
 入队（末尾进）     出队（前面出）
```

**队列 = 先进先出（FIFO）**，就像排队：
- 先来的人站前面，先买到奶茶
- 后来的人站后面，慢慢等

### Python中的队列

```python
from collections import deque

# 创建一个队列
q = deque()          # 空队列
q = deque([1, 2, 3]) # 初始有1,2,3

# 常用操作
q.append(4)          # 入队 → 放到末尾： [1,2,3,4]
left = q.popleft()   # 出队 → 从前面取出：left=1, 队列变成[2,3,4]
first = q[0]         # 看队头（不取出）：first=2
size = len(q)        # 队列长度：2
```

> 💡 **deque** 是 `double-ended queue`（双端队列）的缩写。它从两端操作都很快（O(1)），而普通list从头部弹出(pop(0))是O(n)的，很慢！

**为什么BFS必须用队列？**

因为BFS需要「先入先出」— 先进入队列的节点（更靠近根的）要先被处理，后进入队列的节点（更深的）要后处理。队列完美满足这个需求！

```
    root
    ┌──┐
    │ 1│ ← 先入队 → 先出队处理
    └──┘
   /    \
 ┌──┐  ┌──┐
 │ 2│  │ 3│ ← 后入队 → 后出队处理
 └──┘  └──┘
```

---

## 3. 🧩 BFS层序遍历万能模板

所有BFS层序遍历变体都源于这一个模板：

```python
from collections import deque

def levelOrder(root):
    if not root:          # 🛑 边界：空树直接返回
        return []
    
    res = []              # 📦 结果列表
    q = deque([root])     # 🏁 初始化队列，放入根节点
    
    while q:              # 🔄 只要队列不空
        level_size = len(q)   # 📏 当前层的节点数——关键！
        level = []            # 📦 当前层的节点值列表
        
        for _ in range(level_size):  # 🎯 只处理当前层的节点
            node = q.popleft()       # 取出队头节点
            level.append(node.val)   # 记录节点值
            
            # 将下一层的节点入队
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        
        res.append(level)   # 📝 将当前层结果加入最终结果
    
    return res
```

### 🔑 模板核心要点

| 要素 | 说明 | 为什么重要 |
|------|------|-----------|
| `len(q)` | 取**当前层**的节点数 | 让for循环只处理当层的节点，不会混到下一层 |
| `q.popleft()` | 从队列前面弹出节点 | 保证先进先出，按层顺序处理 |
| `for _ in range(level_size)` | 按层遍历 | 这是「层序遍历」区别于普通BFS的关键 |
| 子节点入队 | 左右子节点依次入队 | 为下一层做准备 |

### 🎨 可视化推演

```
二叉树：
        3
       / \
      9   20
         /  \
        15   7

执行过程：

第1步：q = [3]            len(q)=1
  → 弹出3, level=[3]
  → 把9和20入队
  → res=[[3]]

第2步：q = [9,20]         len(q)=2
  → 弹出9, level=[9]
  → 弹出20, level=[9,20]
  → 把15和7入队
  → res=[[3],[9,20]]

第3步：q = [15,7]         len(q)=2
  → 弹出15, level=[15]
  → 弹出7, level=[15,7]
  → 没有子节点可入队
  → res=[[3],[9,20],[15,7]]

  ✅ 最终结果：[[3],[9,20],[15,7]]
```

---

## 4. 🎯 六大核心套路详解

### 套路1：标准层序遍历

**识别特征**：题目要求「按层」输出、逐层遍历

**模板代码**：
```python
from collections import deque

def levelOrder(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

**时间复杂度**：O(n) — 每个节点访问一次  
**空间复杂度**：O(n) — 队列中最多存储一层节点

---

### 套路2：二叉树的右视图

**识别特征**：返回从右侧看到的值（每层最右边/最左边的节点）

**核心思路**：只需取每层的**最后一个节点**（或第一个节点，取决于遍历顺序）

```python
def rightSideView(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        for i in range(len(q)):
            node = q.popleft()
            if i == 0:                    # 🎯 每层第一个节点
                res.append(node.val)
            if node.right: q.append(node.right)  # 先右后左！
            if node.left:  q.append(node.left)
    return res
```

**💡 关键技巧**：
- 先入队右子节点 → 右子节点先出队 → 每层第一个访问到的就是最右边节点
- 如果要「左视图」，改成先左后右即可

**图示**：
```
       1  ← 右视图看到1          右视图结果：[1,3,4]
      / \
     2   3  ← 右视图看到3
      \
       4  ← 右视图看到4
```

---

### 套路3：每层平均值

**识别特征**：求每一层节点的平均值、总和、最大值、最小值等「聚合统计」

**核心思路**：在`for`循环内累加每层节点值，出for循环后除以该层节点数

```python
def averageOfLevels(root):
    res, q = [], deque([root])
    while q:
        total = 0
        n = len(q)          # 🎯 提前记录当前层节点数
        for _ in range(n):
            node = q.popleft()
            total += node.val
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        res.append(total / n)  # 📊 计算平均值
    return res
```

> 🧠 **小白问**：为什么要先 `n = len(q)` 而不是直接用 `len(q)`？  
> **答**：因为循环内会把节点弹出（popleft），`len(q)`会动态变化！在循环开始前固定住当前层的长度，才能保证只处理当前层的节点。

**套路扩展**：
| 需求 | 修改方式 |
|------|---------|
| 求每层最大值 | `res.append(max(level))` 或边遍历边比较 |
| 求每层最小值 | 类似上面 |
| 求每层总和 | `res.append(total)` |
| 求每层节点数 | `res.append(n)` |

---

### 套路4：锯齿形遍历（Zigzag）

**识别特征**：第一层从左到右 → 第二层从右到左 → 第三层从左到右 → 交替

**核心思路**：层序遍历 + 根据层号奇偶决定插入方向（左端还是右端）

```python
def zigzagLevelOrder(root):
    if not root:
        return []
    res, q = [], deque([root])
    layer = 0               # 🏷️ 层号，从0开始
    while q:
        level = deque()     # 用deque支持两端插入
        for _ in range(len(q)):
            node = q.popleft()
            if layer % 2 == 0:          # 偶数层 → 从左到右
                level.append(node.val)
            else:                       # 奇数层 → 从右到左
                level.appendleft(node.val)  # 头部插入！
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        res.append(list(level))
        layer += 1
    return res
```

**图示**：
```
       3          ← [3]（从左到右）
      / \
     9   20       ← [20,9]（从右到左）
        /  \
       15   7     ← [15,7]（从左到右）

结果：[[3], [20,9], [15,7]]
```

**💡 关键技巧**：
- 用`deque`代替`list`存储每层结果
- 偶数层：`append()`（尾部插入 = 从左到右）
- 奇数层：`appendleft()`（头部插入 = 从右到左）
- 最后用`list(level)`转回普通列表

---

### 套路5：最大层和

**识别特征**：找哪一层的节点和最大/最小

**核心思路**：记录每层和，跟踪最大值和对应的层号

```python
def maxLevelSum(root):
    max_sum = float('-inf')    # 初始化为负无穷
    max_level = 1              # 默认第1层
    q, level_num = deque([root]), 0
    
    while q:
        level_num += 1
        total = 0
        for _ in range(len(q)):
            node = q.popleft()
            total += node.val
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        
        if total > max_sum:         # 🎯 发现更大和 → 更新
            max_sum = total
            max_level = level_num
        # 注意：相等时不更新，因为要求「返回最小的层号」
    
    return max_level
```

**💡 关键技巧**：
- 总和初始化为 `float('-inf')`，保证第一层一定能更新
- 只有 `total > max_sum` 才更新（严格大于），相等时保留更小的层号
- 层号从1开始（题目要求）

---

### 套路6：最底层最左节点

**识别特征**：找二叉树最后一行最左边的节点值

**核心思路**（两种方法）：

**方法一（推荐）**：先右后左，最后出队的节点就是答案 🎯

```python
def findBottomLeftValue(root):
    q = deque([root])
    while q:
        node = q.popleft()
        if node.right: q.append(node.right)   # 先右后左！
        if node.left:  q.append(node.left)
    return node.val   # 🎯 最后弹出的是最底层最左节点
```

**方法二**：层序遍历取每层第一个

```python
def findBottomLeftValue(root):
    q = deque([root])
    while q:
        node = None
        for i in range(len(q)):
            node = q.popleft()
            if i == 0:        # 每层第一个节点
                result = node.val
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
    return result   # 最后一层的第一个节点
```

**为什么方法一先右后左能工作？**

```
        1
       / \
      2   3
     /   / \
    4   5   6
       /
      7

普通BFS顺序:  1 → 2 → 3 → 4 → 5 → 6 → 7
先右后左顺序: 1 → 3 → 2 → 6 → 5 → 4 → 7
                                             ↑ 最后弹出的是7（最底层最左）
```

因为先入队右子节点会让右子节点先弹出，左子节点留在后面。到最后一层时，最左边的节点在所有兄弟节点之后入队，成为最后一个弹出的节点！

---

## 5. 📝 高频题精讲

### 题目1：二叉树的层序遍历 ⭐⭐

**题目描述**：给你二叉树的根节点 `root`，返回其节点值的**层序遍历**。（即逐层地，从左到右访问所有节点）。

```
输入：root = [3,9,20,null,null,15,7]
输出：[[3],[9,20],[15,7]]
```

**解题思路**：这就是我们的万能模板直接套用。

**代码**：
```python
from collections import deque

def levelOrder(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

**时空复杂度**：O(n)时间 / O(n)空间

---

### 题目2：二叉树的右视图 ⭐⭐

**题目描述**：给定一个二叉树的根节点 `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

```
输入：[1,2,3,null,5,null,4]
输出：[1,3,4]
     1         ← 看到1
   /   \
  2     3     ← 看到3
   \     \
    5     4   ← 看到4
```

**解题思路**：层序遍历 + 取每层第一个（先右后左）。

**代码**：
```python
def rightSideView(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        for i in range(len(q)):
            node = q.popleft()
            if i == 0:                # 每层第一个 = 最右边
                res.append(node.val)
            if node.right: q.append(node.right)
            if node.left:  q.append(node.left)
    return res
```

**时空复杂度**：O(n)时间 / O(n)空间

---

### 题目3：二叉树的锯齿形层序遍历 ⭐⭐

**题目描述**：给你二叉树的根节点 `root`，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推）。

```
输入：root = [3,9,20,null,null,15,7]
输出：[[3],[20,9],[15,7]]
     3         → 从左到右：[3]
   /   \
  9    20      ← 从右到左：[20,9]
      /  \
     15   7    → 从左到右：[15,7]
```

**解题思路**：用deque存储每层，偶数层尾部插入（左→右），奇数层头部插入（右→左）。

**代码**：
```python
def zigzagLevelOrder(root):
    if not root:
        return []
    res, q = [], deque([root])
    layer = 0
    while q:
        level = deque()
        for _ in range(len(q)):
            node = q.popleft()
            if layer % 2 == 0:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(list(level))
        layer += 1
    return res
```

**时空复杂度**：O(n)时间 / O(n)空间

---

### 题目4：最大层和 ⭐⭐

**题目描述**：给你一个二叉树的根节点 `root`。设根节点位于第1层。返回总和最大的那一层的层号。如果有多层总和一样大，返回其中最小的层号。

```
输入：[1,7,0,7,-8,null,null]
输出：2
解释：
第1层：1
第2层：7+0=7 ← 最大
第3层：7+(-8)=-1
```

**解题思路**：层序遍历中记录每一层的和，用变量追踪最大值和对应层号。

**代码**：
```python
def maxLevelSum(root):
    max_sum = float('-inf')
    max_level = 1
    q, level_num = deque([root]), 0
    
    while q:
        level_num += 1
        total = 0
        for _ in range(len(q)):
            node = q.popleft()
            total += node.val
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        
        if total > max_sum:     # 注意：严格大于，相等时不更新
            max_sum = total
            max_level = level_num
    
    return max_level
```

**时空复杂度**：O(n)时间 / O(n)空间

---

### 题目5：找树左下角的值 ⭐⭐

**题目描述**：给定一个二叉树的根节点 `root`，请找出该二叉树的最底层最左边节点的值。

```
输入：[2,1,3]
输出：1
     2
   /   \
  1     3    ← 最底层是1和3，最左边是1

输入：[1,2,3,4,null,5,6,null,null,7]
输出：7
        1
       / \
      2   3
     /   / \
    4   5   6
       /
      7     ← 最底层是7，最左边也是7
```

**解题思路**：先右后左BFS，最后一个出队的节点就是答案。

**代码**：
```python
def findBottomLeftValue(root):
    q = deque([root])
    while q:
        node = q.popleft()
        if node.right: q.append(node.right)
        if node.left:  q.append(node.left)
    return node.val
```

**时空复杂度**：O(n)时间 / O(1)空间（不计队列）

---

## 6. ⚠️ 性能优化与常见坑

### 🚨 常见坑

| 坑 | 说明 | 正确做法 |
|----|------|---------|
| ❌ 忘记判空 | `levelOrder(None)` 会报错 | 开头加 `if not root: return []` |
| ❌ 循环内直接 `len(q)` | `popleft()` 会改变 `len(q)` | 提前 `n = len(q)`，用 `range(n)` |
| ❌ 数组当作队列 | `list.pop(0)` 是O(n)复杂度 | 用 `collections.deque` 的 `popleft()` |
| ❌ 混淆左和右 | 右视图先右后左，反过来就变成左视图了 | 想清楚「先入队的先出队」 |
| ❌ Zigzag方向搞反 | 是从左到右开始还是从右到左 | 通常题意「先从左到右」→ 第0层 `layer % 2 == 0` 是左→右 |
| ❌ 最大层和相等时选错 | 相等时返回更小的层号 | `total > max_sum` 才更新，相等不更新 |

### ⚡ 性能优化

1. **用 `deque` 代替 `list`**：`popleft()` 是O(1)，`pop(0)` 是O(n)
2. **层聚合统计时少创建临时列表**：求平均值可以直接累加 total 和 n，不必先创建 `level` 列表再统计
3. **递归转迭代**：树很深时递归可能栈溢出，BFS迭代写法更安全

### 🔬 边界情况

```python
# 空树
assert levelOrder(None) == []

# 单节点
assert levelOrder(TreeNode(1)) == [[1]]

# 完全二叉树
#     1
#    / \
#   2   3
#  / \ / \
# 4  5 6  7
assert levelOrder(root) == [[1],[2,3],[4,5,6,7]]

# 链状（退化树）
# 1
#  \
#   2
#    \
#     3
assert levelOrder(root) == [[1],[2],[3]]
```

---

## 7. 🎮 OD机考实战指南

### 📊 出题特点

| 特点 | 说明 |
|------|------|
| **频率** | ⭐⭐⭐⭐⭐ 层序遍历是OD机考最高频考点之一 |
| **难度** | 多数为中等题，核心模板掌握后即可应对 |
| **变体** | 通常不会直接考标准层序，而是考右视图、平均值、Zigzag等变体 |
| **结合** | 常与二叉树路径、字符串解析、矩阵BFS结合出题 |

### ⏱ 时间分配建议（OD机考）

```
总时间：150分钟（2.5小时），共3题

BFS层序遍历题（中等）：
├─ 读题理解      2分钟
├─ 构思思路      1分钟
├─ 写代码        8分钟  ← 默写模板 + 按题修改
├─ 测试          4分钟  ← 3个测试用例验证
└─ 总耗时       15分钟
```

### 🎯 OD高频考查方向

1. **层序变体** — 右视图、Zigzag、平均值（必会）
2. **BFS+矩阵** — 网格中的BFS（岛屿数量、最短路径）
3. **BFS+图** — 课程表、单词梯
4. **BFS+设计** — 序列化/反序列化

> 💡 **OD机考技巧**：BFS层序模板一定要**默写到5秒内完全无错**。机考时花30秒写完模板，剩下的时间专心改逻辑！

---

## 8. 📚 今日作业

### ⭐ 题目1：二叉树的层序遍历 II
**描述**：给定一个二叉树，返回其节点值自底向上的层序遍历。（即按从叶子层到根节点层的顺序逐层遍历）

**提示**：标准层序 + 结果反转即可

```python
def levelOrderBottom(root):
    # 🏃 你的代码
    pass
```

---

### ⭐ 题目2：N叉树的层序遍历
**描述**：给定一个 N 叉树，返回其节点值的层序遍历。（N叉树每个节点可以有多个子节点）

```python
# N叉树节点定义
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children

def levelOrder(root):
    # 🏃 你的代码
    pass
```

---

### ⭐⭐ 题目3：在每个树行中找最大值
**描述**：您需要在二叉树的每一行中找到最大的值。

```
输入: 
          1
         / \
        3   2
       / \   \  
      5   3   9

输出: [1, 3, 9]
```

**提示**：把「求平均值」改成「求最大值」即可。

---

### ⭐⭐ 题目4：二叉树的堂兄弟节点
**描述**：在二叉树中，根节点位于深度 0 处，每个深度为 k 的节点的子节点位于深度 k+1 处。如果二叉树的两个节点深度相同但父节点不同，则它们是一对堂兄弟节点。给定了具有唯一值的二叉树的根节点 root，以及树中两个不同节点的值 x 和 y。如果这两个节点是堂兄弟节点，返回 true；否则，返回 false。

**提示**：层序遍历，记录每个节点的父节点和深度。

---

### ⭐⭐⭐ 题目5：二叉树最大宽度
**描述**：给定一个二叉树，编写一个函数来获取这个树的最大宽度。树的宽度是所有层中的最大宽度。每一层的宽度被定义为两端点（该层最左和最右的非空节点）之间的长度。

```
输入: 
          1
         / \
        3   2
       /     \  
      5       9
     /         \
    6           7

输出: 8
解释: 最宽层是第4层，宽度为8（位置从0到7）
```

**提示**：给每个节点编号！根节点为0，左子节点为 `2*index`，右子节点为 `2*index+1`。每层宽度 = 最右编号 - 最左编号 + 1。

---

### 💪 参考答案

> 先自己写，再对照答案！卡住时问自己：
> 1. 这个题是哪个套路的变体？
> 2. 模板代码改哪一部分？
> 3. 边界情况处理了吗？

**答案提示**：
- 题目1：标准层序 → `res.reverse()` 或 `res[::-1]`
- 题目2：`for child in node.children:` 代替 `left/right`
- 题目3：层内遍历时用 `max_val = max(max_val, node.val)`
- 题目4：层内找x和y，同时跟踪它们的父节点
- 题目5：队列同时存(node, index)，每层宽度 = max_index - min_index + 1

---

> 🎉 **恭喜你完成Day 25的学习！** BFS层序遍历是面试/机考中的「送分题」——模板背熟，变体一眼看穿，5分钟AC不是梦！  
> 
> 🌟 **今日核心记忆**：**队列 + for len(q) = 层序遍历**  
> 
> 📌 **明天预告**：二叉树的高级遍历 — 更多树的操作和技巧，敬请期待！

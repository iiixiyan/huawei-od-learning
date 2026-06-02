# 📘 Day 22：二叉树遍历 — 从零到精通

> 🎯 **学习目标**：从零掌握二叉树的四种遍历方式（前序、中序、后序、层序），熟练写出递归与迭代两种写法，能解决「最大深度」「对称性」「叶子比较」「好节点计数」等核心题型
>
> 📅 **第4周第1天** · 二叉树·图专题 · OD机考高频区，约占 15% 的考题
>
> 💡 **核心一句话**：**二叉树遍历是树问题的基础底座** — 所有树题目本质都是在遍历的基础上加一层判断逻辑，遍历模板背熟，树题不再难！

---

## 📑 目录

1. [🌳 树是什么？从生活例子说起](#1-树是什么从生活例子说起)
2. [🧭 四大遍历方式详解](#2-四大遍历方式详解)
3. [📦 常见操作大全](#3-常见操作大全)
4. [🎯 四大核心套路](#4-四大核心套路)
5. [📝 高频题精讲](#5-高频题精讲)
6. [⚠️ 性能优化与常见坑](#6-性能优化与常见坑)
7. [🎪 OD机考实战指南](#7-od机考实战指南)
8. [✅ 今日作业](#8-今日作业)

---

## 1. 🌳 树是什么？从生活例子说起

### 1.1 从"公司组织架构"理解树

想象你在一家公司：

```
          CEO（董事长）
         /      |     \
     CTO      CFO     COO
    /   \     /   \     |
 前端  后端  会计  出纳  运营
```

这就是一棵**树**！树由**节点**和**边**组成：

- **根节点**（Root）：最顶层的 CEO
- **父节点**（Parent）：CTO 是 前端、后端 的父节点
- **子节点**（Child）：前端、后端 是 CTO 的子节点
- **叶子节点**（Leaf）：前端、后端、会计等没有下属的节点
- **子树**（Subtree）：CTO 及其下属构成一个"技术子树"

### 1.2 什么是二叉树？

**二叉树**就是每个节点最多有**两个**下属（左孩子、右孩子）的树。

```
        1
       / \
      2   3
     / \
    4   5
```

### 1.3 用 Python 定义二叉树

```python
class TreeNode:
    """二叉树的节点定义"""
    def __init__(self, val=0, left=None, right=None):
        self.val = val      # 节点的值
        self.left = left    # 左子节点（默认为空）
        self.right = right  # 右子节点（默认为空）
```

这就是全部！树节点 = 值 + 左指针 + 右指针。

### 1.4 小白问：为什么树这么重要？

**答：** 🤔 你想啊，现实世界到处都是层级结构：文件系统（文件夹套文件夹）、HTML的DOM树（标签嵌套标签）、公司组织架构、家族族谱……计算机要处理这些层级数据，就必须懂树！OD机考中，树题大约占 **15%**，属于必考题型！

---

## 2. 🧭 四大遍历方式详解

### 2.1 先搞懂"遍历"是什么意思

"遍历" = 走遍整棵树，访问每个节点一次。

想象你在公司大楼里，要给每个员工发一份通知：
- **前序**：先给领导发→再去左部门→最后去右部门（领导优先）
- **中序**：先去左部门→再给领导发→最后去右部门（从左到右）
- **后序**：先去左部门→再去右部门→最后给领导发（让领导最后签字）
- **层序**：按楼层一层一层发（逐层而下）

### 2.2 前序遍历（Preorder）：根→左→右

```
遍历顺序：1 → 2 → 4 → 5 → 3

        ①
       /  \
      ②   ⑤
     / \
    ③  ④

结果：[1, 2, 4, 5, 3]
```

**记忆口诀**：先访问自己，再左子树，最后右子树

**递归代码**：
```python
def preorder(root):
    """前序遍历：根→左→右"""
    if not root:
        return []
    left = preorder(root.left)    # 🔄 左子树
    right = preorder(root.right)  # 🔄 右子树
    return [root.val] + left + right
    # ↑ 先把自己加进去
```

**迭代代码（用栈）**：
```python
def preorder_iterative(root):
    """前序遍历（迭代版）— 用栈模拟"""
    if not root:
        return []
    stack = [root]
    result = []
    while stack:
        node = stack.pop()           # ⭐ 取出栈顶
        result.append(node.val)      # 先访问根
        if node.right:               # 右先入栈（后出）
            stack.append(node.right)
        if node.left:                # 左后入栈（先出）
            stack.append(node.left)
    return result
```

### 2.3 中序遍历（Inorder）：左→根→右

```
遍历顺序：4 → 2 → 5 → 1 → 3

        ①
       /  \
      ②   ⑤
     / \
    ③  ④

结果：[4, 2, 5, 1, 3]
```

**记忆口诀**：先去左子树磨叽，再回来处理自己，最后去右子树

**递归代码**：
```python
def inorder(root):
    """中序遍历：左→根→右"""
    if not root:
        return []
    left = inorder(root.left)       # 🔄 左子树
    right = inorder(root.right)     # 🔄 右子树
    return left + [root.val] + right
    # ↑ 自己在中间
```

**迭代代码（重要！OD常考迭代写法）**：
```python
def inorder_iterative(root):
    """中序遍历（迭代版）"""
    stack = []
    result = []
    curr = root
    while curr or stack:
        while curr:                  # 一直往左走到头
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()           # 弹出
        result.append(curr.val)      # 访问
        curr = curr.right            # 转向右子树
    return result
```

> 💡 **BST的中序遍历就是升序序列** — 这个性质在二叉搜索树题目中极为重要！

### 2.4 后序遍历（Postorder）：左→右→根

```
遍历顺序：4 → 5 → 2 → 3 → 1

        ①
       /  \
      ②   ⑤
     / \
    ③  ④

结果：[4, 5, 2, 3, 1]
```

**记忆口诀**：先去左子树，再去右子树，最后回来处理自己

**递归代码**：
```python
def postorder(root):
    """后序遍历：左→右→根"""
    if not root:
        return []
    left = postorder(root.left)      # 🔄 左子树
    right = postorder(root.right)    # 🔄 右子树
    return left + right + [root.val]
    # ↑ 自己最后
```

**迭代代码（双栈法）**：
```python
def postorder_iterative(root):
    """后序遍历（迭代版）— 前序遍历的变种"""
    if not root:
        return []
    stack = [root]
    result = []
    while stack:
        node = stack.pop()
        result.append(node.val)      # 前序是根左右，这里先根
        if node.left:                # 左先入栈
            stack.append(node.left)
        if node.right:               # 右后入栈
            stack.append(node.right)
    # 此时 result 是 根→右→左（前序的镜像）
    return result[::-1]              # 反转得到 左→右→根
```

### 2.5 层序遍历（BFS）：逐层遍历

```
层序遍历：1 → 2 → 3 → 4 → 5

        ①         ← 第1层
       /  \
      ②   ③       ← 第2层
     / \
    ④  ⑤         ← 第3层

结果：[1, 2, 3, 4, 5]
```

**队列实现（必背模板！）**：
```python
from collections import deque

def level_order(root):
    """层序遍历（BFS）— 关键：每层记录大小"""
    if not root:
        return []
    result = []
    q = deque([root])
    while q:
        level_size = len(q)          # ⭐ 当前层的节点数
        level = []
        for _ in range(level_size):  # 只取当前层的节点
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        result.append(level)         # 按层加入结果
    return result
```

---

## 3. 📦 常见操作大全

| 操作 | 递归 | 迭代 | 适用场景 |
|------|------|------|----------|
| **前序遍历** | ✅ 3行代码 | ✅ 用栈 | 创建树的镜像/复制 |
| **中序遍历** | ✅ 3行代码 | ✅ 用栈+指针 | BST 升序输出 |
| **后序遍历** | ✅ 3行代码 | ✅ 双栈/反转 | 自底向上计算（如最大深度） |
| **层序遍历** | ❌ 不适合 | ✅ 用队列 | 按层处理、最短路径 |

### 3.1 判断叶子节点

```python
def is_leaf(node):
    """判断是否为叶子节点（没有子节点）"""
    return node and not node.left and not node.right
```

### 3.2 求树的高度（最大深度）

```python
def max_depth(root):
    """后序遍历：左右子树的最大深度 + 1"""
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

### 3.3 求树的节点数

```python
def count_nodes(root):
    """前/中/后序都可：统计总节点数"""
    if not root:
        return 0
    return 1 + count_nodes(root.left) + count_nodes(root.right)
```

### 3.4 收集所有叶子节点

```python
def get_leaves(root):
    """收集所有叶子节点的值（前序遍历）"""
    leaves = []
    def dfs(node):
        if not node:
            return
        if not node.left and not node.right:
            leaves.append(node.val)
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return leaves
```

---

## 4. 🎯 四大核心套路

### 套路1：DFS模板（前/中/后序通用）⭐ 必背

**识别特征**：需要对整棵树做某种统计或比较

```python
def dfs_template(root):
    """🪄 模板：DFS递归遍历"""
    if not root:
        return    # 基本情况：空节点
    
    # ===== 前序位置（刚进入节点时）=====
    # 在这里处理：根→左→右
    # 适用于：复制树、构建镜像
    
    dfs_template(root.left)   # 递归左子树
    
    # ===== 中序位置（左右子树之间）=====
    # 在这里处理：左→根→右
    # 适用于：BST升序遍历
    
    dfs_template(root.right)  # 递归右子树
    
    # ===== 后序位置（即将离开节点时）=====
    # 在这里处理：左→右→根
    # 适用于：从下往上传递信息（最大深度、直径等）
```

**适用范围**：约 80% 的树题目

---

### 套路2：BFS层序遍历模板 ⭐ 必背

**识别特征**：需要按层处理、求最短路径、层相关变量

```python
from collections import deque

def bfs_template(root):
    """🪄 模板：层序遍历（BFS）"""
    if not root:
        return []
    
    q = deque([root])
    result = []
    
    while q:
        level_size = len(q)          # 当前层有多少节点
        level_nodes = []
        
        for _ in range(level_size):  # 一次性处理完当前层
            node = q.popleft()
            level_nodes.append(node.val)
            
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        
        result.append(level_nodes)   # 追加当前层结果
    
    return result
```

**适用范围**：层序遍历、二叉树最大宽度、锯齿形遍历等

---

### 套路3：同步遍历比较法

**识别特征**：需要比较两棵树或一棵树的两个部分

```python
def compare_template(p, q):
    """🪄 模板：同步遍历比较两棵树"""
    # 1️⃣ 都为空 → 相等
    if not p and not q:
        return True
    
    # 2️⃣ 一个为空 或 值不等 → 不等
    if not p or not q or p.val != q.val:
        return False
    
    # 3️⃣ 递归比较左右子树
    return (compare_template(p.left, q.left) and
            compare_template(p.right, q.right))
```

**变种** — 对称比较（左vs右交叉）：
```python
def symmetric_check(l, r):
    """对称检查：左的左 vs 右的右，左的右 vs 右的左"""
    if not l and not r:
        return True
    if not l or not r or l.val != r.val:
        return False
    return (symmetric_check(l.left, r.right) and
            symmetric_check(l.right, r.left))
```

**适用范围**：Same Tree、Symmetric Tree

---

### 套路4：路径传递法（带状态DFS）

**识别特征**：需要记录从根到当前节点的路径信息（最大值、最小值、路径和等）

```python
def path_dfs_template(root):
    """🪄 模板：DFS带路径状态传递"""
    
    def dfs(node, state):
        """state 是从根到当前节点的路径状态"""
        if not node:
            return
        
        # 1️⃣ 在 state 中记录当前节点（自定义更新逻辑）
        new_state = state + [node.val]  # 示例：路径列表追加当前节点
        # 也可改为：new_state = max(state, node.val) 路径最大值等
        
        # 2️⃣ 如果是叶子节点，可能在此结算
        if not node.left and not node.right:
            # 用 new_state 做最终处理（如统计、比较）
            pass
        
        # 3️⃣ 递归
        dfs(node.left, new_state)
        dfs(node.right, new_state)
    
    dfs(root, [])  # initial_state = 空列表，表示路径起点

**示例** — 好节点计数：
```python
def goodNodes(root):
    def dfs(node, max_val):
        if not node:
            return 0
        
        # 当前节点 >= 路径最大值 → 好节点
        cnt = 1 if node.val >= max_val else 0
        
        # 更新最大值，传递到子节点
        max_val = max(max_val, node.val)
        
        # 递归子节点
        cnt += dfs(node.left, max_val)
        cnt += dfs(node.right, max_val)
        
        return cnt
    
    return dfs(root, root.val)
```

**适用范围**：好节点计数、路径和、路径最大值/最小值

---

## 5. 📝 高频题精讲

### 题目1：二叉树的最大深度 ⭐

**题目描述**：给定二叉树 `root`，返回其最大深度（根到最远叶子的节点数）。

```
输入：[3,9,20,null,null,15,7]
    3
   / \
  9  20
    /  \
   15   7
输出：3
```

**解题思路**：
- 🧠 **后序遍历 + 分治**：一棵树的最大深度 = max(左子树最大深度, 右子树最大深度) + 1
- 空节点深度为0 → 从叶子向上累加

**代码**：
```python
from typing import Optional

def maxDepth(root: Optional[TreeNode]) -> int:
    if not root:
        return 0
    left_depth = maxDepth(root.left)
    right_depth = maxDepth(root.right)
    return 1 + max(left_depth, right_depth)
```

**时空复杂度**：
- ⏱ 时间：O(N)，每个节点访问一次
- 💾 空间：O(H)，H为树高（递归栈深度）

---

### 题目2：相同的树 ⭐

**题目描述**：判断两棵树是否结构和值完全相同。

```
输入：p = [1,2,3], q = [1,2,3]
输出：true

输入：p = [1,2], q = [1,null,2]
输出：false
```

**解题思路**：
- 🧠 **同步遍历 + 比较**（套路3）
- 三种终止条件：都空→True；一个空→False；值不等→False

**代码**：
```python
from typing import Optional

def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    if not p and not q:
        return True
    if not p or not q or p.val != q.val:
        return False
    return isSameTree(p.left, q.left) and isSameTree(p.right, q.right)
```

**时空复杂度**：
- ⏱ 时间：O(min(N, M))，遇到不等时提前返回
- 💾 空间：O(min(H1, H2))

---

### 题目3：翻转二叉树 ⭐

**题目描述**：翻转二叉树，交换所有节点的左右子树。

```
输入：[4,2,7,1,3,6,9]
        4
       / \
      2   7
     / \ / \
    1  3 6  9

输出：[4,7,2,9,6,3,1]
        4
       / \
      7   2
     / \ / \
    9  6 3  1
```

**解题思路**：
- 🧠 **前序遍历**：每到一个节点，交换左右子树，再递归处理子节点
- 或者后序遍历：先翻转左右子树，再交换

**代码**：
```python
def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if not root:
        return None
    
    # 前序位置：交换左右
    root.left, root.right = root.right, root.left
    
    invertTree(root.left)
    invertTree(root.right)
    
    return root
```

**时空复杂度**：
- ⏱ 时间：O(N)
- 💾 空间：O(H)

> 🔥 **冷知识**：这是 Homebrew 创始人 Max Howell 面试 Google 被拒的那道题！

---

### 题目4：对称二叉树 ⭐⭐

**题目描述**：判断一棵树是否轴对称（镜像对称）。

```
输入：[1,2,2,3,4,4,3]
        1
       / \
      2   2
     / \ / \
    3  4 4  3
输出：true

输入：[1,2,2,null,3,null,3]
        1
       / \
      2   2
       \   \
        3   3
输出：false
```

**解题思路**：
- 🧠 **套路3变种**：左树的左 vs 右树的右，左树的右 vs 右树的左
- 两棵树轴对称 ≠ 两棵树相同 → 比较方式不同

**代码**：
```python
def isSymmetric(root: Optional[TreeNode]) -> bool:
    def check(l, r):
        if not l and not r:
            return True
        if not l or not r or l.val != r.val:
            return False
        # ⭐ 交叉比较：左的左 vs 右的右，左的右 vs 右的左
        return check(l.left, r.right) and check(l.right, r.left)
    
    return check(root.left, root.right) if root else True
```

**时空复杂度**：
- ⏱ 时间：O(N)
- 💾 空间：O(H)

---

### 题目5：好节点计数 ⭐⭐

**题目描述**：好节点定义为从根到该节点路径上，没有比它更大的节点。统计好节点数量。

```
输入：[3,1,4,3,null,1,5]
        3
       / \
      1   4
     /   / \
    3   1   5
输出：4
解释：3（根）、4（路径[3,4]最大值=4）、
      5（路径[3,4,5]最大值=5）、
      右下3（路径[3,1,3]最大值=3）
      左下1（路径[3,1]最大值=3 > 1，不是好节点）
```

**解题思路**：
- 🧠 **套路4（路径传递法）**：DFS传递当前路径最大值
- 当前节点值 ≥ 路径最大值 → 好节点，更新最大值

**代码**：
```python
def goodNodes(root: TreeNode) -> int:
    def dfs(node, max_val):
        if not node:
            return 0
        
        # 判断当前节点
        cnt = 1 if node.val >= max_val else 0
        max_val = max(max_val, node.val)
        
        # 递归子节点
        cnt += dfs(node.left, max_val)
        cnt += dfs(node.right, max_val)
        
        return cnt
    
    return dfs(root, root.val)
```

**时空复杂度**：
- ⏱ 时间：O(N)
- 💾 空间：O(H)

> 🤔 **小白问**：为什么 max_val 不需要用全局变量？每次递归传递新值就行？
> **答**：因为每个路径有自己的最大值！左子树的最大值不会影响右子树。每个递归分支独立维护自己的路径状态，这是递归的精髓所在。

---

### 题目6：叶值相似树 ⭐

**题目描述**：判断两棵树的叶子序列（从左到右）是否相同。

**解题思路**：
- 🧠 **套路1（前序收集）**：DFS遍历，遇到叶子就记录
- 比较两棵树的叶子列表

**代码**：
```python
def leafSimilar(root1: Optional[TreeNode], root2: Optional[TreeNode]) -> bool:
    def dfs(node, leaves):
        if not node:
            return
        if not node.left and not node.right:  # 叶子
            leaves.append(node.val)
        dfs(node.left, leaves)
        dfs(node.right, leaves)
    
    l1, l2 = [], []
    dfs(root1, l1)
    dfs(root2, l2)
    return l1 == l2
```

**时空复杂度**：
- ⏱ 时间：O(N + M)
- 💾 空间：O(N + M)

---

## 6. ⚠️ 性能优化与常见坑

### 🚨 易错点 TOP 5

**❌ 错误1：忘记空节点判断**
```python
# ❌ 错误：root为空直接报错
def maxDepth(root):
    return 1 + max(maxDepth(root.left), maxDepth(root.right))

# ✅ 正确：先检查空节点
def maxDepth(root):
    if not root:
        return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

**❌ 错误2：层序遍历忘记记录每层大小**
```python
# ❌ 错误：没有 level_size 会混乱
while q:
    node = q.popleft()  # 所有节点混在一起！

# ✅ 正确：用 level_size 区分每一层
while q:
    level_size = len(q)
    for _ in range(level_size):
        node = q.popleft()
```

**❌ 错误3：对称比较写成相同比较**
```python
# ❌ 错误：对称树不是左右子树相同
return check(l.left, r.left) and check(l.right, r.right)  # 这是 Same Tree

# ✅ 正确：交叉比较
return check(l.left, r.right) and check(l.right, r.left)
```

**❌ 错误4：中序遍历迭代没处理好指针移动**
```python
# ❌ 错误：没有 curr = curr.right 会无限循环
while curr or stack:
    while curr:
        stack.append(curr)
    # 死循环！curr一直没有移动！

# ✅ 正确：每次弹出后要转向右子树
while curr or stack:
    while curr:
        stack.append(curr)
        curr = curr.left
    curr = stack.pop()
    result.append(curr.val)
    curr = curr.right  # ⭐ 关键！
```

**❌ 错误5：路径传递中忘记更新状态**
```python
# ❌ 错误：没有更新 max_val
def dfs(node, max_val):
    cnt = 1 if node.val >= max_val else 0
    # 忘记更新 max_val！
    dfs(node.left, max_val)  # 传入的还是旧的max_val
    dfs(node.right, max_val)

# ✅ 正确：先更新再传递
def dfs(node, max_val):
    cnt = 1 if node.val >= max_val else 0
    max_val = max(max_val, node.val)  # 更新！
    dfs(node.left, max_val)
    dfs(node.right, max_val)
```

### ⚡ 性能优化

| 场景 | 优化方案 |
|------|----------|
| 树很深（1000+层） | ⚠️ 递归可能栈溢出 → 用迭代 |
| 频繁遍历 | 🔄 用迭代栈代替递归（减少函数调用开销） |
| 大树（10万+节点） | 📊 BFS队列可能很大 → 考虑用递归DFS |
| 只需判断存在 | 🔍 找到即返回，不要遍历全树 |

---

## 7. 🎪 OD机考实战指南

### 7.1 出题特点

| 特点 | 说明 |
|------|------|
| **频率** | 每套题约 1-2 道树题 |
| **难度** | 中等为主（LeetCode 中等难度） |
| **常见题型** | 二叉树遍历、最大深度、对称、路径和、最近公共祖先 |
| **变形方向** | 树 + 回溯、树 + 动态规划、树 + 哈希表 |
| **编码量** | 通常 20-40 行 Python |

### 7.2 时间分配建议

| 阶段 | 时间 | 任务 |
|------|------|------|
| 📖 读题 | 2分钟 | 确定遍历方式 + 数据结构 |
| 🧠 想思路 | 3分钟 | 套模板：DFS还是BFS？需要什么参数？ |
| ✍️ 编码 | 5-8分钟 | 先处理空节点 → 递归/迭代主体 → 返回结果 |
| ✅ 验证 | 2分钟 | 手动跑一个简单例子 |
| 🎯 优化 | 1分钟 | 检查边界条件 |

**总时间建议：12-15分钟/题**

### 7.3 考场必背模板

```python
# ⭐ 考试前先默写这4个模板！

# 1️⃣ DFS递归
def dfs(node):
    if not node: return
    # 前序
    dfs(node.left)
    # 中序
    dfs(node.right)
    # 后序

# 2️⃣ BFS层序
from collections import deque
def bfs(root):
    if not root:
        return []
    q = deque([root])
    while q:
        for _ in range(len(q)):
            node = q.popleft()
            # 处理当前节点
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)

# 3️⃣ 中序迭代
def inorder(root):
    stack, res, curr = [], [], root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        res.append(curr.val)
        curr = curr.right
    return res

# 4️⃣ 比较两棵树
def compare(p, q):
    if not p and not q: return True
    if not p or not q or p.val != q.val: return False
    return compare(p.left, q.left) and compare(p.right, q.right)
```

### 7.4 常见OD考题方向

- 🔄 **遍历变种**：之字形遍历、右视图、左视图
- 📏 **树的性质**：直径、宽度、平衡性
- 🗺️ **路径问题**：根到叶子路径和、路径总和Ⅱ
- 🔗 **二叉树转其他结构**：链表、数组、字符串

---

## 8. ✅ 今日作业

### ⭐ 简单题

**1. 二叉树的最大深度**
- LeetCode 104
- 提示：后序遍历，一行代码搞定

**2. 相同的树**
- LeetCode 100
- 提示：同步遍历比较

### ⭐⭐ 中等题

**3. 对称二叉树**
- LeetCode 101
- 提示：交叉比较，试试写迭代法

**4. 翻转二叉树**
- LeetCode 226
- 提示：前序后序都可以，想想递归顺序的区别

**5. 叶子相似的树**
- LeetCode 872
- 提示：收集叶子列表再比较

### ⭐⭐⭐ 进阶题

**6. 二叉树的锯齿形层序遍历**
- LeetCode 103
- 提示：BFS + 奇偶层反转

**7. 二叉树的最大路径和**
- LeetCode 124
- 提示：后序遍历，每个节点贡献值 = 左贡献 + 右贡献 + 自身

---

> 💪 **第4周第1天，连续22天打卡成功！**
> 
> 二叉树是数据结构的分水岭，今天把四种遍历方式背到肌肉记忆，
> 接下来几天的「树的构建」「BST」「路径问题」都会轻松很多！
> 
> 记住：**遍历模板在手，树题全都有！** 🎉🎉🎉

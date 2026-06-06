# 📘 Day 27：树的综合 — 从零到精通

> 🎯 **学习目标**：掌握 BST 迭代器设计、递增树重构、累加树逆向思维、BST 两数和、LCA 递归分治、完全二叉树计数加速算法。
>
> 📅 **第4周 Day 27** | 二叉树·图专题
>
> 💡 **一句话总结**：树的中序遍历是「灵魂技能」—— 正着走是排序，逆着走是累加，拆开来是迭代器，重构后是递增树，配上哈希表秒解两数和！

---

## 📑 目录

- [1. 基础知识：树的 DFS 和中序遍历](#1-基础知识树的-dfs-和中序遍历)
- [2. 常见操作大全](#2-常见操作大全)
- [3. 核心套路详解（四板斧）](#3-核心套路详解四板斧)
- [4. 高频题精讲](#4-高频题精讲)
- [5. 性能优化与常见坑](#5-性能优化与常见坑)
- [6. OD 机考实战指南](#6-od-机考实战指南)
- [7. 今日作业](#7-今日作业)

---

## 1. 基础知识：树的 DFS 和中序遍历

### 🌳 树的基本结构

先来复习一下树节点长什么样：

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left      # 左孩子
        self.right = right    # 右孩子
```

### 🚶 中序遍历（Inorder Traversal）

中序遍历的顺序是：**左 → 根 → 右**

对于一棵 **二叉搜索树（BST）**，中序遍历的结果是 **升序排列**！

```
    5
   / \
  3   8
 / \   \
1   4   9

中序遍历结果：[1, 3, 4, 5, 8, 9]  ← 升序！
```

**递归写法**（背下来 📝）：
```python
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.val)
    inorder(root.right)
```

**迭代写法（栈模拟）**（面试高频 ✨）：
```python
def inorder_iterative(root):
    stack, res = [], []
    cur = root
    while cur or stack:
        while cur:           # 一路向左，入栈
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()    # 弹出最左节点
        res.append(cur.val)  # 访问
        cur = cur.right      # 转向右子树
    return res
```

### 🔄 逆中序遍历

顺序是：**右 → 根 → 左**，结果就是 **降序排列**！

```python
def reverse_inorder(root):
    if not root:
        return
    reverse_inorder(root.right)
    print(root.val)
    reverse_inorder(root.left)
```

---

## 2. 常见操作大全

| 操作 | 方法 | 复杂度 |
|------|------|--------|
| 中序遍历（递归） | 左→根→右 | O(n), O(h) |
| 中序遍历（迭代栈） | while循环 | O(n), O(h) |
| 逆中序遍历 | 右→根→左 | O(n), O(h) |
| 前序遍历 | 根→左→右 | O(n), O(h) |
| 后序遍历 | 左→右→根 | O(n), O(h) |
| 层序遍历（BFS） | 队列 | O(n), O(w) |
| BST 搜索 | 二分查找 | O(h) |
| BST 插入 | 递归/迭代 | O(h) |
| BST 删除 | 三种情况处理 | O(h) |
| 求高度 | 递归 | O(n) |
| 求节点数 | 递归/完全二叉树加速 | O(n)/O(log²n) |
| 判断完全二叉树 | BFS 判空 | O(n) |

---

## 3. 核心套路详解（四板斧）

### 🪓 套路一：栈模拟中序 —「一边拆一边用」

**识别特征** 🕵️：
- 需要按中序（升序）逐个访问 BST 的节点
- 不能一次性遍历完存数组（空间限制 O(h)）
- 典型的「迭代器」模式

**模板代码** 📝：
```python
class InorderIterator:
    """BST 中序遍历迭代器 — 每次 next() 返回下一个最小值"""
    def __init__(self, root):
        self.stack = []
        self._push_left(root)

    def _push_left(self, node):
        """将 node 及所有左后代入栈"""
        while node:
            self.stack.append(node)
            node = node.left

    def next(self):
        """返回下一个最小值"""
        node = self.stack.pop()
        self._push_left(node.right)  # 处理右子树（关键！）
        return node.val

    def has_next(self):
        return len(self.stack) > 0
```

**适用范围** 🎯：
- BST Iterator 类问题
- 需要「惰性求值」的场合
- 两个 BST 合并/比对（双迭代器）

> 🐣 **小白问**：为什么 `_push_left(node.right)` 这么重要？
>
> **答**：因为当前节点弹出后，它的右子树是「下一个待处理区域」。而右子树的最小值在右子树的最左边，所以要把右子树路径上的所有左节点入栈。这样保证栈顶永远是「当前未访问的最小值」。

---

### 🪓 套路二：中序重构 —「遍历的同时建新树」

**识别特征** 🕵️：
- 把 BST 变成只有右子树的链状结构
- 在中序遍历过程中需要修改树的指针
- 需要 dummy 头节点

**模板代码** 📝：
```python
def inorder_rebuild(root):
    """中序遍历重构 — 将BST变成只有右子树的递增链"""
    dummy = TreeNode(0)   # 哨兵节点
    cur = dummy           # cur 指向新链表的尾部

    def dfs(node):
        nonlocal cur
        if not node:
            return
        dfs(node.left)           # 先处理左子树
        node.left = None         # 🔑 断开左指针
        cur.right = node         # 接上新节点
        cur = cur.right          # 移动指针
        dfs(node.right)          # 再处理右子树

    dfs(root)
    return dummy.right   # 返回新树的根
```

**适用范围** 🎯：
- Increasing Order Search Tree（递增顺序搜索树）
- 将树展平为链表
- 任何需要在遍历中重建树结构的场景

---

### 🪓 套路三：逆中序累加 —「从大到小攒总和」

**识别特征** 🕵️：
- 每个节点的值要加上「所有比它大的节点」的值
- 需要维护一个「累积和」变量
- 从右到左遍历

**模板代码** 📝：
```python
def reverse_inorder_accumulate(root):
    """逆中序累加 — 每个节点的值加上比它大的所有值的和"""
    total = 0   # 前缀和

    def dfs(node):
        nonlocal total
        if not node:
            return
        dfs(node.right)           # 先处理大的（右子树）
        total += node.val         # 累加当前值
        node.val = total          # 更新为累加值
        dfs(node.left)            # 再处理小的（左子树）

    dfs(root)
    return root
```

**适用范围** 🎯：
- Convert BST to Greater Tree（累加树）
- 任何「后缀和」需求（如二叉树变种）
- 求「大于当前节点值的所有节点和」

> 🐣 **小白问**：为什么要从右子树开始？
>
> **答**：因为 BST 的性质是「右 > 根 > 左」。我们要找「比当前节点大的值」只需要看右子树和已经走过的节点。从右开始走，用一个 `total` 不断累加，就自然得到了「所有大于当前节点值的和」。

---

### 🪓 套路四：递归分治找 LCA —「左右各找一次」

**识别特征** 🕵️：
- 找两个节点的最近公共祖先
- 节点可以在树中任意位置
- 可以用递归「分而治之」

**模板代码** 📝：
```python
def lowestCommonAncestor(root, p, q):
    """
    找二叉树中 p 和 q 的最近公共祖先
    返回: LCA 节点
    """
    # 基本情况：空节点或找到了 p/q
    if not root or root == p or root == q:
        return root

    # 分治：在左右子树中分别找
    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)

    # 如果左右都找到了 → 当前节点就是 LCA
    if left and right:
        return root

    # 否则返回非空的那一侧
    return left or right
```

**适用范围** 🎯：
- LCA of Binary Tree（任意二叉树）
- LCA of BST（BST版本可优化，利用值的大小判断）
- 任何需要在树中「同时定位两个节点」的问题

**BST 版本的 LCA**（优化版 🚀）：
```python
def lowestCommonAncestorBST(root, p, q):
    """利用 BST 性质：如果 p 和 q 在 root 的两侧，root 就是 LCA"""
    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestorBST(root.left, p, q)
    if p.val > root.val and q.val > root.val:
        return lowestCommonAncestorBST(root.right, p, q)
    return root  # 分叉点就是 LCA
```

---

## 4. 高频题精讲

### 🏆 题1：二叉搜索树迭代器 BST Iterator ⭐⭐⭐

**题目描述**：实现一个 BST 迭代器，`next()` 返回下一个最小值，`hasNext()` 判断是否还有下一个。要求均摊 O(1) 时间、O(h) 空间。

**解题思路** 💡：
用栈模拟中序遍历的迭代版本。初始化时把根节点到最左叶子的路径全部入栈（`O(h)`）。每次 `next()` 弹出栈顶，然后处理其右子树（把右子树的左链入栈）。

```
初始栈（树为 [7,3,15,null,null,9,20]）：
    7         ← 一路向左入栈
   /
  3         栈：[7, 3]
  
第1次 next()：弹出 3，3 无右子树
  → 返回 3，栈：[7]

第2次 next()：弹出 7，处理右子树 15 → 左链入栈 [15, 9]
  → 返回 7，栈：[15, 9]
```

**代码** 💻：
```python
class BSTIterator:
    def __init__(self, root):
        self.stack = []
        self._push_left(root)

    def _push_left(self, node):
        while node:
            self.stack.append(node)
            node = node.left

    def next(self):
        node = self.stack.pop()
        self._push_left(node.right)
        return node.val

    def hasNext(self):
        return len(self.stack) > 0
```

**复杂度** ⏱️：`next()` 均摊 O(1)，`hasNext()` O(1)，空间 O(h)

---

### 🏆 题2：递增顺序搜索树 ⭐

**题目描述**：将 BST 按中序重排为只有右子节点的递增链。

**解题思路** 💡：
中序遍历时，每访问一个节点就：
1. 断开它的左指针 ← 关键！
2. 用 cur 指针把它接到新树的右边
3. 移动 cur 到新节点

```
原始树：
    5
   / \
  3   6
 /     \
2       8

处理后（只画右指针）：
1 → 2 → 3 → 5 → 6 → 8
```

**代码** 💻：
```python
def increasingBST(root):
    dummy = TreeNode(0)
    cur = dummy

    def dfs(node):
        nonlocal cur
        if not node:
            return
        dfs(node.left)
        node.left = None    # 断开左子树
        cur.right = node    # 接到新链表尾部
        cur = cur.right     # 移动 cur
        dfs(node.right)

    dfs(root)
    return dummy.right
```

**复杂度** ⏱️：O(n)，O(h)

---

### 🏆 题3：累加树 Convert BST to Greater Tree ⭐⭐

**题目描述**：把 BST 转换成每个节点的值 = 原值 + 所有比它大的节点值的总和。

**解题思路** 💡：
逆中序遍历（右→根→左），用一个 `total` 变量累计和。

```
原始：
      4
    /   \
   1     6
  / \   / \
 0   2 5   7
      \     \
       3     8

变成累加树：
        30
       /  \
      36   21
     / \   / \
    36 35 26 15
         \     \
         33     8

计算过程（逆中序）：
 8  → total=8,  node=8
 7  → total=15, node=15
 6  → total=21, node=21
 5  → total=26, node=26
 4  → total=30, node=30
 3  → total=33, node=33
 2  → total=35, node=35
 1  → total=36, node=36
 0  → total=36, node=36
```

**代码** 💻：
```python
def convertBST(root):
    total = 0

    def dfs(node):
        nonlocal total
        if not node:
            return
        dfs(node.right)
        total += node.val
        node.val = total
        dfs(node.left)

    dfs(root)
    return root
```

**复杂度** ⏱️：O(n)，O(h)

---

### 🏆 题4：两数之和 IV ⭐

**题目描述**：在 BST 中找两个节点，值和等于 k。

**解题思路 💡**：
方法一：DFS + HashSet。遍历每个节点，检查 `k - node.val` 是否在集合中。

方法二：中序遍历 + 双指针。先得到有序数组，再用双指针。

方法一的代码最简单！

```
示例：root = [5,3,6,2,4,null,7], k = 9

遍历过程：
 5 → seen={}, 需要 4？不在，加 5 → seen={5}
 3 → need=6, 不在, seen={5,3}
 6 → need=3, 在！→ return True
```

**代码** 💻：
```python
def findTarget(root, k):
    seen = set()

    def dfs(node):
        if not node:
            return False
        if k - node.val in seen:
            return True
        seen.add(node.val)
        return dfs(node.left) or dfs(node.right)

    return dfs(root)
```

**复杂度** ⏱️：O(n)，O(n)

---

### 🏆 题5：最近公共祖先 LCA ⭐⭐

**题目描述**：找二叉树中两个节点的最近公共祖先（任意二叉树，非 BST 特化版）。

**解题思路** 💡：
递归分治。从根节点出发：
1. 当前节点是 p 或 q ？直接返回
2. 去左子树找，去右子树找
3. 左右都找到了 → 当前节点就是 LCA
4. 只有一边找到了 → 返回那一边

```
          3
        /   \
       5     1
      / \   / \
     6   2 0   8
        / \
       7   4

找 p=5, q=1 的 LCA：
  root=3 → left=5, right=1 → 两边都有结果 → return 3 ✓

找 p=5, q=4 的 LCA：
  root=3 → left 从子树找
    root=5 → 找到了 p=5，返回 5
  right 没找到
  left=5, right=None → return 5 ✓
```

**代码** 💻：
```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root

    left = lowestCommonAncestor(root.left, p, q)
    right = lowestCommonAncestor(root.right, p, q)

    if left and right:
        return root       # p 和 q 分居两侧
    return left or right  # 都在同一侧
```

**复杂度** ⏱️：O(n)，O(h)

---

### 🏆 题6：完全二叉树节点数 ⭐⭐

**题目描述**：求完全二叉树的节点个数，要求快于 O(n)。

**解题思路** 💡：
完全二叉树的性质：除了最后一层，前面都是满的。利用「完美二叉树」性质加速。

```
完美二叉树：节点数 = 2^h - 1（h 是层数，根=第1层）

        1 ← 高度3，2^3-1=7个节点
       / \
      2   3
     / \ / \
    4  5 6  7
```

算法：计算左子树高度和右子树高度
- 如果 `lh == rh` → 左子树是满的 → `(2^lh - 1) + 1(根) + countNodes(右子树)`
- 如果 `lh != rh` → 右子树是满的（矮一层）→ `(2^rh - 1) + 1(根) + countNodes(左子树)`

```
示例：[1,2,3,4,5,6]
       1
      / \
     2   3
    / \  /
   4  5 6

root=1: lh(左子树高度)=2, rh(右子树高度)=2
  lh==rh → 左子树满（2^2-1=3）+ 1(根) + countNodes(右=3)
  countNodes(3): lh=1, rh=1 → 左满(1) + 1 + countNodes(右null)
  = 3+1+0=4
  总数 = 3 + 1 + 4 = 8 ❌ 不对...

让我重新算：
lh 是通过一直往左走得到的：
  root=1 → 2 → 4 → None → lh=2
rh 是通过一直往右走得到的：
  root=1 → 3 → 6 → None → rh=2

不对，这里的 height 函数只往左走，lh 和 rh 都是往左走的...

再看代码：height 函数从 node 开始一直往左走到底。
lh = height(root.left) = height(node=2) → 2→4→None → 2
rh = height(root.right) = height(node=3) → 3→6→None → 2

lh==rh: 左子树是完美二叉树！节点数 = 2^2 - 1 = 3
递归 countNodes(root.right=3)

对 node=3: lh=height(6)=1, rh=height(None)=0
lh!=rh: 右子树是完美二叉树（矮一层）
节点数 = 2^0 - 1 = 0 + 1(根) + countNodes(left=6)

对 node=6: lh=0, rh=0, lh==rh → 左满(0) + 1 + countNodes(null=0) = 1

所以: 3 + 1(根1) + (0 + 1(根3) + 1) = 6 ✓
```

**代码** 💻：
```python
def countNodes(root):
    def height(node):
        """往左走到底的高度"""
        h = 0
        while node:
            node = node.left
            h += 1
        return h

    if not root:
        return 0

    lh = height(root.left)
    rh = height(root.right)

    if lh == rh:
        # 左子树是完美二叉树
        return (1 << lh) + countNodes(root.right)
    else:
        # 右子树高度=lh-1，是完美二叉树
        return (1 << rh) + countNodes(root.left)
```

**复杂度** ⏱️：O(log²n)，优于 O(n)

> 🐣 **小白问**：为什么 `1 << lh` 是 2^lh？
>
> **答**：`<<` 是左移运算符。`1 << n` 相当于 `2^n`。比如 `1 << 3 = 8 = 2^3`。完美二叉树节点数公式是 `2^h - 1`，这里 `1 << lh` 就是 `2^lh`，所以左子树满节点数是 `(1 << lh) - 1`，加上根节点就是 `1 << lh`。代码里 `(1 << lh) + countNodes(root.right)` 相当于 `(2^lh - 1 + 1) + 右子树` = `2^lh + 右子树`。

---

## 5. 性能优化与常见坑

### 🚨 易错点 TOP 5

**❌ 坑1：BST 迭代器忘记处理右子树的左链**
```python
# 错误写法
def next(self):
    node = self.stack.pop()
    # 忘记 _push_left(node.right)
    return node.val
```
✅ 每次弹出后，必须把右子树的左链全部入栈！

**❌ 坑2：中序重构忘记断开左子树**
```python
def dfs(node):
    ...
    dfs(node.left)
    # 忘记 node.left = None
    cur.right = node
    ...
```
✅ 不 `node.left = None` 会产生环！

**❌ 坑3：LCA 递归的终止条件遗漏**
```python
# 错误：只判断 root 是否为空
if not root:
    return root
# 缺少 root == p or root == q
```
✅ 必须加上 `root == p or root == q` 的判断！

**❌ 坑4：完全二叉树计数时 height 函数的方向**
- height 是 **往左走** 到底的高度
- 左子树高度和右子树高度 **都是** 往左走的结果
- 不要误解成「右子树往右走的高度」

**❌ 坑5：累加树的 total 作用域**
- 使用 `nonlocal total` 时，确保 total 在外层函数定义
- 忘记 `nonlocal` 会导致赋值不生效

### ⚡ 性能对比

| 算法 | 时间复杂度 | 空间复杂度 | 适用场景 |
|------|-----------|-----------|---------|
| 递归中序 | O(n) | O(h) | 一次性遍历 |
| 迭代中序（栈） | O(n) | O(h) | 需要暂停/恢复 |
| Morris 中序 | O(n) | O(1) | 空间敏感场景 |
| 完全二叉树普通计数 | O(n) | O(h) | 小规模 |
| 完全二叉树优化计数 | O(log²n) | O(log n) | 大规模 |

---

## 6. OD 机考实战指南

### 🎯 出题特点

树的综合题在华为 OD 机考中属于 **中等偏上** 难度，占比约 **15-20%**。

**常见出题方式**：
1. ⭐ **BST 性质应用**（50%）— 利用中序有序、双指针找和
2. ⭐⭐ **LCA 类问题**（25%）— 最近公共祖先的变种
3. ⭐⭐ **树结构改造**（15%）— 递增树、累加树等
4. ⭐⭐⭐ **完全二叉树计数**（10%）— 利用性质加速

### ⏰ 时间分配建议

| 题目类型 | 建议用时 | 策略 |
|---------|---------|------|
| 简单 BST 操作 | 5-10min | 直接套模板 |
| 树的遍历变形 | 10-15min | 想清楚遍历顺序 |
| LCA/递归 | 10-15min | 递归分治框架 |
| 综合大题 | 15-20min | 先暴力后优化 |

### 💡 应试技巧

1. **先判断是哪种树**：BST？普通二叉树？完全二叉树？不同树有不同的性质可用
2. **中序遍历是万能钥匙**：BST 相关题目，90% 可以从「中序遍历」切入
3. **递归三部曲**：
   - 终止条件是什么？
   - 当前节点做什么？
   - 左右子树返回什么？
4. **画图！画图！画图！** 树题不画图，神仙也会错

---

## 7. 今日作业

### ⭐ 简单：二叉搜索树中的搜索

**题目**：在 BST 中查找值为 val 的节点。

**思路提示**：利用 BST 性质，val < root.val 去左子树，val > root.val 去右子树。

```python
def searchBST(root, val):
    # 你的代码
    pass
```

---

### ⭐ 简单：二叉搜索树的最小绝对差

**题目**：给你一个 BST，返回任意两不同节点值之间的最小差值。

**思路提示**：中序遍历得到有序数组，相邻差值最小。

---

### ⭐⭐ 中等：验证二叉搜索树

**题目**：判断一棵树是否为有效的 BST。

**思路提示**：中序遍历是否严格递增？或者用上下界递归。

---

### ⭐⭐ 中等：二叉搜索树中第K小的元素

**题目**：找 BST 中第 k 小元素。

**思路提示**：中序遍历的第 k 个元素。可以优化为计数器+提前终止。

---

### ⭐⭐⭐ 进阶：二叉搜索树中的众数

**题目**：找出 BST 中所有出现次数最多的值（众数可能有多个）。

**思路提示**：中序遍历利用有序性，用计数器统计连续相同值的数量。

---

> 💪 **每天进步一点点，Day 27 通关！树的综合能力已经拉满，继续加油！** 🚀

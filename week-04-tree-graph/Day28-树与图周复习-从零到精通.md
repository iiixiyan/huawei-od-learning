# 📘 Day 28：树与图周复习 — 从零到精通

> 🔥 **学习目标**：本周第7天，系统回顾二叉树与图的全部核心知识点  
> 📆 **OD备考**：第4周第7天 · 树进阶与二叉搜索树  
> 💡 **核心套路**：树的问题 = 遍历 + 处理，一切树算法都是选择一种遍历方式解决一个问题

---

## 📑 目录

1. [本周知识图谱](#本周知识图谱)
2. [四大核心遍历方式](#四大核心遍历方式)
3. [套路一：DFS递归三板斧](#套路一dfs递归三板斧)
4. [套路二：层序遍历（BFS）四步法](#套路二层序遍历bfs四步法)
5. [套路三：BST中序有序大法](#套路三bst中序有序大法)
6. [套路四：路径问题模式](#套路四路径问题模式)
7. [树的构建与序列化](#树的构建与序列化)
8. [高频题精讲](#高频题精讲)
9. [性能优化与常见坑](#性能优化与常见坑)
10. [OD机考实战指南](#od机考实战指南)
11. [本周总结](#本周总结)
12. [今日作业](#今日作业)

---

## 本周知识图谱

```
Week 4 — 树与图
┌────────────────────────────────────────────────────┐
│  D22 二叉树遍历（递归前/中/后序，DFS）              │
│  D23 树的构建（序列→树，序列化/反序列化）           │
│  D24 树的路径（路径和，前缀和，最大路径）           │
│  D25 BFS + 层序遍历（队列模板，zigzag，右视图）      │
│  D26 BST（中序有序，搜索/删除/验证/后继）           │
│  D27 树的综合（迭代器，累加树，LCA，完全二叉树）    │
│  D28 ★ 周复习（核心模板 + 综合刷题） ★             │
└────────────────────────────────────────────────────┘
```

🎯 **本周高频考点**（华为 OD 笔试 + 面试）：
1. 二叉树层序遍历及其变体 ⭐⭐⭐
2. BST 验证与中序遍历 ⭐⭐⭐
3. 路径和（Path Sum III 前缀和）⭐⭐⭐
4. LCA 最近公共祖先 ⭐⭐⭐
5. 最大路径和 ⭐⭐⭐
6. 序列化/反序列化 ⭐⭐
7. 树的构建（pre+in / in+post）⭐⭐⭐

---

## 四大核心遍历方式

### 🌲 递归遍历（前/中/后序）

```
    前序：根 → 左 → 右     [3, 9, 20, 15, 7]
    中序：左 → 根 → 右     [9, 3, 15, 20, 7]
    后序：左 → 右 → 根     [9, 15, 7, 20, 3]

        3
       / \
      9   20
         /  \
        15   7
```

```python
# 前序 - 模板
def preorder(root):
    if not root: return []
    return [root.val] + preorder(root.left) + preorder(root.right)

# 中序 - 模板
def inorder(root):
    if not root: return []
    return inorder(root.left) + [root.val] + inorder(root.right)

# 后序 - 模板
def postorder(root):
    if not root: return []
    return postorder(root.left) + postorder(root.right) + [root.val]
```

### 🌲 迭代遍历（必须掌握）

```python
# 前序（迭代）
def preorder_iter(root):
    if not root: return []
    res, stack = [], [root]
    while stack:
        node = stack.pop()
        res.append(node.val)
        if node.right: stack.append(node.right)  # 右先入栈
        if node.left: stack.append(node.left)    # 左后入栈（先出）
    return res

# 中序（迭代）— 核心模板
def inorder_iter(root):
    res, stack, cur = [], [], root
    while cur or stack:
        while cur:               # 一路向左
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()        # 弹出
        res.append(cur.val)      # 访问
        cur = cur.right          # 转向右子树
    return res

# 后序（迭代）— 前序反转
def postorder_iter(root):
    if not root: return []
    res, stack = [], [root]
    while stack:
        node = stack.pop()
        res.append(node.val)
        if node.left: stack.append(node.left)
        if node.right: stack.append(node.right)
    return res[::-1]  # 反转 = 后序
```

---

### 🎯 哪个遍历用在什么场景？

| 遍历方式 | 适用场景 | 典型案例 |
|---------|---------|---------|
| **前序** | 拷贝/构建树、序列化、统计好节点 | Serialize Tree, Good Nodes |
| **中序** | BST相关（排序特性）、验证BST | Validate BST, Kth Smallest |
| **后序** | 子树信息向上传递、最大路径和 | Max Path Sum, LCA |
| **层序** | 按层处理、最短路径、右视图 | Level Order, Right Side View |

---

## 套路一：DFS递归三板斧

### 🪓 板斧1：自顶向下（前序传递参数）

**特征**：传递信息从根到叶，每个节点基于父节点信息做判断

**模板**：
```python
def solve(root):
    def dfs(node, curr_info):
        if not node:
            return  # 或处理边界
        # 前序位置：更新当前信息
        curr_info = update(curr_info, node)
        if not node.left and not node.right:
            # 叶子节点 → 更新答案
            update_answer(curr_info)
        dfs(node.left, curr_info)
        dfs(node.right, curr_info)
    dfs(root, initial_info)
    return answer
```

**例题**：最大深度、路径总和、好节点计数

**示例 — 最大深度**：
```python
def maxDepth(root):
    if not root: return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```
> 小白问：「为什么是 1 + max(...)？」  
> 答：想象你在数台阶。当前节点站了1层，左边有left层，右边有right层。你往深走，取左右中更深的那边。这就是「1 + max(左深度, 右深度)」。

---

### 🪓 板斧2：自底向上（后序返回值）

**特征**：子树把计算结果返回给父节点，父节点整合子节点结果

**模板**：
```python
def solve(root):
    ans = initial_value
    def dfs(node):
        if not node: return base_value
        left = dfs(node.left)    # 左子树结果
        right = dfs(node.right)  # 右子树结果
        # 后序位置：整合左右结果
        cur = merge(left, right, node)
        ans = max(ans, cur)      # 更新全局答案
        return cur               # 返回给上一层
    dfs(root)
    return ans
```

**例题**：最大路径和、最长ZigZag路径

**示例 — 最大路径和**：
```python
def maxPathSum(root):
    ans = -10**9
    def dfs(node):
        nonlocal ans
        if not node: return 0
        l = max(dfs(node.left), 0)   # 左贡献（负值→0）
        r = max(dfs(node.right), 0)  # 右贡献（负值→0）
        ans = max(ans, node.val + l + r)  # 经过当前节点的最大路径
        return node.val + max(l, r)       # 向父节点贡献的最大单边
    dfs(root)
    return ans
```

> 小白问：「为什么左/右贡献要 max(..., 0)？」  
> 答：想象你在组织登山路线。如果左边是悬崖（负值），你肯定不会走那边，所以贡献=0。只有正贡献才值得走！

---

### 🪓 板斧3：带全局状态（回溯）

**特征**：遍历过程中维护一个全局变量或哈希表，在进入和离开节点时更新状态

**模板**：
```python
def solve(root):
    prefix = {0: 1}  # 前缀和计数
    ans = 0
    
    def dfs(node, cur_sum):
        nonlocal ans
        if not node: return
        
        cur_sum += node.val
        # 检查是否存在 cur_sum - target 的前缀
        ans += prefix.get(cur_sum - target, 0)
        prefix[cur_sum] = prefix.get(cur_sum, 0) + 1
        
        dfs(node.left, cur_sum)
        dfs(node.right, cur_sum)
        
        prefix[cur_sum] -= 1  # 回溯！离开时清除
    
    dfs(root, 0)
    return ans
```

**例题**：Path Sum III（前缀和）、二叉树中的最长交错路径

---

## 套路二：层序遍历（BFS）四步法

### 📐 标准模板

```python
def levelOrder(root):
    if not root: return []
    res, q = [], collections.deque([root])
    while q:
        level = []
        for _ in range(len(q)):     # 第①步：先看队列大小
            node = q.popleft()      # 第②步：弹出队首
            level.append(node.val)  # 第③步：处理当前节点
            if node.left: q.append(node.left)   # 第④步：左右入队
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

### 🔧 常见变体

| 变体 | 改动点 | 代码 | 
|------|--------|------|
| **右视图** | 只取每层最后一个 | `level[-1]` |
| **Zigzag** | 偶数层反转 | `if 层数%2==1: level=level[::-1]` |
| **层平均值** | 求和/长度 | `sum(level)/len(level)` |
| **最大层和** | 记录最大的sum | `max_sum = max(max_sum, sum(level))` |
| **最左下角** | 最后一行第一个 | 最后一层 `level[0]` |

```python
# 右视图 — 只取每层最后一个节点
def rightSideView(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            n = q.popleft()
            level.append(n.val)
            if n.left: q.append(n.left)
            if n.right: q.append(n.right)
        res.append(level[-1])  # ★ 只取最后一个
    return res

# Zigzag层序 — 偶数层反转
def zigzagLevelOrder(root):
    if not root: return []
    res, q, level_num = [], deque([root]), 0
    while q:
        level = []
        for _ in range(len(q)):
            n = q.popleft()
            level.append(n.val)
            if n.left: q.append(n.left)
            if n.right: q.append(n.right)
        if level_num % 2 == 1:  # ★ 偶数层反转
            level = level[::-1]
        res.append(level)
        level_num += 1
    return res
```

---

## 套路三：BST中序有序大法

### 💡 核心思想

BST 的中序遍历 = **升序序列**。所有 BST 题目本质上都在利用或保持这个性质。

```
    5                  中序遍历：
   / \         →       1, 3, 4, 5, 7, 8, 9
  3   8
 / \   \
1   4   9
```

### 🔧 常见应用

```python
# #1 验证 BST — 中序递增
def isValidBST(root):
    prev = -10**18
    stack, cur = [], root
    while cur or stack:
        while cur:
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()
        if cur.val <= prev:  # 不满足递增
            return False
        prev = cur.val
        cur = cur.right
    return True

# #2 第K小 — 中序走到第K个
def kthSmallest(root, k):
    stack, cur = [], root
    while cur or stack:
        while cur:
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()
        k -= 1
        if k == 0: return cur.val
        cur = cur.right

# #3 最小绝对差 — 相邻差值最小
def getMinimumDifference(root):
    prev, ans = -10**18, 10**18
    stack, cur = [], root
    while cur or stack:
        while cur:
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()
        ans = min(ans, cur.val - prev)  # ★ 相邻差值
        prev = cur.val
        cur = cur.right
    return ans
```

### 📐 BST递归验证模板

```python
def isValidBST(root):
    def dfs(node, lo, hi):
        if not node: return True
        if not (lo < node.val < hi):  # 必须在(lo, hi)范围内
            return False
        # 左子树：lo不变，hi变为当前值
        # 右子树：lo变为当前值，hi不变
        return dfs(node.left, lo, node.val) and dfs(node.right, node.val, hi)
    return dfs(root, -10**18, 10**18)
```

> 小白问：「为什么验证 BST 要用范围 (lo, hi)？」  
> 答：想象检查学生证。每个节点都说「我必须在左边界和右边界之间」。  
> 根节点：`(-∞, +∞)` 随便  
> 左孩子：`(-∞, 5)` 必须小于5  
> 右孩子：`(5, +∞)` 必须大于5  
> 递归传下去，8的右孩子是 `(8, +∞)`，它如果=9，OK ✅

---

## 套路四：路径问题模式

树中的「路径」问题有四种常见模式，每种对应不同的解题思路。

### 模式1：根→叶路径（Path Sum）

```python
def hasPathSum(root, targetSum):
    if not root: return False
    if not root.left and not root.right:  # 叶子节点
        return targetSum == root.val
    return hasPathSum(root.left, targetSum - root.val) or \
           hasPathSum(root.right, targetSum - root.val)
```

### 模式2：任意起点→任意终点，求路径条数（Path Sum III）

```python
def pathSum(root, targetSum):
    prefix = {0: 1}  # 前缀和 → 出现次数
    
    def dfs(node, cur):
        if not node: return 0
        cur += node.val
        # 查表：存在多少个前缀和 = cur - targetSum
        cnt = prefix.get(cur - targetSum, 0)
        prefix[cur] = prefix.get(cur, 0) + 1
        cnt += dfs(node.left, cur) + dfs(node.right, cur)
        prefix[cur] -= 1  # ★ 回溯！离开后清除
        return cnt
    
    return dfs(root, 0)
```

> 小白问：「前缀和是个啥？」  
> 答：想象你在逛一条街，每到一个路口（节点）记录兜里总共多少钱（累计和）。  
> 如果到了某家店，发现「当前钱 - 目标金额」这个数以前曾经出现过 → 说明那一段路加起来正好是目标金额！

### 模式3：任意节点间最大路径和（Max Path Sum）

```python
def maxPathSum(root):
    ans = -10**9
    def dfs(node):
        nonlocal ans
        if not node: return 0
        l = max(dfs(node.left), 0)
        r = max(dfs(node.right), 0)
        # 经过当前节点的最大路径 = 自己 + 左贡献 + 右贡献
        ans = max(ans, node.val + l + r)
        # 向父节点只贡献单边
        return node.val + max(l, r)
    dfs(root)
    return ans
```

### 模式4：最近公共祖先（LCA）

```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root
    l = lowestCommonAncestor(root.left, p, q)
    r = lowestCommonAncestor(root.right, p, q)
    if l and r:  # 左右各找到一个 → root就是LCA
        return root
    return l or r  # 只有一个找到 → 返回找到的那个
```

> 小白问：「LCA的递归思路是怎么想的？」  
> 答：找祖先就像家里找失散多年的亲戚。  
> 你问左邻居「小P在左边吗？」又在右邻居「小Q在右边吗？」  
> 如果左右各有一个，说明我就是它们的共同祖先！  
> 如果只在一边找到，那祖先就在那一边。

---

## 树的构建与序列化

### 前序+中序 → 构建二叉树

```python
def buildTree(preorder, inorder):
    idx_map = {v: i for i, v in enumerate(inorder)}
    pre_idx = 0
    
    def build(l, r):
        nonlocal pre_idx
        if l > r: return None
        root_val = preorder[pre_idx]
        root = TreeNode(root_val)
        mid = idx_map[root_val]  # 根在中序中的位置
        pre_idx += 1              # 前序指针后移
        # ★ 必须先左后右！
        root.left = build(l, mid-1)
        root.right = build(mid+1, r)
        return root
    
    return build(0, len(inorder)-1)
```

```
前序: [3, 9, 20, 15, 7]   根 | 左 | 右
中序: [9, 3, 15, 20, 7]   左 | 根 | 右

步骤1: 前序[0]=3 → 根=3
       在中序中找到3 → 左边=[9] 右边=[15,20,7]
步骤2: 前序[1]=9 → 根3的左孩子=9
       在中序9的左右都没有 → 叶子
步骤3: 前序[2]=20 → 根3的右孩子=20
       在中序中找到20 → 左边=[15] 右边=[7]
...
```

### 序列化与反序列化（前序DFS）

```python
class Codec:
    def serialize(self, root):
        vals = []
        def dfs(n):
            if not n:
                vals.append("#")   # null标记
                return
            vals.append(str(n.val))
            dfs(n.left)
            dfs(n.right)
        dfs(root)
        return ",".join(vals)
    
    def deserialize(self, data):
        vals = iter(data.split(","))
        def dfs():
            v = next(vals)
            if v == "#": return None
            n = TreeNode(int(v))
            n.left = dfs()
            n.right = dfs()
            return n
        return dfs()
```

```
序列化示例：
    1
   / \
  2   3
     / \
    4   5

→ "1,2,#,#,3,4,#,#,5,#,#"
```

---

## 高频题精讲

### 题1：二叉树的最大深度 ⭐

**题目**：给定二叉树 root，返回最大深度（根到最远叶子的节点数）

**思路**：后序递归，取左右最大深度+1

```python
def maxDepth(root):
    if not root: return 0
    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```

**时空复杂度**：O(n) 时间，O(h) 空间（h为树高）

---

### 题2：二叉树的层序遍历 ⭐⭐

**题目**：按层返回节点值，每层一个列表

**思路**：BFS + 每层for循环

```python
def levelOrder(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            n = q.popleft()
            level.append(n.val)
            if n.left: q.append(n.left)
            if n.right: q.append(n.right)
        res.append(level)
    return res
```

**时空复杂度**：O(n) 时间，O(n) 空间（队列最多存一层最多节点）

---

### 题3：验证二叉搜索树 ⭐⭐

**题目**：判断二叉树是否为BST（左<根<右，递归成立）

**思路**：中序递增或范围递归

```python
def isValidBST(root):
    def dfs(node, lo, hi):
        if not node: return True
        if not (lo < node.val < hi): return False
        return dfs(node.left, lo, node.val) and dfs(node.right, node.val, hi)
    return dfs(root, -10**18, 10**18)
```

**时空复杂度**：O(n) 时间，O(h) 空间

---

### 题4：路径总和 III ⭐⭐⭐

**题目**：求二叉树中和为 targetSum 的路径数目（路径不要求从根开始）

**思路**：前缀和 + 回溯

```python
def pathSum(root, targetSum):
    prefix = {0: 1}
    def dfs(node, cur):
        if not node: return 0
        cur += node.val
        cnt = prefix.get(cur - targetSum, 0)
        prefix[cur] = prefix.get(cur, 0) + 1
        cnt += dfs(node.left, cur) + dfs(node.right, cur)
        prefix[cur] -= 1  # 回溯！
        return cnt
    return dfs(root, 0)
```

**时空复杂度**：O(n) 时间，O(n) 空间

---

### 题5：二叉树的最近公共祖先 ⭐⭐⭐

**题目**：给定p、q，找到它们的最近公共祖先

**思路**：后序递归查找

```python
def lowestCommonAncestor(root, p, q):
    if not root or root == p or root == q:
        return root
    l = lowestCommonAncestor(root.left, p, q)
    r = lowestCommonAncestor(root.right, p, q)
    if l and r: return root
    return l or r
```

**时空复杂度**：O(n) 时间，O(h) 空间

---

## 性能优化与常见坑

### ❌ 常见错误 TOP 5

| # | 错误 | 正确做法 |
|---|------|---------|
| 1 | `preorder.pop(0)` | 用索引或哈希表，O(1)而不是O(n) |
| 2 | Prefix Sum忘记回溯 | `prefix[cur] -= 1` 离开节点时清除 |
| 3 | 最大路径和贡献取负值 | 取 `max(contrib, 0)` 避免负贡献 |
| 4 | BST验证只检查局部 | 必须用范围 (lo, hi) 递归检查 |
| 5 | 层序遍历忘记固定len(q) | 每次for循环用 `len(q)` 固定本轮大小 |

### 🚀 性能要点

- **递归 vs 迭代**：递归更易写但可能栈溢出（树深>1000时用迭代）
- **哈希表优化构建**：提前构建 `val→index` 映射，避免O(n²)
- **前缀和**：Path Sum III 的 O(n) 解法必须掌握
- **位运算**：完全二叉树计数 `1 << h` 等价于 `2**h`

---

## OD机考实战指南

### 🎯 本主题出题特点

| 考点 | 出现频率 | 难度 | 建议用时 |
|------|---------|------|---------|
| 层序遍历及其变体 | ⭐⭐⭐⭐⭐ | 中等 | 10-15min |
| BST验证/中序应用 | ⭐⭐⭐⭐ | 中等 | 10-15min |
| 树的构建 | ⭐⭐⭐ | 中高 | 15-20min |
| LCA | ⭐⭐⭐ | 中高 | 10-15min |
| 最大路径和 | ⭐⭐ | 困难 | 15-20min |
| 序列化 | ⭐⭐ | 中等 | 10-15min |

### ⏱ 时间分配建议

```
OD 100分题（树类）：
├── 理解题意：2min
├── 选对遍历方式：1min
├── 写代码：5-8min
├── 调试：3-5min
└── 总计：12-15min ✅
```

### 📌 实战心法

1. **遇到树，先想遍历方式**：是前序（自上而下）、后序（自下而上）、还是层序（按层）？
2. **遇到BST，先想中序**：中序有序是BST的超级武器
3. **遇到路径和，想前缀和**：任意起点→任意终点路径
4. **遇到公共祖先，想后序**：后序天然适合子树信息汇总

---

## 本周总结

### 📊 正确率自测表

| 题型 | 题数 | 思路清晰 ✅ | 代码默写 ✅ |
|------|------|-----------|-----------|
| 遍历（递归/迭代） | 6 | □ | □ |
| 树的构建 | 6 | □ | □ |
| 路径问题 | 6 | □ | □ |
| BFS 层序 | 6 | □ | □ |
| BST | 6 | □ | □ |
| 综合 | 6 | □ | □ |

### 🎯 复盘要点

1. **中序遍历**是 BST 的灵魂——验证、第 K 小、最小差值、累加树都靠它
2. **层序遍历模板**必须烂熟于心——右视图、zigzag、平均值都是小改
3. **路径和 III 的前缀和**和**最大路径和的后序贡献**是两道最难题，建议反复看
4. **LCA 递归**理解口诀：左右各找——各有一个返回根——一个有一边则返回那个

### 🚀 下周预告

**第5周：回溯算法** → 全排列、子集、组合、N 皇后、单词搜索等

---

## 今日作业

### ⭐ 简单

**1️⃣ 二叉树的最小深度**
```python
# 求根到最近叶子节点的距离
# 注意：如果左子树为空，不能走左边
def minDepth(root):
    if not root: return 0
    if not root.left: return 1 + minDepth(root.right)
    if not root.right: return 1 + minDepth(root.left)
    return 1 + min(minDepth(root.left), minDepth(root.right))
```

### ⭐⭐ 中等

**2️⃣ 二叉树的右视图**（复习BFS层序）

**3️⃣ 二叉搜索树中的搜索 + 删除节点**（复习BST操作）

### ⭐⭐⭐ 进阶

**4️⃣ 从前序与中序遍历序列构造二叉树**（独立默写，10分钟内完成）

**5️⃣ 二叉树中的最大路径和**（独立默写，理解为什么贡献要max(.,0)）

---

> 💪 **第4周结束！你已经掌握了树与图的全部核心知识。**  
> 下周开始，我们将进入回溯算法的世界——全排列、子集、组合……  
> 准备好接受新的挑战了吗？🚀

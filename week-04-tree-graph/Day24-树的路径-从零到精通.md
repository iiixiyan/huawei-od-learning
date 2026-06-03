# 📘 Day 24：树的路径 — 从零到精通

> 🎯 **学习目标**：掌握二叉树路径问题的四大核心套路——从根到叶子路径、任意路径和、最大路径和、交错路径。今天将带你从"只知道树的遍历"进阶到"任何路径问题都能手撕代码"！
>
> 🗓️ **OD备考**：第4周·Day 24 | 二叉树·图专题
>
> 💡 **核心一句话**：**树的路径问题 = DFS遍历 + 路径状态维护 + 回溯恢复**，万变不离其宗！

---

## 📋 目录

1. [🤔 什么是"树的路径"？](#1-什么是树的路径)
2. [🌳 常见操作大全](#2-常见操作大全)
3. [🎯 四大核心套路详解](#3-四大核心套路详解)
4. [✏️ 高频题精讲](#4-高频题精讲)
5. [⚠️ 性能优化与常见坑](#5-性能优化与常见坑)
6. [🎮 OD机考实战指南](#6-od机考实战指南)
7. [✅ 今日作业](#7-今日作业)

---

## 1. 🤔 什么是"树的路径"？

### 🧠 先来个生活例子

> 想象你在一棵大树（真正的🌳）下面，树干是根节点，树枝分叉是中间节点，树叶是叶子节点。
>
> **路径**就是：从树干出发，沿着树枝走到某片树叶的路线。比如：`树干 → 大枝 → 小枝 → 树叶A`
>
> 而在二叉树里，路径就是沿 `root → 中间节点 → ... → 叶子节点` 一路走下去，把经过的节点值加起来看总和是多少。

### 📌 路径问题的三种模式

```
模式1：根 → 叶子（完整路径）
    ① → ② → ③      判断路径和是否 = target
    
模式2：根 → 任意节点（前缀路径）
    ① → ② ...       计算路径上的数值
    
模式3：任意节点 → 任意节点（最大/任意路径）
      ②              可以拐弯，不一定要从根开始
     ↙  ↘
    ①    ③           
```

---

## 2. 🌳 常见操作大全

### 📝 二叉树节点定义（面试前默写！）

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### 🔧 所有树的路径相关操作

| 操作 | 描述 | 时间复杂度 |
|:---:|:---|:---:|
| 🔍 判断是否存在根到叶子的路径和为 target | 递归减值，叶子判断 | O(N) |
| 📊 计算根到叶子所有数字之和 | DFS传累积值，叶子累加 | O(N) |
| 🔢 统计任意路径和为 target 的数量 | 前缀和 + 回溯计数 | O(N) |
| 🏆 求二叉树最大路径和 | 后序遍历，返回单侧最大贡献 | O(N) |
| ⚡ 最长交错路径 | DFS传方向+长度 | O(N) |
| ✂️ 剪枝（删除不含1的子树） | 后序遍历，自底向上删除 | O(N) |

---

## 3. 🎯 四大核心套路详解

### 套路1：🧩 自顶向下递减法

**识别特征**：从 **根到叶子**，判断是否存在满足条件的路径

**适用场景**：Path Sum（判断是否存在和为target的根到叶子路径）

**思路图解**：
```
        5 (target=22)
       / \
      4   8        从根开始，每走一步减去当前节点值
     /   / \       走到叶子时，看剩余值是否=0
    11  13  4
   /  \      \
  7    2      1

  22-5=17 → 17-4=13 → 13-11=2 → 2-7=-5 ✗
                                    → 2-2=0 ✓ 找到！
```

**模板代码** 💾
```python
def hasPathSum(root: Optional[TreeNode], targetSum: int) -> bool:
    # 1️⃣ 空节点：不存在路径
    if not root:
        return False
    
    # 2️⃣ 减去当前节点值
    targetSum -= root.val
    
    # 3️⃣ 叶子节点：判断是否为0
    if not root.left and not root.right:
        return targetSum == 0
    
    # 4️⃣ 递归左右子树
    return hasPathSum(root.left, targetSum) or hasPathSum(root.right, targetSum)
```

**复杂度**：⏱️ O(N) | 💾 O(H)（H为树高，递归栈深度）

---

### 套路2：🔢 路径值累积法

**识别特征**：从 **根到叶子**，路径上的值按位组合成数字

**适用场景**：Sum Root to Leaf Numbers（根到叶子数字之和）

**思路图解**：
```
      1
     / \
    2   3

  路径1→2：cur = 0*10+1=1 → 1*10+2=12 ✓
  路径1→3：cur = 0*10+1=1 → 1*10+3=13 ✓
  总和 = 12 + 13 = 25
```

**模板代码** 💾
```python
def sumNumbers(root: Optional[TreeNode]) -> int:
    def dfs(node, cur):
        # 1️⃣ 空节点：和为0
        if not node:
            return 0
        
        # 2️⃣ 累加当前数字（像拼接字符串一样）
        cur = cur * 10 + node.val
        
        # 3️⃣ 叶子节点：返回当前数字
        if not node.left and not node.right:
            return cur
        
        # 4️⃣ 递归左右子树，累加结果
        return dfs(node.left, cur) + dfs(node.right, cur)
    
    return dfs(root, 0)
```

> 💡 **小白问**：为什么要 `cur * 10 + val`？
> **答**：就像把数字拼在一起！路径 1→2→3 要拼成数字 123，就是 0→1→12→123。每走一步先把前面的数×10（腾出个位数位置），再加上当前节点的值。

**复杂度**：⏱️ O(N) | 💾 O(H)

---

### 套路3：🔑 前缀和回溯法 ⭐核心重点⭐

**识别特征**：**任意节点到任意节点**（不一定从根出发，也不一定到叶子结束），统计和为target的路径数

**适用场景**：Path Sum III（二叉树中路径和为target的数量）

**思路图解**：
```
       10
      /  \
     5   -3
    / \     \
   3   2     11
  / \    \
 3  -2    1

target = 8

前缀和记录：{0:1}（初始空路径）
走到10：前缀和=10  → 找 10-8=2 不在字典中，cnt=0
  → 走到5：前缀和=15 → 找 15-8=7 不在，cnt=0
    → 走到3：前缀和=18 → 找 18-8=10 存在！cnt=1（路径10→5→3）
```

**模板代码** 💾
```python
def pathSum(root: Optional[TreeNode], targetSum: int) -> int:
    # 1️⃣ 前缀和字典：key=前缀和，value=出现次数
    #    初始：前缀和为0出现1次（空路径）
    prefix = {0: 1}
    
    def dfs(node, cur):
        if not node:
            return 0
        
        # 2️⃣ 更新当前前缀和
        cur += node.val
        
        # 3️⃣ 关键一步：cur - targetSum 在前缀和中出现过几次？
        #    每次出现都代表一条以当前节点结尾的合法路径
        cnt = prefix.get(cur - targetSum, 0)
        
        # 4️⃣ 记录当前前缀和，继续DFS
        prefix[cur] = prefix.get(cur, 0) + 1
        cnt += dfs(node.left, cur)
        cnt += dfs(node.right, cur)
        
        # 5️⃣ 回溯：恢复字典（重要！避免影响其他分支）
        prefix[cur] -= 1
        
        return cnt
    
    return dfs(root, 0)
```

> 💡 **小白问**：为什么要有前缀和？为什么不是直接DFS求和？
> **答**：因为路径可以是任意起点！比如路径 `5→3` 的和是8，但如果我们从根10开始累加，到节点3时和是18。根据公式：`当前路径和 - 目标值 = 之前某个前缀和`，即 `18 - 8 = 10`。字典里有 `{10:1}` 说明先前缀和为10的路径存在，所以 `18-10=8` 这段路径就是我们要找的！✌️

**复杂度**：⏱️ O(N) | 💾 O(N)

---

### 套路4：🏆 后序贡献值法

**识别特征**：求 **整棵树的最大路径和**，路径可以从任意节点开始，在任意节点结束，**可以拐弯**

**适用场景**：Binary Tree Maximum Path Sum（二叉树最大路径和）

**思路图解**：
```
     -10
     / \
    9   20
       /  \
      15   7
    
后序遍历，自底向上：
节点9：left贡献=0（空），right贡献=0（空），单侧最大=9
      过9的最大路径=9+0+0=9，全局max=9
节点15：单侧最大=15，过15的最大路径=15，全局max=15
节点7：单侧最大=7，过7的最大路径=7，全局max=15
节点20：左贡献=max(15,0)=15，右贡献=max(7,0)=7
        单侧最大=20+max(15,7)=35
        过20的最大路径=20+15+7=42，全局max=42
节点-10：左贡献=max(9,0)=9，右贡献=max(35,0)=35
         单侧最大=-10+max(9,35)=25
         过-10的最大路径=-10+9+35=34，全局max=42（不变）
最终结果：42 🎉
```

**模板代码** 💾
```python
def maxPathSum(root: Optional[TreeNode]) -> int:
    ans = -10**9  # 全局最大值，初始化为极小值
    
    def dfs(node):
        nonlocal ans
        if not node:
            return 0
        
        # 1️⃣ 递归计算左右子树的最大贡献
        #    max(贡献, 0)：负贡献不选（相当于切断）
        left = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        
        # 2️⃣ 过当前节点的最大路径和（可以拐弯！）
        #    node.val + left + right 就是"当前节点的值+左贡献+右贡献"
        ans = max(ans, node.val + left + right)
        
        # 3️⃣ 返回单侧最大贡献（给父节点用，不能拐弯）
        return node.val + max(left, right)
    
    dfs(root)
    return ans
```

> 💡 **小白问**：为什么left和right要max(..., 0)？
> **答**：如果子树的贡献是负数，加上它反而让路径和变小了！所以干脆不选它（当成0），就像砍掉这根坏树枝🌿✂️

**复杂度**：⏱️ O(N) | 💾 O(H)

---

## 4. ✏️ 高频题精讲

### 题1：Path Sum（⭐简单）

**题目**：判断二叉树中是否存在 **根节点到叶子节点** 的路径，使得路径上节点值之和等于 `targetSum`。

**解题思路**：
- 使用套路1「自顶向下递减法」
- 每走一步用 targetSum 减去当前节点值
- 到达叶子节点时判断剩余是否等于0

**代码**：
```python
def hasPathSum(root: Optional[TreeNode], targetSum: int) -> bool:
    if not root:
        return False
    targetSum -= root.val
    if not root.left and not root.right:
        return targetSum == 0
    return hasPathSum(root.left, targetSum) or hasPathSum(root.right, targetSum)
```

**时空复杂度**：⏱️ O(N) | 💾 O(H)

---

### 题2：Sum Root to Leaf Numbers（⭐⭐中等）

**题目**：计算从根到叶子路径上所有数字拼接后的总和。
> 示例：路径 `1→2→3` 代表数字 123

**解题思路**：
- 使用套路2「路径值累积法」
- DFS 传 `cur * 10 + val`
- 叶子节点返回当前累积值，非叶子返回左右子树之和

**代码**：
```python
def sumNumbers(root: Optional[TreeNode]) -> int:
    def dfs(node, cur):
        if not node:
            return 0
        cur = cur * 10 + node.val
        if not node.left and not node.right:
            return cur
        return dfs(node.left, cur) + dfs(node.right, cur)
    return dfs(root, 0)
```

**时空复杂度**：⏱️ O(N) | 💾 O(H)

---

### 题3：Path Sum III（⭐⭐⭐中等）

**题目**：统计二叉树中路径和等于 targetSum 的路径数量。路径 **不必从根开始，也不必在叶子结束**，但方向必须向下。

**解题思路**：
- 使用套路3「前缀和回溯法」
- 用字典记录从根到当前节点的所有前缀和出现次数
- `当前前缀和 - target` 在字典中的出现次数 = 以当前节点结尾的合法路径数
- **关键**：回溯时要恢复字典，避免影响其他分支

**代码**：
```python
def pathSum(root: Optional[TreeNode], targetSum: int) -> int:
    prefix = {0: 1}
    
    def dfs(node, cur):
        if not node:
            return 0
        cur += node.val
        cnt = prefix.get(cur - targetSum, 0)
        prefix[cur] = prefix.get(cur, 0) + 1
        cnt += dfs(node.left, cur)
        cnt += dfs(node.right, cur)
        prefix[cur] -= 1  # 回溯恢复
        return cnt
    
    return dfs(root, 0)
```

> ⚠️ **易错点**：容易忘记回溯恢复 `prefix[cur] -= 1`！不恢复的话，另一条分支的前缀和会污染当前路径。

**时空复杂度**：⏱️ O(N) | 💾 O(N)

---

### 题4：Binary Tree Maximum Path Sum（⭐⭐⭐⭐困难）

**题目**：二叉树中的路径被定义为节点序列，每个节点最多出现一次，且不一定经过根。求 **最大路径和**。

**解题思路**：
- 使用套路4「后序贡献值法」
- 后序遍历，自底向上计算每个节点的"最大单侧贡献"
- 在每个节点处，更新全局最大路径和（节点值 + 左贡献 + 右贡献）
- 负贡献取0（直接丢弃）

**代码**：
```python
def maxPathSum(root: Optional[TreeNode]) -> int:
    ans = -10**9
    
    def dfs(node):
        nonlocal ans
        if not node:
            return 0
        left = max(dfs(node.left), 0)
        right = max(dfs(node.right), 0)
        ans = max(ans, node.val + left + right)
        return node.val + max(left, right)
    
    dfs(root)
    return ans
```

> 💡 **小白问**：为什么后序遍历？
> **答**：因为最大路径和需要在每个节点"汇聚"左右子树的信息，必须先把子树的结果算出来才能确定当前节点的贡献。这就像盖楼🏗️——得先把地基打好才能盖上层！

**时空复杂度**：⏱️ O(N) | 💾 O(H)

---

### 题5：Longest ZigZag Path（⭐⭐⭐中等）

**题目**：求二叉树中最长的"左右交替"路径长度。每次必须改变方向（左→右→左→右...）。

**解题思路**：
- DFS 传递三个信息：当前节点、方向、当前 zigzag 长度
- 向左走时，如果上一步也是向左，长度重置为1；否则长度+1
- 向右走时同理
- 全局变量记录最大值

**代码**：
```python
def longestZigZag(root: Optional[TreeNode]) -> int:
    ans = 0
    
    def dfs(node, direction, length):
        nonlocal ans
        ans = max(ans, length)
        if node.left:
            dfs(node.left, 'L', length + 1 if direction != 'L' else 1)
        if node.right:
            dfs(node.right, 'R', length + 1 if direction != 'R' else 1)
    
    if root.left:
        dfs(root.left, 'L', 1)
    if root.right:
        dfs(root.right, 'R', 1)
    return ans
```

**图示**：
```
      1   ← 从根开始
       \
        1  长度=1（向右）
       /
      1  长度=2（向左，方向变了✓）
       \
        1  长度=3（向右，方向变了✓）
         \
          1  长度=4（向右，方向没变❌→重置为1）

最长ZigZag = 3
```

**时空复杂度**：⏱️ O(N) | 💾 O(H)

---

## 5. ⚠️ 性能优化与常见坑

### 🚨 5大常见错误

| # | 错误 | 正确做法 |
|:-:|:---|:--------|
| 1️⃣ | Path Sum 忘记判断叶子节点 | 只有左右子节点都为 None 才是叶子 |
| 2️⃣ | 前缀和回溯忘记恢复字典 | `prefix[cur] -= 1` 一定要在函数返回前执行 |
| 3️⃣ | Max Path Sum 初始值设为 0 | 应设为 `-10**9`，因为可能有负数节点 |
| 4️⃣ | Max Path Sum 返回左右和而不是左右最大值 | 返回 `node.val + max(left, right)`（单侧） |
| 5️⃣ | ZigZag 重置条件写反 | 方向相同时重置为1，不同时+1 |

### ⚡ 边界情况要留意

```
空树：         root = None → 返回 False 或 0
单节点树：     root = [5]  → 路径就是节点本身
全负数树：     root = [-1, -2, -3] → MaxPathSum = -1（最大的负数）
全0树：        剪枝题中，全0树应返回 None
```

### 🎨 递归 vs 迭代

```python
# 递归版（推荐，清晰直观）
def hasPathSum(root, target):
    if not root: return False
    target -= root.val
    if not root.left and not root.right:
        return target == 0
    return hasPathSum(root.left, target) or hasPathSum(root.right, target)

# 迭代版（面试备用）
def hasPathSum(root, target):
    if not root: return False
    stack = [(root, target - root.val)]
    while stack:
        node, cur = stack.pop()
        if not node.left and not node.right and cur == 0:
            return True
        if node.right:
            stack.append((node.right, cur - node.right.val))
        if node.left:
            stack.append((node.left, cur - node.left.val))
    return False
```

> 💡 面试时优先写 **递归版**，代码简洁，思路清晰。如果面试官问递归深度问题，再补充迭代版。

---

## 6. 🎮 OD机考实战指南

### 📊 出题特点

| 特点 | 说明 |
|:---|:---|
| 🎯 偏好题型 | **Path Sum III**（前缀和）和 **Max Path Sum** 是高频考点 |
| ⭐ 难度分布 | 简单30% / 中等50% / 困难20% |
| 🔄 变形方向 | 二维矩阵路径和（网格DP）常出现在OD 200分题中 |
| ⏰ 时间建议 | 二叉树路径题控制在 **15-25分钟** |

### 🏃 OD考场上遇到路径题的4步法

```
Step 1 📖 读题判断：是"根→叶子"还是"任意节点"？
  ├── 根→叶子 → 套路1或套路2
  ├── 任意节点（统计数量） → 套路3（前缀和）
  └── 任意节点（最大和） → 套路4（后序贡献）

Step 2 ✍️ 画图分析：画一棵小树手动走一遍

Step 3 💻 套模板：找到对应的模板代码，填空

Step 4 ✅ 边界检查：空树、单节点、负数等特殊情况
```

### 🎯 分值分布参考

```
OD 100分题：Path Sum（简单）、Prune Tree（中等）
OD 200分题：Path Sum III + 变形（二维矩阵路径和）
```

---

## 7. ✅ 今日作业

### ⭐ 简单

**1. Path Sum（LeetCode 112）**
> 判断根到叶子是否存在和为 target 的路径
> [https://leetcode.cn/problems/path-sum/](https://leetcode.cn/problems/path-sum/)

**2. Binary Tree Pruning（LeetCode 814）**
> 删除所有不包含1的子树
> [https://leetcode.cn/problems/binary-tree-pruning/](https://leetcode.cn/problems/binary-tree-pruning/)

### ⭐⭐ 中等

**3. Sum Root to Leaf Numbers（LeetCode 129）**
> 计算所有根到叶子路径数字之和
> [https://leetcode.cn/problems/sum-root-to-leaf-numbers/](https://leetcode.cn/problems/sum-root-to-leaf-numbers/)

**4. Longest ZigZag Path（LeetCode 1372）**
> 求二叉树中最长的交错路径长度
> [https://leetcode.cn/problems/longest-zigzag-path-in-a-binary-tree/](https://leetcode.cn/problems/longest-zigzag-path-in-a-binary-tree/)

### ⭐⭐⭐ 进阶

**5. Binary Tree Maximum Path Sum（LeetCode 124）**
> 求二叉树中任意路径的最大和
> **挑战**：如果路径可以拐弯（即不限制方向必须向下），怎么做？
> [https://leetcode.cn/problems/binary-tree-maximum-path-sum/](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

**6. Path Sum III（LeetCode 437）**
> 统计路径和为target的路径数量（任意起点）
> **挑战**：如果不用前缀和，改用双重DFS（每个节点作为根再DFS），时间复杂度是多少？
> [https://leetcode.cn/problems/path-sum-iii/](https://leetcode.cn/problems/path-sum-iii/)

---

## 📝 今日总结

```
今天学了什么？
├── 🧩 套路1：自顶向下递减法 → 根到叶子路径和判断
├── 🔢 套路2：路径值累积法 → 根到叶子数字拼接
├── 🔑 套路3：前缀和回溯法 → 任意路径和计数 ⭐核心
└── 🏆 套路4：后序贡献值法 → 最大路径和

核心思想：DFS + 状态传递 + 回溯
万变不离其宗！💪
```

> 🎯 **记住**：树的路径问题，本质就是 **DFS 遍历 + 在遍历过程中维护路径信息**。遇到新题，先判断是哪种模式，再套对应的模板，最后处理边界情况。

---

> 💪 **第4周第3天 (Day 24/28)，连续24天打卡成功！** 路径问题你已经掌握了最核心的4种套路，遇到任何变体都能应对自如。明天继续冲刺二叉搜索树！🚀

# 📘 Day 23：树的构建 — 从零到精通

> 🎯 **学习目标**：掌握用遍历序列还原二叉树的构建方法，熟练串联/展平操作，理解序列化反序列化的核心思想。  
> 📅 **OD备考**：第4周 二叉树·图 — 树的构建是OD机考高频考点，几乎每周必出！  
> 💡 **核心套路一句话**：**"前/后序定根，中序定左右"** — 无论什么构建题，本质都是递归找到根节点并划分左右子树区间。

---

## 📋 目录
1. [🌳 先让小白认识二叉树](#1-先让小白认识二叉树)
2. [🔧 树的常见操作大全](#2-树的常见操作大全)
3. [🎯 核心套路详解（4板斧）](#3-核心套路详解4板斧)
4. [📝 高频题精讲](#4-高频题精讲)
5. [⚡ 性能优化与常见坑](#5-性能优化与常见坑)
6. [🎮 OD机考实战指南](#6-od机考实战指南)
7. [📚 今日作业](#7-今日作业)

---

## 1. 🌳 先让小白认识二叉树

### 小白问：二叉树到底是什么？🌲

**答**：想象一个家族族谱 👨‍👩‍👧‍👦 — 每个人最多有2个孩子（左孩子和右孩子）。最上面的老祖宗叫**根节点**，没有孩子的叫**叶子节点**。

```
      老祖宗 👑
      /      \
   爸爸      二叔
   /  \      /
  我  妹妹  堂弟
```

这就是一棵二叉树！每个圆圈叫 **节点（Node）**，连线叫 **边（Edge）**。

### 小白问：什么是遍历序列？有啥用？

**答**：遍历就是按照一定规则"走遍"整棵树，记录访问顺序。有三种最基本的走法：

```
        ①根
       /   \
    ②左    ③右
```
- **前序遍历（Preorder）**：根 → 左 → 右 → `[①, ②, ③]` 
- **中序遍历（Inorder）**：左 → 根 → 右 → `[②, ①, ③]`
- **后序遍历（Postorder）**：左 → 右 → 根 → `[②, ③, ①]`

> 💡 **记忆口诀**："前中后"指的是**根的位置**！  
> 前序=根在前，中序=根在中，后序=根在后。左永远在右前面，这是铁律！⚡

### 树构建的核心思想

**给定遍历序列，如何还原一棵树？** 🤔

就像给你一张**零件清单**（前序）和一张**组装图**（中序），让你把树拼出来！

- **前序**告诉你：谁是根（第一个元素就是根）
- **中序**告诉你：根的左边是左子树，右边是右子树

**完美配合！** 🎯

---

## 2. 🔧 树的常见操作大全

### 📦 TreeNode 定义模板

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### 操作1：前序遍历（递归）

```python
def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)
# 输出: [根, 左子树..., 右子树...]
```

### 操作2：中序遍历（递归）

```python
def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)
# 输出: [左子树..., 根, 右子树...]
```

### 操作3：后序遍历（递归）

```python
def postorder(root):
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]
# 输出: [左子树..., 右子树..., 根]
```

### 操作4：层序遍历（BFS）

```python
from collections import deque

def levelorder(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        node = q.popleft()
        res.append(node.val)
        if node.left: q.append(node.left)
        if node.right: q.append(node.right)
    return res
# 输出: 按层从左到右
```

### 操作5：获取树的高度

```python
def height(root):
    if not root:
        return 0
    return 1 + max(height(root.left), height(root.right))
```

### 操作6：判断是否为叶子节点

```python
def is_leaf(node):
    return node and not node.left and not node.right
```

---

## 3. 🎯 核心套路详解（4板斧）

### 🪓 第1板斧：前序+中序构建二叉树（最经典！）

**识别特征**：题目给了 `preorder` 和 `inorder` 两个数组，让你还原二叉树。

**原理示意图**：
```
前序: [3, | 9, | 20, 15, 7]
       根   左子树  右子树

中序: [9, | 3, | 15, 20, 7]
       左子树 根   右子树

找到根3在中序的索引=1 → 左子树大小=1
→ 前序中左子树范围: [1:1+1] = [9]
→ 前序中右子树范围: [1+1:] = [20,15,7]
```

**模板代码** 🧩：

```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    # Step 1: 用哈希表缓存中序的值→索引映射（O(1)查找！）
    idx_map = {v: i for i, v in enumerate(inorder)}
    
    # pre_idx 指向当前要处理的根节点在前序中的位置
    pre_idx = 0
    
    def build(l, r):
        """构建中序区间 [l, r] 对应的子树"""
        nonlocal pre_idx
        if l > r:
            return None
        
        # Step 2: 前序第一个就是根
        root_val = preorder[pre_idx]
        pre_idx += 1
        root = TreeNode(root_val)
        
        # Step 3: 在中序中找到根的位置
        mid = idx_map[root_val]
        
        # Step 4: 递归构建左右子树
        root.left = build(l, mid - 1)    # 左子树：中序[l, mid-1]
        root.right = build(mid + 1, r)   # 右子树：中序[mid+1, r]
        
        return root
    
    return build(0, len(inorder) - 1)
```

**时间复杂度**：O(N) — 每个节点访问一次  
**空间复杂度**：O(N) — 哈希表 + 递归栈

---

### 🪓 第2板斧：中序+后序构建二叉树

**识别特征**：给了 `inorder` 和 `postorder`，还原二叉树。

**与第1板斧的区别** ⚠️：
- 前序的根在**最前面**（pop(0)）
- 后序的根在**最后面**（pop()）
- **关键差异**：后序 pop 根后，**先构建右子树，再构建左子树**！

**原理**：后序倒着取根的顺序=先右后左，因为后序是"左→右→根"，倒过来就是"根→右→左"。

**模板代码** 🧩：

```python
def buildTree(inorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
    idx_map = {v: i for i, v in enumerate(inorder)}
    
    def build(l, r):
        if l > r or not postorder:
            return None
        
        # 后序最后一个元素是根
        root_val = postorder.pop()
        root = TreeNode(root_val)
        mid = idx_map[root_val]
        
        # ⚠️ 先右后左！（因为后序倒着取，右子树先被访问）
        root.right = build(mid + 1, r)
        root.left = build(l, mid - 1)
        
        return root
    
    return build(0, len(inorder) - 1)
```

**小白问** 🤔：为什么 pop() 取根，而不是 pop(0)？

**答**：后序的根在最后！`pop()` 从末尾取效率是 O(1)，如果 `pop(0)` 从前取是 O(N)（要移动所有元素），超慢的！🚀

**时间复杂度**：O(N)  
**空间复杂度**：O(N)

---

### 🪓 第3板斧：层序遍历串联 next 指针

**识别特征**：二叉树的每个节点除了 left/right，还有一个 next 指针，要指向同一层右边的节点。

**核心思想**：用 **dummy 哑节点** 串联每一层的子节点！

**示意图**：
```
原始树:          串联后:
    1              1 → #
   / \            / \
  2   3         2 → 3 → #
 / \   \        / \   \
4  5    7     4→5→7→#
```

**模板代码** 🧩：

```python
def connect(root: 'Node') -> 'Node':
    if not root:
        return None
    
    cur = root  # 当前层的"遍历指针"
    
    while cur:
        # 每层新建一个哑节点，作为下一层的"链表头"
        dummy = Node(0)
        prev = dummy  # prev 用于串联
        
        # 遍历当前层的所有节点（通过 next 指针）
        while cur:
            if cur.left:
                prev.next = cur.left
                prev = prev.next  # 移动 prev
            if cur.right:
                prev.next = cur.right
                prev = prev.next
            cur = cur.next  # 移动到当前层的下一个节点
        
        # cur 指向下一层的第一个节点
        cur = dummy.next
    
    return root
```

**时间复杂度**：O(N)  
**空间复杂度**：O(1) — 只用常数空间！

**适用范围**：完美二叉树或不完美二叉树均可！✅

---

### 🪓 第4板斧：前/后序序列化与反序列化

**识别特征**：要把树转成字符串（序列化），再从字符串还原树（反序列化）。

**核心思想**：用前序遍历顺序，空节点用特殊标记（如 `#`），用分隔符（如 `,`）拼接。

**序列化 → 树变字符串**：
```
    1
   / \
  2   3     →  "1,2,#,#,3,4,#,#,5,#,#"
     / \
    4   5
```

**模板代码** 🧩：

```python
class Codec:
    def serialize(self, root):
        """前序序列化：根→左→右，空节点用#"""
        def dfs(node):
            if not node:
                vals.append("#")
                return
            vals.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        
        vals = []
        dfs(root)
        return ",".join(vals)
    
    def deserialize(self, data):
        """反序列化：按前序顺序重建"""
        def dfs():
            val = next(vals)
            if val == "#":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        
        vals = iter(data.split(","))
        return dfs()
```

**时间复杂度**：O(N)  
**空间复杂度**：O(N)

**小白问** 🤔：为什么要用 `#` 标记空节点？

**答**：没有空标记，前序 `1,2,3` 既可以是：
```
    1          1
   / \   也可能是 \
  2   3          2
                  \
                   3
```
加了空标记才能唯一确定！🆔

---

## 4. 📝 高频题精讲

### 题1：从前序与中序遍历序列构造二叉树（⭐⭐⭐）

**题目**：给定 `preorder = [3,9,20,15,7]` 和 `inorder = [9,3,15,20,7]`，构造二叉树。

**解题思路** 🔍：
1. 前序第一个 `3` 是根
2. 中序找到 `3` 的位置 → 左边 `[9]` 是左子树，右边 `[15,20,7]` 是右子树
3. 前序剩下的 `[9, 20,15,7]`：`9` 是左子树的根，`20` 是右子树的根
4. 递归下去...

**代码**：
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    idx_map = {v: i for i, v in enumerate(inorder)}
    pre_idx = 0
    
    def build(l, r):
        nonlocal pre_idx
        if l > r:
            return None
        root_val = preorder[pre_idx]
        pre_idx += 1
        root = TreeNode(root_val)
        mid = idx_map[root_val]
        root.left = build(l, mid - 1)
        root.right = build(mid + 1, r)
        return root
    
    return build(0, len(inorder) - 1)
```

**时空复杂度**：⏱ O(N) | 💾 O(N)

---

### 题2：从中序与后序遍历序列构造二叉树（⭐⭐⭐）

**题目**：给定 `inorder = [9,3,15,20,7]` 和 `postorder = [9,15,7,20,3]`，构造二叉树。

**解题思路** 🔍：
1. 后序最后一个 `3` 是根
2. 中序找 `3` 的位置 → 左 `[9]`，右 `[15,20,7]`
3. 后序剩余 `[9,15,7,20]`：`pop()` 得 `20` 是右根，`7` 是右右根...
4. ⚠️ 先构建右子树，再左子树！

**代码**：
```python
def buildTree(inorder: List[int], postorder: List[int]) -> Optional[TreeNode]:
    idx_map = {v: i for i, v in enumerate(inorder)}
    
    def build(l, r):
        if l > r or not postorder:
            return None
        root_val = postorder.pop()
        root = TreeNode(root_val)
        mid = idx_map[root_val]
        root.right = build(mid + 1, r)  # 先右
        root.left = build(l, mid - 1)   # 后左
        return root
    
    return build(0, len(inorder) - 1)
```

**时空复杂度**：⏱ O(N) | 💾 O(N)

---

### 题3：填充每个节点的下一个右侧节点指针 II（⭐⭐）

**题目**：给定二叉树，每个节点增加 next 指针，指向同一层右边的节点（不要求完美二叉树）。

**解题思路** 🔍：
- 层序遍历变体，不用队列，用 **链表串联**！
- 每层用 dummy 哑节点做表头，prev 逐个串联子节点
- 不完美二叉树也不怕，跳过空节点即可

**代码**：
```python
def connect(root: 'Node') -> 'Node':
    if not root:
        return None
    cur = root
    while cur:
        dummy = Node(0)
        prev = dummy
        while cur:
            if cur.left:
                prev.next = cur.left
                prev = prev.next
            if cur.right:
                prev.next = cur.right
                prev = prev.next
            cur = cur.next
        cur = dummy.next
    return root
```

**时空复杂度**：⏱ O(N) | 💾 O(1)

---

### 题4：二叉树展开为链表（⭐⭐）

**题目**：将二叉树按前序遍历顺序展开为只有右子树的链表。

**解题思路** 🔍：
- 逆前序遍历（右→左→根）！
- 用全局变量 `prev` 记录上一个处理完的节点
- 当前节点的 `right` 指向 `prev`，`left` 置空

**图解** ✏️：
```
原始:       处理后:
    1         1
   / \         \
  2   5         2
 / \   \         \
3   4   6         3
                   \
                    4
                     \
                      5
                       \
                        6
```

**代码**：
```python
def flatten(root: Optional[TreeNode]) -> None:
    prev = None
    
    def dfs(node):
        nonlocal prev
        if not node:
            return
        dfs(node.right)   # 先右
        dfs(node.left)    # 再左
        node.right = prev  # 右指针->上一个节点
        node.left = None   # 左指针置空
        prev = node
    
    dfs(root)
```

**时空复杂度**：⏱ O(N) | 💾 O(N)（递归栈）

---

### 题5：二叉树的序列化与反序列化（⭐⭐⭐）

**题目**：设计算法将二叉树转为字符串（序列化），并能从字符串还原（反序列化）。

**解题思路** 🔍：
- 前序遍历，空节点记 `#`
- 反序列化时按相同顺序重建
- **关键**：序列化和反序列化的遍历顺序必须一致！

**代码**：
```python
class Codec:
    def serialize(self, root):
        def dfs(node):
            if not node:
                vals.append("#")
                return
            vals.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        vals = []
        dfs(root)
        return ",".join(vals)
    
    def deserialize(self, data):
        def dfs():
            val = next(vals)
            if val == "#":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        vals = iter(data.split(","))
        return dfs()
```

**时空复杂度**：⏱ O(N) | 💾 O(N)

---

### 题6：将有序数组转换为二叉搜索树（⭐）

**题目**：给定升序数组，转成高度平衡的二叉搜索树（BST）。

**解题思路** 🔍：
- 数组中间元素是根！二分递归构建左右子树
- 保证平衡：每次取中点

**代码**：
```python
def sortedArrayToBST(nums: List[int]) -> Optional[TreeNode]:
    def build(l, r):
        if l > r:
            return None
        mid = (l + r) // 2
        root = TreeNode(nums[mid])
        root.left = build(l, mid - 1)
        root.right = build(mid + 1, r)
        return root
    return build(0, len(nums) - 1)
```

**时空复杂度**：⏱ O(N) | 💾 O(log N)

---

## 5. ⚡ 性能优化与常见坑

### 🚨 常见坑 TOP5

| 坑 | 说明 | 解决方案 |
|:--|:-----|:---------|
| **① pop(0) 超慢** | 前序用 pop(0) 取根是 O(N) | 用**全局索引**替代 pop |
| **② 后序先右后左** | 中序+后序构建时左右顺序搞反 | 后序倒序取根，**先构建右子树** |
| **③ 忘记哈希表** | 每次 index() 查找 O(N) | 预处理 `idx_map` 缓存，O(1) 查找 |
| **④ 序列化没标记空** | 无法唯一确定树结构 | 用 `#` 或 `null` 标记空节点 |
| **⑤ 递归栈溢出** | 树很深（如链状）时递归爆炸 | 转迭代或用系统栈设置 `sys.setrecursionlimit` |

### ⚡ 性能优化要点

1. **哈希表加速查找** 🔑：`idx_map = {v: i for i, v in enumerate(inorder)}` — 把 O(N) 查找变成 O(1)！

2. **用索引区间代替切片** 🎯：不要 `inorder[:idx]`，每次切片 O(N) 复制数组。用 `(l, r)` 区间索引！

3. **后序用 pop() 不用 pop(0)**：后序根在末尾，`pop()` 是 O(1)，`pop(0)` 是 O(N)！

4. **递归转迭代** 🔄：如果树很深（N>10000），考虑用栈模拟递归

---

## 6. 🎮 OD机考实战指南

### 本主题在OD中的出题特点

| 特点 | 说明 |
|:----|:-----|
| 📈 **出现频率** | ⭐⭐⭐⭐⭐（几乎每周必考！） |
| 🎯 **常见题型** | 树的构建/遍历、序列化、二叉树还原 |
| ⏰ **建议时间** | 中等题15-20分钟，困难题20-30分钟 |
| 🔑 **加分解法** | 能用哈希表优化的一定要用，展示代码质量 |

### OD机考小贴士 💡

1. **先写模板框架**：TreeNode 定义 + 主函数签名写出来，再填空
2. **边界检查不丢分**：`if not root: return None` 写在最前面
3. **善用哈希表**：在中序索引查找时用 `idx_map` 展示优化意识
4. **前序+中序构建是必考**：背熟模板代码，5分钟写完！
5. **注意输入格式**：OD可能给字符串输入，需要先 parse 成数组

### 典型OD题示例

```
输入：preorder = "3,9,20,15,7"  inorder = "9,3,15,20,7"
输出：还原的二叉树 → 层序遍历输出
```

**解析思路**：先 split 字符串得到数组，再用模板代码构建，最后层序遍历输出。

---

## 7. 📚 今日作业

### ⭐ 简单

**1️⃣ 有序数组转BST**
> 实现 `sortedArrayToBST`（今天已学，自己再写一遍巩固！）

**2️⃣ 二叉树的最大深度**
> 给定一个二叉树，找出其最大深度。根节点深度为0还是1？自己定义！

### ⭐⭐ 中等

**3️⃣ 从前序与中序遍历构造二叉树**
> 不看模板，独立写出完整代码。提示：用哈希表 + 区间索引。

**4️⃣ 填充每个节点的下一个右侧节点指针**
> 试试不用额外空间（O(1)），用 dummy+prev 串联法！

### ⭐⭐⭐ 进阶

**5️⃣ 二叉树的序列化与反序列化**
> 尝试用层序遍历（BFS）实现序列化和反序列化，对比前序法的优缺点。

**6️⃣ 前序+后序能否唯一确定二叉树？**
> 思考题：给定 `preorder` 和 `postorder`，能否唯一还原二叉树？为什么？试举例说明。

---

## 🎯 今日总结

```
树的构建四板斧 🪓
├── ① 前序+中序 → 根在中序中间分左右
├── ② 中序+后序 → 根在后序末尾，先右后左！
├── ③ next串联 → dummy哑节点做链表头
└── ④ 序列化 → 前序+空标记"#"
```

> 💪 **记住一句话**：树的构建 = **找根 + 分左右 + 递归**，无论什么题都是这个套路！

---

📅 **Day 23 打卡完成！** 🎉 第4周第2天，你已经掌握了树的构建，离OD上岸又近了一步！明天 Day 24 继续二叉树进阶 — **树的性质与遍历变形**，不见不散！🚀

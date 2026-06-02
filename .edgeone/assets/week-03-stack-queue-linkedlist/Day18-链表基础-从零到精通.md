# 📘 Day 18：链表基础 — 从零到精通

> 🎯 **学习目标**：零基础掌握链表的核心操作——遍历、判环、反转、合并、相加。学会快慢指针和 dummy 节点两大护法，拿下 OD 必考链表题！
> 📅 **华为OD备考 · 第3周 · Day 18** | [返回目录](../README.md)
> 💡 **核心套路一句话**：**链表操作 = 画图 + 指针操作 + dummy 节点兜底！**

---

## 📋 目录

- [一、链表是什么？—— 用生活中的例子理解](#一链表是什么用生活中的例子理解)
- [二、Python 中链表的实现](#二python-中链表的实现)
- [三、链表常见操作大全](#三链表常见操作大全)
- [四、四大核心套路详解](#四四大核心套路详解)
  - [套路一：快慢指针 —— 判环、找中点、找倒数第K个](#套路一快慢指针判环找中点找倒数第k个)
  - [套路二：Dummy 节点 —— 让头节点操作不再特殊](#套路二dummy-节点让头节点操作不再特殊)
  - [套路三：双指针遍历 —— 相交与合并](#套路三双指针遍历相交与合并)
  - [套路四：链表反转 —— 迭代与递归双修](#套路四链表反转迭代与递归双修)
- [五、高频题精讲](#五高频题精讲)
  - [Q1 环形链表（⭐简单）](#q1-环形链表简单)
  - [Q2 环形链表 II（⭐⭐中等）](#q2-环形链表-ii中等)
  - [Q3 相交链表（⭐⭐中等）](#q3-相交链表中等)
  - [Q4 反转链表（⭐简单）](#q4-反转链表简单)
  - [Q5 合并两个有序链表（⭐简单）](#q5-合并两个有序链表简单)
  - [Q6 两数相加（⭐⭐中等）](#q6-两数相加中等)
- [六、性能优化与常见坑](#六性能优化与常见坑)
- [七、OD 机考实战指南](#七od-机考实战指南)
- [八、今日作业](#八今日作业)

---

## 一、链表是什么？—— 用生活中的例子理解

### 🧩 什么是链表？

想象你在玩一个**寻宝游戏** 🗺️：

```
🏠 起点 → 🧩 线索1 → 🧩 线索2 → 🧩 线索3 → 🏆 宝藏
```

每张线索纸条上都写着两样东西：
1. **这关的密码**（数据）
2. **下一张线索藏在哪**（指针/引用）

这就是链表！每个**节点（Node）**包含：
- **数据（val）**：存储的值
- **指针（next）**：指向下一个节点的引用

### 链表 vs 数组 的直观对比

```
📦 数组（Array）：
┌────┬────┬────┬────┬────┐
│ 10 │ 20 │ 30 │ 40 │ 50 │
└────┴────┴────┴────┴────┘
  ^    ^    ^    ^    ^
  0    1    2    3    4    ← 下标直接访问

🔗 链表（Linked List）：
┌───┐    ┌───┐    ┌───┐    ┌───┐    ┌───┐
│10 │──→│20 │──→│30 │──→│40 │──→│50 │──→ None
└───┘    └───┘    └───┘    └───┘    └───┘
```

| 特性 | 数组 | 链表 |
|------|------|------|
| 访问元素 | O(1) — 直接下标访问 | O(n) — 必须从头遍历 |
| 插入/删除头部 | O(n) — 需要移动所有元素 | O(1) — 改个指针就行 |
| 内存 | 连续空间 | 分散空间（靠指针连接） |
| 大小 | 固定（或需扩容） | 动态增长 |

### 🎯 什么时候用链表？

**链表最擅长的场景**：
- ✅ 频繁在头部插入/删除元素（比如：浏览器历史）
- ✅ 不知道总共有多少数据
- ✅ 需要把分散的内存串起来

**数组更擅长的场景**：
- ✅ 需要随机访问某个位置的元素（`arr[5]`）
- ✅ 数据量固定，尾部操作多

---

> 💡 **小白问**：为什么链表插入很快却用得少？
> **答**：因为大多数时候我们更需要「快速查找」而非「快速插入」。数据库、搜索引擎都靠数组+索引。链表在特定场景（哈希表解决冲突、LRU缓存、图邻接表）中发光发热。

---

## 二、Python 中链表的实现

### 📝 定义链表节点

LeetCode 和 OD 机考中，链表节点通常这样定义：

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val      # 节点的值
        self.next = next    # 指向下一个节点的指针
```

### 🔧 构建链表

```python
# 手动构建 1 → 2 → 3 → 4 → 5
node5 = ListNode(5)
node4 = ListNode(4, node5)
node3 = ListNode(3, node4)
node2 = ListNode(2, node3)
node1 = ListNode(1, node2)
head = node1  # head 指向第一个节点
```

### 🔍 遍历链表（最最基础的操作！）

```python
def traverse(head: ListNode):
    """从头到尾遍历链表，打印每个节点的值"""
    cur = head          # 从头部开始
    while cur:          # 只要还没到 None
        print(cur.val, end=" → ")
        cur = cur.next  # 移动到下一个节点
    print("None")

# 输出：1 → 2 → 3 → 4 → 5 → None
```

### 🔍 获取链表长度

```python
def get_length(head: ListNode) -> int:
    count = 0
    cur = head
    while cur:
        count += 1
        cur = cur.next
    return count
```

---

## 三、链表常见操作大全

### 📋 操作速查表

| 操作 | 核心方法 | 时间复杂度 | 空间复杂度 |
|------|---------|-----------|-----------|
| 遍历 | `while cur: cur = cur.next` | O(n) | O(1) |
| 获取长度 | `while cur: count++; cur = cur.next` | O(n) | O(1) |
| 查找元素 | `while cur: if cur.val == target: return` | O(n) | O(1) |
| 头部插入 | `new_node.next = head; head = new_node` | O(1) | O(1) |
| 尾部插入 | 遍历到末尾 → 追加 | O(n) | O(1) |
| 中间插入 | 找到前驱 → `node.next = prev.next; prev.next = node` | O(n) | O(1) |
| 头部删除 | `head = head.next` | O(1) | O(1) |
| 删除指定节点 | 找到前驱 → `prev.next = prev.next.next` | O(n) | O(1) |
| 反转链表 | 三指针迭代 或 递归 | O(n) | O(1) 或 O(n) |
| 合并有序链表 | dummy + 双指针比较 | O(m+n) | O(1) |
| 环检测 | 快慢指针 | O(n) | O(1) |
| 找环入口 | 快慢指针 + 同步走 | O(n) | O(1) |
| 找交点 | 双指针走等长路径 | O(m+n) | O(1) |
| 两数相加 | 遍历 + 进位 | O(max(m,n)) | O(1) |

### 🔧 常用操作代码示例

**1️⃣ 头部插入**

```python
def insert_at_head(head: ListNode, val: int) -> ListNode:
    new_node = ListNode(val)
    new_node.next = head  # 新节点指向旧头
    return new_node       # 新节点成为新头

# 示例：head = 1→2→3, 插入0
# 结果：0→1→2→3
```

**2️⃣ 尾部插入**

```python
def insert_at_tail(head: ListNode, val: int) -> ListNode:
    new_node = ListNode(val)
    if not head:           # 空链表，直接返回新节点
        return new_node
    
    cur = head
    while cur.next:        # 找到最后一个节点
        cur = cur.next
    cur.next = new_node    # 追加
    return head
```

**3️⃣ 删除指定值的节点**

```python
def delete_node(head: ListNode, val: int) -> ListNode:
    # 🔑 使用 dummy 节点避免特殊处理头节点
    dummy = ListNode(0, head)
    cur = dummy
    while cur.next:
        if cur.next.val == val:
            cur.next = cur.next.next  # 跳过要删除的节点
            break
        cur = cur.next
    return dummy.next
```

**4️⃣ 查找节点是否存在**

```python
def find_node(head: ListNode, target: int) -> bool:
    cur = head
    while cur:
        if cur.val == target:
            return True
        cur = cur.next
    return False
```

---

## 四、四大核心套路详解

### 套路一：快慢指针 —— 判环、找中点、找倒数第K个

#### 🎯 识别特征

```
题目中出现：环、中点的位置、倒数第K个节点 → 立刻想到快慢指针！
```

#### 💡 生活类比

两个人赛跑 🏃‍♂️🏃‍♂️：
- **慢指针**：每秒走1步
- **快指针**：每秒走2步

如果跑道是环形的，快的人最终会追上慢的人（套圈）！如果跑道是笔直的，快的人先到终点。

#### 📝 模板代码

```python
def fast_slow_pattern(head: ListNode):
    """快慢指针通用模板"""
    slow = fast = head
    while fast and fast.next:    # 保证 fast 可以走两步
        slow = slow.next         # 慢指针走1步
        fast = fast.next.next    # 快指针走2步
        
        # 根据具体题目在此处添加逻辑
        # if slow == fast: 有环！
    return slow  # 慢指针指向中点
```

#### 🔧 三种变体

**变体1：判环 🌀**

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:    # 🎯 快慢指针相遇 → 有环！
            return True
    return False
```

**变体2：找中点 🎯**

```python
def find_middle(head):
    """返回链表的中间节点（奇数长度返回中间，偶数长度返回第二个中间节点）"""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # slow 就是中点！
```

**变体3：找倒数第K个 🔢**

```python
def find_kth_from_end(head: ListNode, k: int) -> ListNode:
    """返回倒数第k个节点"""
    slow = fast = head
    # 1️⃣ fast 先走 k 步
    for _ in range(k):
        if not fast:   # k 大于链表长度
            return None
        fast = fast.next
    
    # 2️⃣ 同步前进，fast 到终点时 slow 就是倒数第k个
    while fast:
        slow = slow.next
        fast = fast.next
    return slow
```

---

### 套路二：Dummy 节点 —— 让头节点操作不再特殊

#### 🎯 识别特征

```
题目中出现：删除节点、合并链表、在头部插入 → 优先考虑使用 dummy 节点！
```

#### 💡 为什么要用 dummy？

想象你排成一列队伍 👥：

```
head → A → B → C → D
```

如果想删除头节点 A，你需要特殊处理：`head = head.next`

但如果有**虚拟头节点**（dummy）：

```
dummy → A → B → C → D
  ^
head
```

无论删除任何节点（包括 A），你都可以统一用：
```python
prev.next = prev.next.next  # 统一逻辑，无需特判头节点
```

#### 📝 模板代码

```python
def dummy_pattern_operation(head: ListNode):
    """Dummy 节点通用模板"""
    dummy = ListNode(0)    # 创建虚拟头节点
    dummy.next = head      # 指向真正的头
    cur = dummy            # 从 dummy 开始操作
    
    while cur.next:        # 遍历处理
        # 操作 cur.next（当前节点的下一个）
        # 例如：if condition:
        #     cur.next = cur.next.next  # 删除节点
        # else:
        #     cur = cur.next
        pass
    
    return dummy.next      # 返回真正的头
```

#### 🔧 典型应用：删除所有值为 val 的节点

```python
def remove_elements(head: ListNode, val: int) -> ListNode:
    dummy = ListNode(0, head)  # 创建 dummy，并指向 head
    cur = dummy
    
    while cur.next:
        if cur.next.val == val:
            cur.next = cur.next.next  # 跳过这个节点
        else:
            cur = cur.next            # 继续遍历
    
    return dummy.next  # 跳过 dummy，返回真正的头
```

---

### 套路三：双指针遍历 —— 相交与合并

#### 🎯 识别特征

```
题目中出现：两个链表、相交、合并 → 双指针相遇！
```

#### 📝 模板代码：相交链表

**核心思想**：两个指针分别走 A+B 和 B+A 的路程，如果相交，最终会在交点相遇！

```python
def intersection_pattern(headA: ListNode, headB: ListNode) -> ListNode:
    """双指针走等长路径——找相交节点"""
    if not headA or not headB:
        return None
    
    pa, pb = headA, headB
    while pa != pb:                   # 相遇时停止
        pa = pa.next if pa else headB   # A走完→换到B
        pb = pb.next if pb else headA   # B走完→换到A
    return pa  # 交点（或 None）
```

#### 🔬 手把手推演

以 `A = [4,1,8,4,5]`, `B = [5,6,1,8,4,5]` 为例，交点在值为8的节点：

```
第1步：pa=4, pb=5    ← 各走各的
第2步：pa=1, pb=6
第3步：pa=8, pb=1    ← pa到了交点区域
第4步：pa=4, pb=8    
第5步：pa=5, pb=4    
第6步：pa=None(换B), pb=5
第7步：pa=5(B起点), pb=None(换A)
第8步：pa=6, pb=4(A起点)
第9步：pa=1, pb=1    
第10步：pa=8, pb=8   ← 🎯 相遇！返回交点8

原理：pa走了 A+B 的长度，pb走了 B+A 的长度，路程相同！
pa走过的路径：4→1→8→4→5→None→5→6→1→8(交点!)
pb走过的路径：5→6→1→8→4→5→None→4→1→8(交点!)
```

#### 📝 模板代码：合并有序链表

```python
def merge_pattern(list1: ListNode, list2: ListNode) -> ListNode:
    """Dummy + 双指针合并有序链表"""
    dummy = cur = ListNode(0)   # dummy 兜底
    
    while list1 and list2:       # 两个链表都有元素
        if list1.val < list2.val:   # 选小的
            cur.next = list1
            list1 = list1.next
        else:
            cur.next = list2
            list2 = list2.next
        cur = cur.next            # cur 前进
    
    cur.next = list1 if list1 else list2  # 接上剩的
    return dummy.next
```

---

### 套路四：链表反转 —— 迭代与递归双修

#### 🎯 识别特征

```
题目中出现：反转、倒序、从后往前 → 反转链表或其变体！
```

#### 💡 生活类比

把一列火车（1→2→3→4→5）调转方向变成（5→4→3→2→1），你需要：
- 记住后面每节车厢
- 让每节车厢的挂钩指向它前面的车厢

#### 📝 模板代码：迭代法（推荐，面试必备！）

```python
def reverse_list_iterative(head: ListNode) -> ListNode:
    prev = None          # 前驱（初始为 None）
    cur = head           # 当前节点
    
    while cur:
        nxt = cur.next   # ① 先保存下一个（否则会丢失）
        cur.next = prev  # ② 当前节点指向前驱（反转！）
        prev = cur       # ③ 前驱前移
        cur = nxt        # ④ 当前前移
    
    return prev          # prev 就是新头
```

#### 🔬 手把手推演

反转 `1 → 2 → 3 → 4 → None`：

```
初始状态：
prev = None, cur = 1

第1轮：
  ① nxt = 2                    (保存下一个)
  ② 1.next = None              (1指向前驱None)
  ③ prev = 1                   (prev前移)
  ④ cur = 2                    (cur前移)
  结果：None ← 1    2 → 3 → 4 → None

第2轮：
  ① nxt = 3
  ② 2.next = 1                 (2指向1)
  ③ prev = 2
  ④ cur = 3
  结果：None ← 1 ← 2    3 → 4 → None

第3轮：
  ① nxt = 4
  ② 3.next = 2
  ③ prev = 3
  ④ cur = 4
  结果：None ← 1 ← 2 ← 3    4 → None

第4轮：
  ① nxt = None
  ② 4.next = 3
  ③ prev = 4
  ④ cur = None
  结果：None ← 1 ← 2 ← 3 ← 4

返回 prev = 4，反转完成！🎉
```

#### 📝 模板代码：递归法（简洁但需要理解递归）

```python
def reverse_list_recursive(head: ListNode) -> ListNode:
    # base case：空链表或只有一个节点
    if not head or not head.next:
        return head
    
    # 递归反转后面的链表
    new_head = reverse_list_recursive(head.next)
    
    # 核心操作：让 head.next.next 指向 head
    # 比如 head=4, head.next=5: 5.next = 4
    head.next.next = head
    head.next = None  # 4.next = None
    
    return new_head
```

#### 🔬 递归图解

```
reverseList(1):
  new_head = reverseList(2):
    new_head = reverseList(3):
      new_head = reverseList(4):
        new_head = reverseList(5):
          5.next = None → 返回 5
        4.next.next = 4 即 5→4
        4.next = None → 返回 5
      3.next.next = 3 即 4→3
      3.next = None → 返回 5
    2.next.next = 2 即 3→2
    2.next = None → 返回 5
  1.next.next = 1 即 2→1
  1.next = None → 返回 5

最终：None←1←2←3←4←5
```

---

## 五、高频题精讲

### Q1 环形链表（⭐简单）

**题目**：给定一个链表，判断链表中是否有环。

**来源**：[LeetCode 141](https://leetcode.cn/problems/linked-list-cycle/)

#### 💡 解题思路

用**快慢指针**！就像两个人在环形跑道上跑步 🏃‍♂️🏃‍♂️：

- 如果跑道是直的 → 快的人先到终点（`fast.next == None`）
- 如果跑道是环形的 → 快的人最终会追上慢的人（套圈）

**快慢指针相遇的数学原理**：
- 假设环长为 L，入环前有 a 个节点
- 当慢指针入环时，快指针已经在环里走了 a 步
- 快指针每次比慢指针多走 1 步，需要追 L-a 步
- 所以最多 L 步内必相遇

#### ✅ 代码

```python
def hasCycle(head: ListNode) -> bool:
    slow = fast = head
    
    while fast and fast.next:    # 必须保证 fast 能走两步
        slow = slow.next         # 慢指针走1步
        fast = fast.next.next    # 快指针走2步
        
        if slow == fast:         # 🎯 相遇了！有环！
            return True
    
    return False  # fast 到终点了，无环
```

#### 📊 复杂度分析

- **时间复杂度**：O(n) — 最坏情况下遍历整个链表
- **空间复杂度**：O(1) — 只用了两个指针

#### ⚠️ 易错点

```
❌ while fast.next and fast.next.next  → 这样会空指针异常！
✅ while fast and fast.next           → 正确写法

快指针可能走到 None，必须判 fast 本身
```

---

### Q2 环形链表 II（⭐⭐中等）

**题目**：找到链表环的入口节点。如果没有环，返回 None。

**来源**：[LeetCode 142](https://leetcode.cn/problems/linked-list-cycle-ii/)

#### 💡 解题思路

**两阶段法**：

```
第一阶段：快慢指针相遇（同Q1）
第二阶段：从 head 和相遇点同步前进，相遇处即环入口

        入口 O
          ↑
head → A → B → ... → C
                      ↓
                      D ← ...
                      ↓
                   相遇点 P

数学推导：head→O 的距离 = P→O 的距离（绕环方向）
```

**数学证明**：
- 设 head→入口 = a，入口→相遇点 = b，环长 = L
- 相遇时：慢指针走了 `a + b`，快指针走了 `a + b + kL`（多绕了k圈）
- 因为快指针速度是慢指针的2倍：`2(a+b) = a+b+kL`
- 所以：`a + b = kL`，即 `a = kL - b = (k-1)L + (L-b)`
- L-b 就是相遇点→入口的距离
- 结论：head 到入口的距离 = 相遇点到入口的距离（绕环方向）！🎯

#### ✅ 代码

```python
def detectCycle(head: ListNode) -> ListNode:
    slow = fast = head
    
    # 第一阶段：找相遇点
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:               # 🎯 相遇！
            # 第二阶段：找入口
            slow = head                # 一个指针回到起点
            while slow != fast:        # 同步前进直到相遇
                slow = slow.next
                fast = fast.next
            return slow                # 入口节点！
    
    return None  # 无环
```

#### 📊 复杂度分析

- **时间复杂度**：O(n) — 最多遍历两次链表
- **空间复杂度**：O(1)

#### 🔬 推演示例

`head = [3,2,0,-4], pos=1`（环入口在节点2）：

```
第一阶段：
  slow=3, fast=3
  → slow=2, fast=0
  → slow=0, fast=2
  → slow=-4, fast=-4  ← 🎯 相遇！
  
第二阶段：
  slow回到head=3, fast在-4
  → slow=3, fast=2(绕环)
  → slow=2, fast=2  ← 🎯 相遇在入口！返回节点2
```

---

### Q3 相交链表（⭐⭐中等）

**题目**：找到两个链表相交的起始节点。如果不想交，返回 None。

**来源**：[LeetCode 160](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

#### 💡 解题思路

**双指针走等长路径** ✨

核心思想：让两个指针分别走 A+B 和 B+A 的路程。
- 如果相交，它们会在交点相遇（因为走过的路程相同）
- 如果不想交，它们会在 None 相遇（也走了相同路程）

#### ✅ 代码

```python
def getIntersectionNode(headA: ListNode, headB: ListNode) -> ListNode:
    if not headA or not headB:
        return None
    
    pa, pb = headA, headB
    
    while pa != pb:                     # 直到相遇
        pa = pa.next if pa else headB   # A走完了换到B
        pb = pb.next if pb else headA   # B走完了换到A
    
    return pa  # 交点 or None
```

#### 📊 复杂度分析

- **时间复杂度**：O(m+n) — 两个链表各遍历一遍
- **空间复杂度**：O(1)

#### 💡 小白问：为什么换路走能相遇？

**答**：想象你和朋友从两个不同点出发，各自走完自己的路后换到对方的路继续走。如果你们要去的终点（交点）一样，你们一定会同时到达！因为你们走的总路程相同（A+B长度之和 = B+A长度之和）。

---

### Q4 反转链表（⭐简单）

**题目**：反转单链表，返回反转后的链表头。

**来源**：[LeetCode 206](https://leetcode.cn/problems/reverse-linked-list/)

#### 💡 解题思路

**三指针迭代法**（面试首选！）

三个指针各司其职：
- `prev`：前驱节点（已经反转好的部分）
- `cur`：当前要反转的节点
- `nxt`：下一个节点（存着还没反转的部分）

#### ✅ 代码

```python
# 迭代法（推荐）
def reverseList(head: ListNode) -> ListNode:
    prev = None
    cur = head
    
    while cur:
        nxt = cur.next   # ① 存下一个（不然反转后就丢了）
        cur.next = prev  # ② 反转：当前→ 指向前驱
        prev = cur       # ③ 前驱前移
        cur = nxt        # ④ 当前前移
    
    return prev          # prev 就是新头
```

```python
# 递归法（了解，面试续问）
def reverseList_recursive(head: ListNode) -> ListNode:
    if not head or not head.next:
        return head
    
    new_head = reverseList_recursive(head.next)
    head.next.next = head
    head.next = None
    
    return new_head
```

#### 📊 复杂度分析

| 方法 | 时间复杂度 | 空间复杂度 |
|------|-----------|-----------|
| 迭代 | O(n) | O(1) |
| 递归 | O(n) | O(n) — 递归栈 |

---

### Q5 合并两个有序链表（⭐简单）

**题目**：合并两个升序链表，返回一个升序链表。

**来源**：[LeetCode 21](https://leetcode.cn/problems/merge-two-sorted-lists/)

#### 💡 解题思路

**Dummy 节点 + 双指针** 🎯

就像合并两副按大小排好的扑克牌 🃏：
- 每次比较两副牌最上面一张
- 选小的放到结果堆里
- 直到一副牌用完，把剩下的全部接上

#### ✅ 代码

```python
def mergeTwoLists(list1: ListNode, list2: ListNode) -> ListNode:
    dummy = cur = ListNode(0)  # dummy 节点，统一操作
    
    while list1 and list2:          # 两个链表都有元素
        if list1.val < list2.val:   # 取较小的
            cur.next = list1
            list1 = list1.next
        else:
            cur.next = list2
            list2 = list2.next
        cur = cur.next              # cur 前进
    
    # 接上剩下的节点（如果还有的话）
    cur.next = list1 if list1 else list2
    
    return dummy.next  # 跳过 dummy
```

#### 📊 复杂度分析

- **时间复杂度**：O(m+n) — 两个链表各遍历一次
- **空间复杂度**：O(1) — 只用了几个指针

#### 🔬 推演

```
l1 = [1,2,4], l2 = [1,3,4]

dummy(0) → None
cur = dummy

第1轮：l1.val(1) >= l2.val(1) → 选l2的1
  dummy → 1(来自l2) → None, l2变[3,4], cur=1

第2轮：l1.val(1) < l2.val(3) → 选l1的1
  dummy → 1→1(来自l1) → None, l1变[2,4], cur=1

第3轮：l1.val(2) < l2.val(3) → 选l1的2
  dummy → 1→1→2, l1变[4], cur=2

第4轮：l1.val(4) > l2.val(3) → 选l2的3
  dummy → 1→1→2→3, l2变[4], cur=3

第5轮：l1.val(4) = l2.val(4) → 选l2的4（else分支）
  dummy → 1→1→2→3→4, l2=None, cur=4

剩余：l1=[4] → 接上
  dummy → 1→1→2→3→4→4

最终：1→1→2→3→4→4 🎉
```

---

### Q6 两数相加（⭐⭐中等）

**题目**：两个用链表逆序表示的非负整数，求和并以同样形式返回。

**来源**：[LeetCode 2](https://leetcode.cn/problems/add-two-numbers/)

#### 💡 解题思路

**链表遍历 + 模拟加法** 🧮

```
  2 → 4 → 3   (代表 342)
+ 5 → 6 → 4   (代表 465)
━━━━━━━━━━━━
  7 → 0 → 8   (代表 807)
```

就像小学的竖式加法：
- 从个位（链表头）开始逐位相加
- 满10进1
- 最后可能多进位（如 99+1=100）

#### ✅ 代码

```python
def addTwoNumbers(l1: ListNode, l2: ListNode) -> ListNode:
    dummy = cur = ListNode(0)
    carry = 0  # 进位值
    
    while l1 or l2 or carry:  # 任一还有值就继续
        # 计算当前位的和
        sum_val = carry
        if l1:
            sum_val += l1.val
            l1 = l1.next
        if l2:
            sum_val += l2.val
            l2 = l2.next
        
        # 计算进位和当前位数字
        carry = sum_val // 10      # 进位（如 12//10=1）
        cur.next = ListNode(sum_val % 10)  # 个位（如 12%10=2）
        cur = cur.next
    
    return dummy.next
```

#### 📊 复杂度分析

- **时间复杂度**：O(max(m,n)) — 遍历较长的链表
- **空间复杂度**：O(max(m,n)) — 结果链表的长度

#### 🔬 推演

```
l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
（代表 9999999 + 9999 = 10009998）

       carry
第1位：9+9+0=18, carry=1, 结果位=8
第2位：9+9+1=19, carry=1, 结果位=9
第3位：9+9+1=19, carry=1, 结果位=9
第4位：9+9+1=19, carry=1, 结果位=9
第5位：9+0+1=10, carry=1, 结果位=0
第6位：9+0+1=10, carry=1, 结果位=0
第7位：9+0+1=10, carry=1, 结果位=0
第8位：0+0+1=01, carry=0, 结果位=1

结果：8→9→9→9→0→0→0→1 ✓
（代表 10009998）
```

---

## 六、性能优化与常见坑

### 🚫 常见错误

#### ❌ 错误1：忘记保存 next 指针

```python
# ❌ 错误写法
cur.next = prev  # 修改了 cur.next，但丢失了原来的下一个节点
prev = cur
cur = cur.next   # cur 变成了 prev，死循环了！

# ✅ 正确写法
nxt = cur.next   # 先保存下一个
cur.next = prev  # 再反转
prev = cur
cur = nxt        # 用保存的值前进
```

#### ❌ 错误2：快慢指针只用 while fast.next

```python
# ❌ 错误写法
while fast.next:  # 如果 fast 本身是 None，会报错！
    ...

# ✅ 正确写法
while fast and fast.next:  # 两个条件都要
    ...
```

#### ❌ 错误3：忘记处理空链表

```python
# ❌ 错误
def get_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow.val  # 如果 head 是 None，会 AttributeError！

# ✅ 正确
def get_middle(head):
    if not head:     # 先判空
        return None
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```

#### ❌ 错误4：忘记处理最后可能多出的进位

```python
# ❌ 可能漏掉最后的进位
def addTwoNumbers(l1, l2):
    while l1 or l2:       # 如果最后有进位，不会处理
        ...
    return dummy.next     # 漏了最高位！

# ✅ 正确：加上 or carry
while l1 or l2 or carry:  # 即使 l1,l2 都为空，还有进位也要处理
```

### ⚡ 性能对比

| 操作 | ❌ 不好 | ✅ 好 |
|------|---------|-------|
| 多次遍历 | 每次查长度都遍历 | 一次遍历记下长度 |
| 找倒数第k个 | 先算长度再找 | 快慢指针一步到位 O(n) |
| 合并有序链表 | 用数组中转 | 直接链表操作 O(1)空间 |
| 反转链表 | 用数组存 | 三指针原地反转 O(1)空间 |

### 🎯 边界情况自查清单

- [ ] 链表为空（`head == None`）
- [ ] 链表只有一个节点
- [ ] 链表只有两个节点
- [ ] 所有节点值相同
- [ ] 值分布在负数/零/正数范围
- [ ] 链表有环/无环
- [ ] 两个链表长度相等/不相等
- [ ] 进位导致结果多一位（99+1=100）

---

## 七、OD 机考实战指南

### 🎯 链表在OD中的出题特点

| 题型 | 出现频率 | 难度 | 优先级 |
|------|---------|------|--------|
| 反转链表 | ⭐⭐⭐⭐⭐ | 简单 | ⭐⭐⭐ 必刷 |
| 环检测 | ⭐⭐⭐⭐ | 简单 | ⭐⭐⭐ 必刷 |
| 合并有序链表 | ⭐⭐⭐⭐ | 简单 | ⭐⭐⭐ 必刷 |
| 相交链表 | ⭐⭐⭐ | 中等 | ⭐⭐ 建议刷 |
| 两数相加 | ⭐⭐⭐ | 中等 | ⭐⭐ 建议刷 |
| 链表排序 | ⭐⭐ | 中等 | ⭐ 选刷 |
| 链表深拷贝 | ⭐⭐ | 较难 | ⭐ 选刷 |

### ⏰ 时间分配建议

OD 机考通常有 2-3 道题（100分 + 100分 + 200分）：

**链表题大概率出现在100分题中**：
- ⏱ 简单链表题：5-10 分钟
- ⏱ 中等链表题：10-15 分钟
- ⏱ 较难链表题：15-25 分钟

**考场策略**：
1. ✅ 先审题，确定是哪种套路（快慢指针？dummy？反转？）
2. ✏️ 在纸上画出示意图，标出指针变化
3. 💻 先写核心逻辑，再处理边界
4. 🔍 测试时重点检查：空链表、单节点、首尾操作

### 🏆 必背模版清单

OD 机考前一定要背熟以下代码（**手写无压力**）：

```python
# 1️⃣ ListNode 定义
# 2️⃣ 反转链表（迭代）
# 3️⃣ 快慢指针判环
# 4️⃣ dummy 节点模板
# 5️⃣ 合并有序链表
```

---

## 八、今日作业

### ⭐ 必做题（简单·15分钟）

**1. 反转链表**
- 题目：[LeetCode 206](https://leetcode.cn/problems/reverse-linked-list/)
- 要求：分别用迭代和递归两种方式实现
- 自测：`[1,2,3,4,5]` → `[5,4,3,2,1]`

**2. 环形链表**
- 题目：[LeetCode 141](https://leetcode.cn/problems/linked-list-cycle/)
- 要求：用快慢指针实现
- 自测：自己构造一个小环验证

### ⭐⭐ 中等题（20-30分钟）

**3. 合并两个有序链表**
- 题目：[LeetCode 21](https://leetcode.cn/problems/merge-two-sorted-lists/)
- 要求：用 dummy 节点实现，画图推演一遍
- 自测：`[1,2,4]` + `[1,3,4]` → `[1,1,2,3,4,4]`

**4. 两数相加**
- 题目：[LeetCode 2](https://leetcode.cn/problems/add-two-numbers/)
- 关键：不要漏掉最后的进位！
- 自测：`[9,9,9]` + `[1]` → `[0,0,0,1]`

### ⭐⭐⭐ 挑战题（30分钟）

**5. 环形链表 II**
- 题目：[LeetCode 142](https://leetcode.cn/problems/linked-list-cycle-ii/)
- 要求：理解数学推导过程，不要死记硬背
- 提示：为什么相遇点能用来找入口？自己能推导出来吗？

### 📝 自测清单

- [ ] 能默写出反转链表的迭代代码
- [ ] 能解释快慢指针为什么能判环
- [ ] 能解释 dummy 节点解决了什么问题
- [ ] 能给相交链表讲清楚「为什么换路走能相遇」
- [ ] 能处理链表的边界情况（空、单节点、尾节点）

---

> 💪 **每日一句**：链表是算法的「积木」—— 画图、画图、画图！今天练熟了，明天树和图就轻松多了！
> 
> 📖 **明天预告**：Day 19 — 链表进阶！来学删除倒数第N个节点、旋转链表、奇偶链表、LRU缓存设计！

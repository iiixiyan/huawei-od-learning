# 📘 Day 19：链表进阶 — 从零到精通

> 🎯 **学习目标**：掌握链表进阶操作的四大核心套路——快慢指针、链表反转、链表拆分合并、深拷贝
> 📆 **第3周 · 第5天** | 栈·队列·链表专题
> 💡 **核心一句话**：链表进阶不过"找中点 + 反转 + 合并"三板斧的排列组合！

---

## 📑 目录

- [1. 基础知识：链表再认识](#1-基础知识链表再认识)
- [2. 常见操作大全](#2-常见操作大全)
- [3. 核心套路详解](#3-核心套路详解)
- [4. 高频题精讲](#4-高频题精讲)
- [5. 性能优化与常见坑](#5-性能优化与常见坑)
- [6. OD机考实战指南](#6-od机考实战指南)
- [7. 今日作业](#7-今日作业)

---

## 1. 基础知识：链表再认识

### 🧩 链表是什么？

想象一列**火车** 🚂——每一节车厢（节点）都连接着下一节车厢。你只能从车头走到车尾，不能跳着走，也不能从中间上车。

```
[数据|next] → [数据|next] → [数据|next] → null
```

### 🔄 链表 vs 数组

| 特性 | 数组 | 链表 |
|------|------|------|
| 🔍 随机访问 | O(1) — 直接下标取 | O(n) — 必须从头走 |
| ➕ 插入/删除 | O(n) — 要移动元素 | O(1) — 改指针就行 |
| 📦 内存 | 连续大块 | 零散分布 |
| 📏 长度 | 固定/动态 | 随时增长 |

> **小白问**：既然数组随机访问更快，为什么还要用链表？
> **答**：因为链表**插入和删除**超快！比如在中间插入一个元素，数组要搬动后面所有元素，链表只需改两个指针。OD机考的很多题目就是考你「只会数组不会链表」这个弱点 🎯

### 🧰 链表的基本积木

在进入进阶操作前，我们先回顾三个必须背熟的**基本积木**：

#### 积木①：遍历链表（"沿着铁轨走"）
```python
cur = head
while cur:        # 还没走到尽头
    print(cur.val) # 看看当前车厢
    cur = cur.next # 走向下一节
```

#### 积木②：哑节点（Dummy Node）🪄
```python
dummy = ListNode(0, head)  # 在head前面加一个假头
# 好处：不用单独处理头节点被删除的情况
return dummy.next
```

#### 积木③：定义链表节点
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

---

## 2. 常见操作大全

### 📋 链表进阶常用操作（全部带代码示例）

#### 操作1：快慢指针找中间节点 🐢🐇

```python
def find_middle(head: ListNode) -> ListNode:
    """快慢指针找中间节点（慢指针走1步，快指针走2步）"""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # 奇数个节点时返回正中间，偶数个时返回后半段第一个
```

> 💡 **图示**：
> ```
> head → ① → ② → ③ → ④ → ⑤ → null
>        🐢🐇 (起点)
>             🐢 → 🐇 (第1步)
>                  🐢 → → 🐇 (第2步 → fast到末尾, slow在③)
> ```

#### 操作2：找倒数第N个节点

```python
def find_nth_from_end(head: ListNode, n: int) -> ListNode:
    """快慢指针找倒数第N个节点"""
    dummy = ListNode(0, head)
    slow = fast = dummy
    for _ in range(n):     # 快指针先走n步
        fast = fast.next
    while fast.next:        # 然后一起走
        slow = slow.next
        fast = fast.next
    return slow.next        # 倒数第N个
```

#### 操作3：反转链表（迭代版）🔄

```python
def reverse_list(head: ListNode) -> ListNode:
    """反转整个链表"""
    prev = None
    cur = head
    while cur:
        nxt = cur.next    # ① 保存下一站
        cur.next = prev   # ② 掉头
        prev = cur        # ③ prev前进
        cur = nxt         # ④ cur前进
    return prev
```

> 💡 **记忆口诀**：先存后指，prev跟上，cur走起！

#### 操作4：拆分链表（奇偶分离）

```python
def split_odd_even(head: ListNode) -> (ListNode, ListNode):
    """将链表按奇偶索引分离，返回(奇数头, 偶数头)"""
    if not head or not head.next:
        return head, None
    odd = head
    even = head.next
    even_head = even
    while even and even.next:
        odd.next = even.next
        odd = odd.next
        even.next = odd.next
        even = even.next
    odd.next = None  # 断开奇数链表尾部
    return head, even_head
```

#### 操作5：合并两个链表（交叉合并）

```python
def merge_alternate(l1: ListNode, l2: ListNode) -> ListNode:
    """交叉合并两个链表：L1→L2→L1→L2..."""
    first, second = l1, l2
    while second:
        tmp1, tmp2 = first.next, second.next
        first.next = second
        second.next = tmp1
        first, second = tmp1, tmp2
    return l1
```

#### 操作6：在链表中插入副本节点

```python
def insert_copies(head: ListNode) -> None:
    """每个原节点后插入一个副本：A→A'→B→B'→..."""
    cur = head
    while cur:
        copy = ListNode(cur.val, cur.next)
        cur.next = copy
        cur = copy.next
```

---

## 3. 核心套路详解

### 🔥 套路一：快慢指针 🐢🐇 — "龟兔赛跑"定位法

**识别特征** 🔍：
- 题目涉及"删除倒数第N个"、"找中间节点"、"判断是否有环"
- 需要定位链表中某个特定位置

**核心思想** 💭：
让两个指针以不同速度移动，慢的每次走1步，快的每次走2步。快指针到达终点时，慢指针正好在中间位置。

**模板代码** 📝：

```python
# 🏆 快慢指针通用模板
def fast_slow_template(head: ListNode) -> ListNode:
    # 场景A：找中间节点（需要前驱 → 用dummy）
    dummy = ListNode(0, head)
    slow = fast = dummy
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    # 此时 slow 指向中间节点的前驱
    return slow
    
    # 场景B：找倒数第N个（前驱）
    # dummy = ListNode(0, head)
    # slow = fast = dummy
    # for _ in range(n):
    #     fast = fast.next
    # while fast.next:
    #     slow = slow.next
    #     fast = fast.next
    # return slow  # 倒数第N个的前驱
```

**适用范围** 🎯：
| 场景 | 模板变体 |
|------|---------|
| 删除倒数第N个 | 快指针先走N步，然后一起走 |
| 删除中间节点 | 用dummy作为慢指针起点，快指针从head开始 |
| 判断回文 | 快慢指针找中点 + 反转后半段 |
| 重排链表 | 快慢指针找中点 + 反转 + 合并 |

---

### 🔥 套路二：反转链表 🔄 — "掉头大法"

**识别特征** 🔍：
- 题目要求"反转"、"倒序"、"从后往前"
- 配合快慢指针找中点后使用

**核心思想** 💭：
逐个节点改变next指针方向，同时用prev保存已反转部分的头部。

**模板代码** 📝：

```python
# 🏆 反转链表模板（必须背到肌肉记忆 🧠）
def reverse_linked_list(head: ListNode) -> ListNode:
    prev = None
    cur = head
    while cur:
        nxt = cur.next    # ① 记下后路
        cur.next = prev   # ② 掉转枪头
        prev = cur        # ③ 更新prev
        cur = nxt         # ④ 前进
    return prev           # 新头
```

```python
# 🏆 寻找中点 + 反转后半段（高频组合拳 🤜🤛）
def split_and_reverse(head: ListNode) -> ListNode:
    """找到中点，断开链表，反转后半段"""
    if not head or not head.next:
        return head
    
    # ① 快慢指针找中点
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # ② 断开 & 反转后半段
    second = slow.next
    slow.next = None      # 断开！
    second = reverse_linked_list(second)
    
    return second  # 返回反转后的后半段头
```

---

### 🔥 套路三：拆分 + 合并 🔗 — "分而治之"万能法

**识别特征** 🔍：
- 重排链表、奇偶链表
- 需要"交错"、"交替"排列
- 复杂操作分解为"找中点→反转→合并"三步

**核心思想** 💭：
把一个复杂问题分解为**三个阶段**——每个阶段用上面背过的代码解决。

**模板代码** 📝：

```python
# 🏆 三步法模板（重排链表、回文链表都是它！）
def three_step_template(head: ListNode) -> None:
    """
    第一步：找中点（快慢指针）
    第二步：反转后半段
    第三步：合并两段
    """
    # Step 1: 找中点
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # Step 2: 断开 + 反转后半段
    second = slow.next
    slow.next = None
    prev = None
    while second:
        nxt = second.next
        second.next = prev
        prev = second
        second = nxt
    
    # Step 3: 合并
    first, second = head, prev
    while second:
        tmp1, tmp2 = first.next, second.next
        first.next = second
        second.next = tmp1
        first, second = tmp1, tmp2
```

> 💡 **链路进阶的万能钥匙** 🗝️：看到链表难题 → 拆解为"找中点（快慢指针）→ 反转（部分或全部）→ 合并/比较"三步骤。

---

### 🔥 套路四：哈希表辅助映射 🗺️ — "构建映射关系"

**识别特征** 🔍：
- 深拷贝链表（带random指针）
- 需要建立"旧节点→新节点"的映射关系

**核心思想** 💭：
用一个字典（哈希表）记录原节点 → 新节点的映射，第二次遍历时设置next和random。

**模板代码** 📝：

```python
# 🏆 哈希表法模板（深拷贝 — O(n)空间，但最容易理解）
def copy_with_hash(head: 'Node') -> 'Node':
    if not head:
        return None
    
    # ① 创建映射：旧节点 → 新节点
    old_to_new = {}
    cur = head
    while cur:
        old_to_new[cur] = Node(cur.val)
        cur = cur.next
    
    # ② 设置next和random
    cur = head
    while cur:
        old_to_new[cur].next = old_to_new.get(cur.next)
        old_to_new[cur].random = old_to_new.get(cur.random)
        cur = cur.next
    
    return old_to_new[head]
```

```python
# 🏆 O(1)空间法模板（深拷贝 — 更难但更优！）
def copy_with_insert(head: 'Node') -> 'Node':
    if not head:
        return None
    
    # ① 每个节点后插入副本
    cur = head
    while cur:
        new_node = Node(cur.val, cur.next, None)
        cur.next = new_node
        cur = new_node.next
    
    # ② 设置random指针
    cur = head
    while cur:
        if cur.random:
            cur.next.random = cur.random.next
        cur = cur.next.next
    
    # ③ 拆分：恢复原链表 + 提取新链表
    cur = head
    new_head = head.next
    while cur:
        new_node = cur.next
        cur.next = new_node.next
        new_node.next = new_node.next.next if new_node.next else None
        cur = cur.next
    
    return new_head
```

---

## 4. 高频题精讲

### 🏆 题1：删除链表的倒数第 N 个节点 ⭐⭐

> **来源**：[LeetCode 19](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)
> **难度**：中等 | **OD频率**：⭐⭐⭐⭐⭐

**📝 题目描述**：
给定链表 `1→2→3→4→5`，删除倒数第2个节点 → `1→2→3→5`。

**💡 解题思路**：
快慢指针 + 哑节点。快指针先走n步，然后快慢一起走。快指针到末尾时，慢指针在待删节点的前驱位置。

```
dummy → 1 → 2 → 3 → 4 → 5 → null
 🐢🐇 (一起出发)
 🐢 → → 🐇 (快先走2步)
      🐢 → → 🐇 (一起走..快到终点时慢在3，正是待删节点的前驱!)
```

**✅ 代码实现**：

```python
def removeNthFromEnd(head: ListNode, n: int) -> ListNode:
    dummy = ListNode(0, head)
    slow = fast = dummy
    
    # 快指针先走n步
    for _ in range(n):
        fast = fast.next
    
    # 一起走
    while fast.next:
        slow = slow.next
        fast = fast.next
    
    # 删除节点
    slow.next = slow.next.next
    return dummy.next
```

**⏱ 时空复杂度**：O(n) 时间 | O(1) 空间

**💣 注意坑**：
- 头节点可能被删 → 一定要用 **哑节点**！
- n 保证有效（1 ≤ n ≤ sz），所以不用判空

---

### 🏆 题2：重排链表 ⭐⭐⭐

> **来源**：[LeetCode 143](https://leetcode.cn/problems/reorder-list/)
> **难度**：中等 | **OD频率**：⭐⭐⭐⭐

**📝 题目描述**：
`1→2→3→4→5` → `1→5→2→4→3`（首尾交替排列）

**💡 解题思路**：
三步法：找中点 → 反转后半段 → 交叉合并

```
① 找中点:  1 → 2 → 3 → 4 → 5 → null
                       ↑
                     中点(slow)

② 反转后半: 1 → 2 → 3    5 → 4 → null(反转后)
                      ↑    ↑
                   前半尾  后半头

③ 合并:    1 → 5 → 2 → 4 → 3 → null
```

**✅ 代码实现**：

```python
def reorderList(head: ListNode) -> None:
    if not head or not head.next:
        return
    
    # 1. 找中点
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # 2. 反转后半段
    prev, cur = None, slow.next
    slow.next = None  # 切断前后
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    
    # 3. 合并
    first, second = head, prev
    while second:
        tmp1, tmp2 = first.next, second.next
        first.next = second
        second.next = tmp1
        first, second = tmp1, tmp2
```

**⏱ 时空复杂度**：O(n) 时间 | O(1) 空间

---

### 🏆 题3：回文链表 ⭐⭐

> **来源**：[LeetCode 234](https://leetcode.cn/problems/palindrome-linked-list/)
> **难度**：简单 | **OD频率**：⭐⭐⭐⭐

**📝 题目描述**：
判断链表是否回文：`1→2→2→1` → true | `1→2` → false

**💡 解题思路**：
找中点 → 反转后半段 → 比较前后段

```
1 → 2 → 2 → 1 → null
          ↑
        中点
反转后半: 1 → 2 → null   1 → 2 → null(反转后半)
         ↑              ↑
        left          right
比较: 1==1, 2==2 → True! ✅
```

**✅ 代码实现**：

```python
def isPalindrome(head: ListNode) -> bool:
    if not head or not head.next:
        return True
    
    # 找中点
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # 反转后半段
    prev = None
    while slow:
        nxt = slow.next
        slow.next = prev
        prev = slow
        slow = nxt
    
    # 比较
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left, right = left.next, right.next
    return True
```

**⏱ 时空复杂度**：O(n) 时间 | O(1) 空间

> **小白问**：为什么不用数组存起来反转比较？
> **答**：那样空间是O(n)！面试官会追问O(1)空间的方法，所以快慢指针+反转法是必考的。

---

### 🏆 题4：奇偶链表 ⭐⭐

> **来源**：[LeetCode 328](https://leetcode.cn/problems/odd-even-linked-list/)
> **难度**：中等 | **OD频率**：⭐⭐⭐

**📝 题目描述**：
将索引为奇数的节点和偶数节点分组，偶数接在奇数后面。
`1→2→3→4→5` → `1→3→5→2→4`

**💡 解题思路**：
分离奇数链表和偶数链表，最后拼接。

```
① 分离: 
  奇数链: 1 → 3 → 5
  偶数链: 2 → 4
② 拼接: 1 → 3 → 5 → 2 → 4
```

**✅ 代码实现**：

```python
def oddEvenList(head: ListNode) -> ListNode:
    if not head or not head.next:
        return head
    
    odd = head
    even = head.next
    even_head = even
    
    while even and even.next:
        odd.next = even.next
        odd = odd.next
        even.next = odd.next
        even = even.next
    
    odd.next = even_head  # 偶数链接到奇数链末尾
    return head
```

**⏱ 时空复杂度**：O(n) 时间 | O(1) 空间

---

### 🏆 题5：复制带随机指针的链表 ⭐⭐⭐

> **来源**：[LeetCode 138](https://leetcode.cn/problems/copy-list-with-random-pointer/)
> **难度**：中等 | **OD频率**：⭐⭐⭐

**📝 题目描述**：
深拷贝一个带random指针的链表，random可以指向任意节点或null。

**💡 解题思路**：
两种方法：
1. **哈希表法**（易理解）：旧→新映射，两次遍历
2. **插入法**（O(1)空间）：每个节点后插副本，三步走

**✅ 代码实现（O(1)空间法）**：

```python
def copyRandomList(head: 'Node') -> 'Node':
    if not head:
        return None
    
    # Step 1: A→A'→B→B'→...
    cur = head
    while cur:
        clone = Node(cur.val, cur.next, None)
        cur.next = clone
        cur = clone.next
    
    # Step 2: 设置random
    cur = head
    while cur:
        if cur.random:
            cur.next.random = cur.random.next
        cur = cur.next.next
    
    # Step 3: 拆分
    cur = head
    new_head = head.next
    while cur:
        clone = cur.next
        cur.next = clone.next
        clone.next = clone.next.next if clone.next else None
        cur = cur.next
    
    return new_head
```

**⏱ 时空复杂度**：O(n) 时间 | O(1) 空间（哈希表法O(n)空间）

> 💡 **面试官可能问**：为什么用插入法能做到O(1)空间？
> **答**：因为副本和原节点通过`next`指针建立了隐含映射关系——`原节点.next`就是它的副本，不需要额外的哈希表！

---

## 5. 性能优化与常见坑

### 🚫 常见坑（打死也别犯！）

#### 坑1：忘记处理head被删的情况
```python
# ❌ 错误：删头节点时，直接返回head报错
def removeNthFromEnd(head, n):
    slow = fast = head  # 没用dummy！
    # ...如果删除的是头节点，slow的前驱不存在

# ✅ 正确：用哑节点
dummy = ListNode(0, head)
slow = fast = dummy
```

#### 坑2：反转链表时丢失后续节点
```python
# ❌ 错误：cur.next被改写后，找不到下一个节点
cur.next = prev  # 改完之后cur后面的节点找不到了！
cur = cur.next   # 实际上原地踏步...

# ✅ 正确：先保存后路
nxt = cur.next
cur.next = prev
prev = cur
cur = nxt
```

#### 坑3：快慢指针while条件搞反
```python
# ❌ 错误：偶数个节点时空指针异常
while fast.next and fast:  # 先判fast.next再判fast？顺序错了！

# ✅ 正确：先判断fast本身，再判fast.next
while fast and fast.next:
```

#### 坑4：拆分链表后忘记断开尾部
```python
# ❌ 错误：奇数链尾部还连着偶数链
odd.next = even_head  # 但odd.next还指向even！形成环！

# ✅ 正确：
odd.next = None  # 先断开
odd.next = even_head
```

### ⚡ 性能优化技巧

| 场景 | 优化建议 |
|------|---------|
| 大量删除 | 用哑节点避免if-else判断头节点 |
| 多次反转 | 用迭代而不是递归（避免栈溢出） |
| 深拷贝 | O(1)空间插入法优于哈希表法 |
| 多次遍历 | 尽量一次遍历完成（如快慢指针） |

---

## 6. OD机考实战指南

### 🎯 出题特点

链表进阶题在OD机考中**必考**，但特点是：

1. **不直接考纯链表** — 往往是"披着其他外衣的链表题"
2. **偏好能O(1)空间解决的问题** — 考官看重空间意识
3. **组合题居多** — 两步操作嵌套（如找中点+反转）

### ⏰ 时间分配建议

| 难度 | 建议用时 | 策略 |
|------|---------|------|
| ⭐ 简单（回文链表） | 5-8分钟 | 手速题，直接套三步法模板 |
| ⭐⭐ 中等（删除倒N） | 8-12分钟 | 注意哑节点和边界 |
| ⭐⭐⭐ 中高（重排链表） | 12-18分钟 | 拆解为三步，逐段调试 |
| ⭐⭐⭐⭐ 困难（深拷贝） | 15-20分钟 | 先哈希法再优化为插入法 |

### 🧠 必背模板清单（考前10分钟过一遍）

```
1️⃣ 快慢指针模板          ← 链表80%题的起点
2️⃣ 反转链表模板          ← 链表60%题的核心
3️⃣ 合并链表模板          ← 三步法的最后一步
4️⃣ 插入副本模板          ← 深拷贝专用
```

---

## 7. 今日作业

### ⭐ 简单题

**T1**：[环形链表 II（L142）](https://leetcode.cn/problems/linked-list-cycle-ii/) — 找环入口
> 提示：快慢指针相遇后，再从头走一个指针，相遇即入口

### ⭐⭐ 中等题

**T2**：[对链表进行插入排序（L147）](https://leetcode.cn/problems/insertion-sort-list/) — 链表的插入排序
> 提示：每次从"已排序段"头部开始找插入位置

**T3**：[两数相加 II（L445）](https://leetcode.cn/problems/add-two-numbers-ii/) — 链表表示的数字相加（不能反转原链表）
> 提示：用栈反转顺序，或先反转链表

### ⭐⭐⭐ 进阶题

**T4**：[K 个一组翻转链表（L25）](https://leetcode.cn/problems/reverse-nodes-in-k-group/) — 链表版的"乾坤大挪移"
> 提示：递归 + 迭代反转，注意组内反转后与前后段的连接

**T5**：[扁平化多级双向链表（L430）](https://leetcode.cn/problems/flatten-a-multilevel-doubly-linked-list/) — 有孩子的链表怎么遍历？
> 提示：遇到有child的节点，DFS递归处理子链表

---

> 💪 **连续第19天打卡成功！** 链表的进阶操作并不难，关键是把「快慢指针」「反转链表」「合并链表」三块积木练成本能。当你看到一道题能条件反射地说出"先找中点，再反转，再合并"——你就真正入门了！明天树结构见！🌳

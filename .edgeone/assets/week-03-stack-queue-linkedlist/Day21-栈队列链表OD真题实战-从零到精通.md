# 📘 Day 21：栈·队列·链表 OD真题实战 — 从零到精通

> 🎯 **学习目标**：通过华为OD真题实战，熟练掌握栈、队列、链表三大数据结构的综合运用，能够快速识别题型并套用最优模板解题
> 📌 **OD备考**：第3周第7天（D15-D21）| 栈·队列·链表通关日
> 💡 **核心一句话**：栈解决「最近关联」问题，队列解决「层序/轮询」问题，链表解决「指针操作」问题 — 三者的组合出现在OD 100分/200分题中

---

## 📑 目录

- [一、本周核心套路回顾](#一本周核心套路回顾)
- [二、套路1：用栈模拟「后进先出」过程](#二套路1用栈模拟后进先出过程)
- [三、套路2：用队列实现「先进先出」+ 滑动窗口](#三套路2用队列实现先进先出--滑动窗口)
- [四、套路3：链表的「快慢指针 + 反转 + 合并」三板斧](#四套路3链表的快慢指针--反转--合并三板斧)
- [五、套路4：双数据结构组合题](#五套路4双数据结构组合题)
- [六、OD真题实战（5道高频题）](#六od真题实战5道高频题)
- [七、OD机考实战指南](#七od机考实战指南)
- [八、常见坑与避坑技巧](#八常见坑与避坑技巧)
- [九、今日作业](#九今日作业)

---

## 一、本周核心套路回顾

这一周我们学了栈、队列、链表三种线性数据结构，一起来回顾一下各自的「身份铭牌」：

```
┌─────────────────────────────────────────────────────┐
│  📦 栈 (Stack)    │  🚶 队列 (Queue)   │  🔗 链表 (LinkedList)│
├─────────────────────────────────────────────────────┤
│  LIFO后进先出     │  FIFO先进先出      │  动态内存分配        │
│  栈顶操作 O(1)    │  队首队尾 O(1)     │  插入删除 O(1)       │
│  适合:相邻消除    │  适合:滑动窗口     │  适合:任意位置修改    │
│  适合:括号匹配    │  适合:BFS层序      │  适合:大数运算       │
│  适合:单调栈      │  适合:轮询调度     │  适合:无法预知长度   │
└─────────────────────────────────────────────────────┘
```

OD考试中，这三者经常组合出现，比如「用队列实现栈」「用栈实现队列」「链表+栈模拟计算器」等。今天我们就来攻克这些组合题和真题！

---

## 二、套路1：用栈模拟「后进先出」过程

### 🎯 识别特征
- 出现「最近」「相邻」「成对」「回溯」等关键词
- 需要将元素暂存供后续处理
- 处理顺序与输入顺序相反

### 📋 模板代码

```python
# 栈的通用模板
def solve_with_stack(items):
    stack = []
    for item in items:
        if 需要出栈的条件:
            stack.pop()
        else:
            stack.append(item)
    return stack  # 或对stack进一步处理
```

### 🔥 OD高频变体

**变体1：相邻字符消除**
```
题目：给定字符串s，删除相邻且相同的字符对，反复操作直到无法删除
例：s = "abbaaca"
→ "abbaaca" → a和b不同，b和b相同→删除→"aaca"
→ "aaca" → a和a相同→删除→"ca" → 无法删除→返回"ca"
```

**变体2：有效括号的扩展**
```
题目：给定一个仅包含 '(' 和 ')' 的字符串，判断是否有效
扩展：加入 '*' 作为万能括号（可当 '(' 或 ')' 或空）
→ 用两个栈分别存 '(' 和 '*' 的下标
```

---

## 三、套路2：用队列实现「先进先出」+ 滑动窗口

### 🎯 识别特征
- 时间窗口内统计（如最近3000ms的请求数）
- 逐层处理（BFS二叉树层序遍历）
- 固定长度的滑动窗口最值

### 📋 模板代码

```python
from collections import deque

# 滑动窗口过期清理模板
def sliding_window(nums, window_size):
    q = deque()
    result = []
    for i, val in enumerate(nums):
        # 1. 入队
        q.append(val)
        # 2. 过期出队
        if len(q) > window_size:
            q.popleft()
        # 3. 窗口满时计算结果
        if i >= window_size - 1:
            result.append(处理窗口(q))
    return result

# BFS层序模板 (二叉树)
def bfs_level_order(root):
    if not root: return []
    q = deque([root])
    result = []
    while q:
        level_size = len(q)
        level_vals = []
        for _ in range(level_size):
            node = q.popleft()
            level_vals.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        result.append(level_vals)
    return result
```

### 🔥 OD高频变体：滑动窗口最大值

这道题在OD的100分题中反复出现！

```
输入: nums = [1,3,-1,-3,5,3,6,7], k = 3
输出: [3,3,5,5,6,7]
核心思路：单调递减双端队列，维护窗口内最大值候选
```

---

## 四、套路3：链表的「快慢指针 + 反转 + 合并」三板斧

### 🎯 识别特征
- 操作涉及「倒数第k个」「环」「中点」「反转」「合并」
- 要求 O(1) 空间复杂度
- 不允许修改节点值（必须改指针）

### 📋 核心代码（必须背诵）

**板斧1：快慢指针找中点**
```python
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # 中点（奇数长度正中间，偶数长度偏右）
```

**板斧2：反转链表（迭代版）**
```python
def reverse_list(head):
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
```

**板斧3：合并两个有序链表**
```python
def merge_two(l1, l2):
    dummy = cur = ListNode(0)
    while l1 and l2:
        if l1.val < l2.val:
            cur.next = l1
            l1 = l1.next
        else:
            cur.next = l2
            l2 = l2.next
        cur = cur.next
    cur.next = l1 if l1 else l2
    return dummy.next
```

### 🔥 三板斧组合使用「重排链表」

```
输入: 1 → 2 → 3 → 4 → 5
输出: 1 → 5 → 2 → 4 → 3

步骤：① 快慢指针找中点（断开）→ ② 反转后半段 → ③ 交替合并
```

---

## 五、套路4：双数据结构组合题

OD特别喜欢考「用X实现Y」的题目，下面是最经典的两种：

### 📦 套路4.1：用栈实现队列（L232）

```python
class MyQueue:
    def __init__(self):
        self.in_stack = []   # 入队用
        self.out_stack = []  # 出队用

    def push(self, x):
        self.in_stack.append(x)

    def pop(self):
        self.peek()
        return self.out_stack.pop()

    def peek(self):
        # 只有在out_stack为空时才倒数据
        if not self.out_stack:
            while self.in_stack:
                self.out_stack.append(self.in_stack.pop())
        return self.out_stack[-1]

    def empty(self):
        return not self.in_stack and not self.out_stack
```

> 💡 **关键技巧**：out_stack为空时才倒数据，避免打乱顺序。每个元素最多入栈2次、出栈2次，均摊 O(1)。

### 📦 套路4.2：用队列实现栈（L225）

```python
from collections import deque

class MyStack:
    def __init__(self):
        self.q = deque()

    def push(self, x):
        self.q.append(x)
        # 把新元素前面的都移到后面
        for _ in range(len(self.q) - 1):
            self.q.append(self.q.popleft())

    def pop(self):
        return self.q.popleft()

    def top(self):
        return self.q[0]

    def empty(self):
        return not self.q
```

> 💡 **关键技巧**：push时把新元素放到队首位置（通过旋转队列），保证栈顶在队首。

### 📦 套路4.3：链表 + 哈希表实现 LRU 缓存（L146）

OD 200分高频题！

```python
class DLinkedNode:
    def __init__(self, key=0, value=0):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = {}  # key → node
        self.capacity = capacity
        self.size = 0
        # 伪头尾节点
        self.head = DLinkedNode()
        self.tail = DLinkedNode()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove_node(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def _add_to_head(self, node):
        node.prev = self.head
        node.next = self.head.next
        self.head.next.prev = node
        self.head.next = node

    def _move_to_head(self, node):
        self._remove_node(node)
        self._add_to_head(node)

    def _pop_tail(self):
        node = self.tail.prev
        self._remove_node(node)
        return node

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self._move_to_head(node)  # 最近使用 → 移到头部
        return node.value

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            node = self.cache[key]
            node.value = value
            self._move_to_head(node)
        else:
            node = DLinkedNode(key, value)
            self.cache[key] = node
            self._add_to_head(node)
            self.size += 1
            if self.size > self.capacity:
                tail = self._pop_tail()
                del self.cache[tail.key]
                self.size -= 1
```

---

## 六、OD真题实战（5道高频题）

### 题1：有效的括号（OD 100分）⭐

**题目描述**：给定一个只包含 `'('`, `')'`, `'{'`, `'}'`, `'['`, `']'` 的字符串，判断字符串是否有效。

**解题思路**：经典栈匹配。遇到左括号入栈，遇到右括号检查栈顶是否匹配。

```python
def isValid(s: str) -> bool:
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in pairs:  # 右括号
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
        else:             # 左括号
            stack.append(ch)
    return not stack
```

**时空复杂度**：O(n) | O(n)

> 🐞 OD考试中可能变体为：检查括号的嵌套层次最大深度（用栈跟踪即可）

---

### 题2：逆波兰表达式求值（OD 100分）⭐⭐

**题目描述**：计算逆波兰表示的表达式值。运算符包括 `+`、`-`、`*`、`/`。

**解题思路**：数字入栈，遇到运算符弹出两个数字运算后入栈。

```python
def evalRPN(tokens: list[str]) -> int:
    stack = []
    for t in tokens:
        if t in {'+', '-', '*', '/'}:
            b, a = stack.pop(), stack.pop()
            if t == '+': stack.append(a + b)
            elif t == '-': stack.append(a - b)
            elif t == '*': stack.append(a * b)
            else: stack.append(int(a / b))  # 向零取整
        else:
            stack.append(int(t))
    return stack[0]
```

**时空复杂度**：O(n) | O(n)

> 🐞 **易错点**：`-` 和 `/` 的顺序！先弹出的是 b（右操作数），后弹出的是 a（左操作数），计算 a - b 和 a / b。

---

### 题3：滑动窗口最大值（OD 100分）⭐⭐⭐

**题目描述**：给定数组 nums 和滑动窗口大小 k，求出每个滑动窗口中的最大值。

**解题思路**：单调递减双端队列。队列存下标，保证队首永远是当前窗口最大值。

```python
from collections import deque

def maxSlidingWindow(nums: list[int], k: int) -> list[int]:
    q = deque()  # 存下标，值单调递减
    res = []
    for i, val in enumerate(nums):
        # 1. 移除队尾小于当前值的元素
        while q and nums[q[-1]] < val:
            q.pop()
        q.append(i)
        # 2. 移除过期元素（不在窗口内）
        if q[0] == i - k:
            q.popleft()
        # 3. 窗口满时记录结果
        if i >= k - 1:
            res.append(nums[q[0]])
    return res
```

**时空复杂度**：O(n) | O(k)

> 💡 **OD考试**：这道题被包装成「找连续k天的最高气温」「统计k秒内的最大并发连接数」等形式出现。

---

### 题4：环形链表 II（OD 100分）⭐⭐

**题目描述**：给定链表，返回环的入口节点。无环返回 null。

**解题思路**：快慢指针判环 → 相遇后从头同步走找入口。

```python
def detectCycle(head: ListNode) -> ListNode:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:  # 相遇
            slow = head
            while slow != fast:  # 找入口
                slow = slow.next
                fast = fast.next
            return slow
    return None
```

**时空复杂度**：O(n) | O(1)

> 🐞 **数学证明**：快指针走的路程是慢指针的2倍。相遇时，从head到入口的距离 = 相遇点到入口的距离。记住结论即可。

---

### 题5：LRU缓存（OD 200分）⭐⭐⭐

**题目描述**：设计 LRU（最近最少使用）缓存机制，实现 get 和 put 操作。

**解题思路**：哈希表 + 双向链表。哈希表 O(1) 查找，双向链表 O(1) 移动和删除。

（完整代码见上方套路4.3的模板）

**时空复杂度**：get / put 均为 O(1)

> 🔥 **OD 200分必考**！LRU是OD高频200分题，必须熟练默写。面试追问还可能问「如果让你实现 LFU 呢？」

---

## 七、OD机考实战指南

### 📊 本周主题出题特点

| 题目类型 | 出题频率 | 常见分值 | 备考建议 |
|---------|---------|---------|---------|
| 栈的基本操作（括号匹配等） | ⭐⭐⭐⭐⭐ | 100分 | 必拿题，送分 |
| 单调栈（每日温度、最大矩形） | ⭐⭐⭐⭐ | 100分 | 掌握模板 |
| 滑动窗口最大值 | ⭐⭐⭐⭐ | 100分 | 单调队列模板 |
| 链表基础（反转、合并、判环） | ⭐⭐⭐⭐⭐ | 100分 | 必拿题 |
| 链表进阶（重排、回文、深拷贝） | ⭐⭐⭐ | 100-200分 | 三件套组合 |
| LRU缓存 | ⭐⭐⭐⭐ | 200分 | 高频200分 |
| 用栈实现队列/用队列实现栈 | ⭐⭐⭐ | 100分 | 面试必问 |

### ⏰ 时间分配建议

```
OD考试总共150分钟，建议：
- 100分题（2道）：每题30-40分钟 = 60-80分钟
- 200分题（1道）：60-70分钟
- 检查调试：10-20分钟

对于栈/队列/链表题：
- 简单题（括号匹配/反转链表）：10-15分钟
- 中等题（单调栈/重排链表）：20-30分钟
- 难题（LRU/最大矩形）：30-45分钟
```

### 🎯 解题优先级策略

```
OD考试常见策略（按分值和时间）：
1️⃣ 先扫全部题目，确定题型难度
2️⃣ 优先做会做的100分题（拿保底分）
3️⃣ 再做200分题（冲高分）
4️⃣ 最后攻克不会的100分题

对于栈/队列/链表类：
  如果遇到「看不懂」的题 → 回头想是否可以用栈/队列模拟
  如果遇到「指针绕晕」的题 → 画图！
```

---

## 八、常见坑与避坑技巧

### 🕳️ 坑1：链表操作丢失引用

```python
# ❌ 错误写法
cur.next = cur.next.next  # 忘记保存next引用
# ✅ 正确写法
nxt = cur.next
cur.next = nxt.next
# 或者直接用dummy节点
```

### 🕳️ 坑2：单调栈内存储「下标」而非「值」

```python
# ❌ 错误：栈内存值，无法计算距离
stack = [73, 74, 75]  # 不知道每个值的下标
# ✅ 正确：栈内存下标
stack = [0, 1, 2]  # 知道每个值的下标，可以算距离
```

### 🕳️ 坑3：忘记考虑空栈

```python
# ❌ 危险写法
stack.pop()  # 空栈会抛异常
# ✅ 安全写法
if stack:
    stack.pop()
# 或用 stack 做布尔判断
```

### 🕳️ 坑4：队列 pop 的性能陷阱

```python
# ❌ 用 list 做队列
q = []
q.pop(0)   # O(n)，数据量大时超时！
# ✅ 用 collections.deque
from collections import deque
q = deque()
q.popleft()  # O(1)
```

### 🕳️ 坑5：反转链表后丢失head

```python
# ❌ 直接对head操作后找不到头了
cur = head
while cur:
    # 直接反转...
head = cur  # cur已经变成None！
# ✅ 保存新头部
new_head = reverse_list(head)
```

---

## 九、今日作业

### ⭐ 简单

**1. 用队列实现栈（L225）**
实现一个栈，仅使用两个队列作为底层数据结构。

**2. 删除链表的倒数第N个节点（L19）**
用快慢指针，一趟扫描完成删除。

### ⭐⭐ 中等

**3. 每日温度（L739）**
输入温度数组，返回每个温度需要等几天才能遇到更高的温度。单调栈经典题。

**4. 重排链表（L143）**
将链表 L0 → L1 → ... → Ln-1 → Ln 重排为 L0 → Ln → L1 → Ln-1 → ...

### ⭐⭐⭐ 进阶

**5. 柱状图中最大的矩形（L84）**
给定非负整数数组 heights，求柱状图能勾勒出的最大矩形面积。（单调栈，困难题！）

---

> 🎯 **今日总结**：栈·队列·链表是线性数据结构的三大支柱。OD机考中，栈和链表的出现率最高，队列常与滑动窗口/BFS一起出现。记住三个核心模板——**栈的进出逻辑**、**队列的窗口管理**、**链表的快慢指针/反转/合并三件套**，就能应对大部分考题。
>
> 💪 **Day 21完成！你已经完成了第3周的全程学习！下周我们将进入「二叉树·图」的全新世界！**

---

📝 **参考答案提示**
- 题1：push时旋转队列使新元素在队首
- 题2：快指针先走n步，然后一起走
- 题3：单调递减栈存下标
- 题4：找中点→反转后半→合并
- 题5：单调递增栈，末尾加0强制清空

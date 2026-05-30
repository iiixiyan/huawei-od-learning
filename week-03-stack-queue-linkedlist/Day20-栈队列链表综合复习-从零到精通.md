# 📘 Day 20：栈·队列·链表综合复习 — 从零到精通

> 🎯 **学习目标**：从零基础到熟练掌握三大基础数据结构的核心操作与高频题型  
> 📅 **OD备考**：第3周第6天 · 栈·队列·链表专项  
> 💡 **一句话总结**：栈是「先进后出」的反悔神器，队列是「先进先出」的排队工具，链表是「动态分配」的灵活结构 — 三者共同构成了数据结构的基础三件套！

---

## 📑 目录

- [一、本周知识总览](#一本周知识总览)
- [二、栈（Stack）核心回顾](#二栈stack核心回顾)
- [三、队列（Queue）核心回顾](#三队列queue核心回顾)
- [四、链表（Linked List）核心回顾](#四链表linked-list核心回顾)
- [五、四大核心套路（D20 四板斧）](#五四大核心套路d20-四板斧)
- [六、高频易错点 & 性能优化](#六高频易错点--性能优化)
- [七、OD机考实战指南](#七od机考实战指南)
- [八、综合限时测验（45分钟）](#八综合限时测验45分钟)
- [九、本周知识点脑图](#九本周知识点脑图)
- [十、今日作业](#十今日作业)

---

## 一、本周知识总览

| Day | 主题 | 核心内容 | 难度 |
|-----|------|---------|------|
| D15 | 栈基础 | 有效括号、最小栈、逆波兰表达式、小行星碰撞 | ⭐⭐ |
| D16 | 栈进阶 | 单调栈、字符串解码、柱状图最大矩形 | ⭐⭐⭐ |
| D17 | 队列+设计 | 队列实现、滑动窗口最大值、BFS层序遍历 | ⭐⭐⭐ |
| D18 | 链表基础 | 判环、反转、合并、两数相加 | ⭐⭐ |
| D19 | 链表进阶 | 重排、回文、深拷贝、删除倒N节点 | ⭐⭐⭐ |

**适合谁看？**
- 🐣 零基础小白：直接看 → 二、三、四 的基础概念回顾
- 💪 有基础选手：直接跳到 → 五、核心套路 + 八、限时测验

---

## 二、栈（Stack）核心回顾

### 2.1 什么是栈？🍔

> 栈就像一叠盘子 — 你总是先拿最上面的盘子（后放上去的），放盘子也总是放在最上面。

**核心特性**：**后进先出（LIFO, Last In First Out）**

```
   入栈(push)                 出栈(pop)
    ↓                          ↑
┌─────────┐                ┌─────────┐
│    5    │ ← 栈顶(top)    → 拿出 5  │
├─────────┤                ├─────────┤
│    3    │                │    3    │
├─────────┤                ├─────────┤
│    1    │ ← 栈底         │    1    │
└─────────┘                └─────────┘
```

### 2.2 基本操作

```python
# Python中用list模拟栈
stack = []

# 入栈 push
stack.append(5)      # [5]
stack.append(3)      # [5, 3]
stack.append(1)      # [5, 3, 1]

# 出栈 pop
top = stack.pop()    # top = 1, stack = [5, 3]

# 查看栈顶 peek
peek = stack[-1]     # peek = 3

# 判空
is_empty = len(stack) == 0   # False

# 大小
size = len(stack)     # 2
```

### 2.3 本周栈题型一览

| 题型 | 核心思路 | 模板代码 |
|------|---------|---------|
| 有效括号 ✅ | 左括号入栈，右括号匹配栈顶 | `stack.append()` / `stack.pop()` |
| 最小栈 📊 | 辅助栈同步存最小值 | `min_stack.append(min(val, min_stack[-1]))` |
| 逆波兰表达式 🧮 | 数字入栈，遇到运算符弹出计算 | 弹出两数 → 运算 → 结果入栈 |
| 小行星碰撞 💥 | 正数入栈，负数与栈顶碰撞消解 | `while stack and ...` 循环处理 |
| 单调栈 📈 | 维护单调递增/递减栈 | `while stack and nums[stack[-1]] < x: pop()` |
| 字符串解码 🔤 | 数字栈 + 字符串栈，遇到]弹出处理 | 双栈同步操作 |
| 柱状图最大矩形 📏 | 单调递增栈，出栈时计算面积 | `height * (cur - stack[-1] - 1)` |

---

## 三、队列（Queue）核心回顾

### 3.1 什么是队列？🎫

> 队列就像在奶茶店排队 — 先来的人先买到奶茶，后来的人排在后面。

**核心特性**：**先进先出（FIFO, First In First Out）**

```
  入队(enqueue)            出队(dequeue)
     ↓                        ↑
┌──────┬──────┬──────┬──────┐
│  1   │  2   │  3   │  4   │
└──────┴──────┴──────┴──────┘
 队头(front)             队尾(rear)
```

### 3.2 基本操作

```python
from collections import deque

# 创建队列
q = deque()

# 入队
q.append(1)        # deque([1])
q.append(2)        # deque([1, 2])
q.append(3)        # deque([1, 2, 3])

# 出队
front = q.popleft()     # front = 1, q = deque([2, 3])

# 查看队头
peek = q[0]             # peek = 2

# 大小
size = len(q)            # 2
```

### 3.3 队列的三种变形

```
普通队列   ← 先进先出
   ↓
双端队列(deque)  ← 两端都可进出（Python默认推荐）
   ↓
优先队列(heapq)  ← 按优先级出队（底层是堆）
```

### 3.4 本周队列题型一览

| 题型 | 核心思路 |
|------|---------|
| 滑动窗口最大值 | 单调队列：维护递减队列，队头即最大值 |
| BFS层序遍历 | 每层入队，size控制一次处理一层 |
| CBT插入器 | 队列存可插入节点，BFS查找空位 |
| 循环队列 | 数组模拟，front/rear指针循环移动 |

---

## 四、链表（Linked List）核心回顾

### 4.1 什么是链表？⛓️

> 链表就像寻宝游戏 — 每张纸条上写着下一个藏宝地点的地址，你需要顺着纸条一个一个找下去。

**核心结构**：
```
head → [val|next] → [val|next] → [val|next] → None
```

**对比数组**：

| 对比项 | 数组 | 链表 |
|-------|------|------|
| 内存分配 | 连续 | 分散 |
| 随机访问 | O(1) | O(n) |
| 插入/删除 | O(n)（要移动元素） | O(1)（改指针） |
| 大小 | 固定/动态扩容 | 动态 |

### 4.2 常用操作模板

```python
# 定义链表节点
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# 遍历链表
cur = head
while cur:
    print(cur.val)
    cur = cur.next

# ⭐ 反转链表（必背！）
def reverseList(head):
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
```

### 4.3 本周链表题型一览

| 题型 | 核心技巧 | 链表心法 |
|------|---------|---------|
| 环形链表 | 快慢指针 | 快走2慢走1，相遇即有环 |
| 环入口 | 快慢指针 + 同步走 | 相遇后slow回head，同步走到相遇 |
| 相交链表 | 双指针走等长路 | A走完走B，B走完走A，相遇即交点 |
| 反转链表 | 三指针迭代 | prev/cur/nxt 三指针推进 |
| 合并有序链表 | dummy + 双指针 | 谁小接谁，剩余全接 |
| 两数相加 | 遍历+进位 | carry = sum // 10 |
| 删除倒N节点 | 快慢指针 | 快先走n步，同步走 |
| 重排链表 | 找中点→反转→合并 | 三步法！ |
| 回文链表 | 找中点→反转→比较 | 先反转再比 |
| 深拷贝 | 哈希表/原地复制 | 先复制节点，再处理random |

---

## 五、四大核心套路（D20 四板斧）

### 🪓 套路一：快慢指针法

**识别特征**：涉及链表环、中点、倒数第N等"位置定位"问题

**核心思想**：快指针一次走2步，慢指针一次走1步

**模板代码**：
```python
def solve_with_fast_slow(head):
    """快慢指针通用模板"""
    slow = fast = head
    while fast and fast.next:
        slow = slow.next      # 慢指针走1步
        fast = fast.next.next # 快指针走2步
        if slow == fast:
            # 情况1：寻找环入口
            # slow = head
            # while slow != fast:
            #     slow = slow.next
            #     fast = fast.next
            # return slow
            pass
    # 此时slow在中间位置（用于找中点、回文比较等）
    return slow
```

**适用范围**：✅ 环检测 ✅ 环入口 ✅ 找中点 ✅ 删除倒数第N ✅ 回文链表

---

### 🪓 套路二：栈的匹配与消消乐

**识别特征**：需要「回头看」最近元素 — 括号匹配、表达式求值、碰撞问题

**核心思想**：利用栈的LIFO特性保存"历史"，遇到匹配条件就弹出

**模板代码**：
```python
def solve_with_stack(items):
    """栈的匹配/消解通用模板"""
    stack = []
    for item in items:
        if is_left_or_push(item):        # 左括号 / 入栈条件
            stack.append(item)
        else:                            # 右括号 / 出栈条件
            if stack and matches(stack[-1], item):
                stack.pop()              # 匹配成功！
            else:
                return False             # 匹配失败
    return len(stack) == 0               # 全部匹配
```

**适用范围**：✅ 有效括号 ✅ 小行星碰撞 ✅ 逆波兰表达式 ✅ 字符串解码

---

### 🪓 套路三：单调栈（Monotonic Stack）

**识别特征**：找下一个更大/更小元素、滑动窗口极值、柱状图面积

**核心思想**：维护栈内元素单调递增或递减，违反单调性就弹出

**模板代码**：
```python
def monotonic_stack(nums):
    """单调递增栈 — 找下一个更大元素"""
    stack = []       # 存索引
    result = [-1] * len(nums)
    
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:   # 违反单调性
            idx = stack.pop()
            result[idx] = x                     # 下一个更大元素
        stack.append(i)
    
    return result

def max_sliding_window(nums, k):
    """滑动窗口最大值 — 单调递减队列"""
    from collections import deque
    q = deque()       # 存索引，保证递减
    result = []
    
    for i, x in enumerate(nums):
        # 维护单调递减
        while q and nums[q[-1]] < x:
            q.pop()
        q.append(i)
        
        # 移除过期元素
        if q[0] <= i - k:
            q.popleft()
        
        # 记录结果
        if i >= k - 1:
            result.append(nums[q[0]])
    
    return result
```

**适用范围**：✅ 每日温度 ✅ 柱状图最大矩形 ✅ 滑动窗口最大值 ✅ 接雨水

---

### 🪓 套路四：链表三步法

**识别特征**：重排链表、回文链表、反转部分链表等"重组"问题

**核心思想**：分解为 找中点 + 反转 + 合并 三个子操作

**模板代码**：
```python
def linked_list_three_step(head):
    if not head or not head.next:
        return head
    
    # 第1步：找中点（快慢指针）
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # 第2步：反转后半段
    prev, cur = None, slow.next
    slow.next = None       # 断开前后段
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    
    # 第3步：合并
    first, second = head, prev
    # ... 根据题目要求合并
    return head
```

**适用范围**：✅ 重排链表 ✅ 回文链表 ✅ 奇偶链表

---

## 六、高频易错点 & 性能优化

### ❌ 易错点TOP 5

**1. 链表指针丢失**
```python
# ❌ 错误：修改next前忘记保存下一个节点
cur.next = prev  # cur.next被覆盖，下一个节点找不到了

# ✅ 正确：先保存再修改
nxt = cur.next
cur.next = prev
prev, cur = cur, nxt
```

**2. 栈空时pop**
```python
# ❌ 错误：没检查栈是否为空就pop
stack.pop()

# ✅ 正确：先检查
if stack:
    stack.pop()
elif stack[-1] == target:  # Python的list[-1]在空列表时会抛IndexError
    pass
```

**3. 链表删除时不使用dummy头**

> 当头节点可能被删除时（如删除倒数第N个），**一定要用dummy节点**！

```python
# ✅ 好习惯：dummy节点
dummy = ListNode(0, head)
slow = fast = dummy
# ... 操作
return dummy.next  # 永远返回新的头
```

**4. 单调栈存值vs存索引**

```python
# ❌ 存值：丢失了位置信息，没法计算距离/宽度
stack = [val1, val2, val3]

# ✅ 存索引：通过索引可以访问值和位置
stack = [0, 2, 5]  # 存下标
nums[stack[-1]]    # 获取值
i - stack[-1]      # 计算距离
```

**5. 队列BFS忘记处理每层大小**

```python
# ❌ 错误：无法分层
while q:
    node = q.popleft()
    for child in node.children:
        q.append(child)

# ✅ 正确：size控制每层
while q:
    size = len(q)          # 当前层的节点数
    for _ in range(size):
        node = q.popleft()
        # 处理当前层...
        for child in node.children:
            q.append(child)
```

### ⚡ 性能优化

| 场景 | 优化前 | 优化后 |
|------|-------|-------|
| 频繁pop(0) | O(n²) | ✅ 用deque的popleft() → O(1) |
| 多次反转链表 | O(n²) | ✅ 一次反转 → O(n) |
| 哈希表实现深拷贝 | O(n)空间 | ✅ 原地复制 → O(1)空间 |
| 两重循环找极值 | O(n²) | ✅ 单调栈 → O(n) |

---

## 七、OD机考实战指南

### 🎯 出题特点

| 数据结构 | OD机考出现频率 | 典型题型 |
|---------|--------------|---------|
| 栈 | ⭐⭐⭐⭐⭐ 极高 | 括号匹配、表达式计算、单调栈 |
| 队列 | ⭐⭐⭐⭐ 高 | 滑动窗口、BFS、任务调度 |
| 链表 | ⭐⭐⭐ 中 | 反转、环检测、合并有序链表 |

### ⏱ 时间分配建议

```
题目阅读 + 思路分析：3-5分钟
基础代码编写：5-8分钟
边界处理（空值/单节点）：2分钟
测试用例验证：2分钟
----------------------------
单题总时间：15分钟以内
```

### 🚨 OD常见坑

1. **输入处理**：OD机考常用 `input().split()` 读取，注意空行
2. **链表定义**：OD机考可能不给ListNode定义，需要自己写
3. **栈用list就够了**：不需要额外引入，Python的list就是最好的栈
4. **超时排查**：如果超时，先检查是不是用了O(n²)而不是O(n)的单调栈

---

## 八、综合限时测验（45分钟）

### 📝 题目1：有效的括号（⭐⭐ 5分钟）

**题目**：给定一个只包括 `'('`, `')'`, `'{'`, `'}'`, `'['`, `']'` 的字符串 s，判断字符串是否有效。

```python
def isValid(s: str) -> bool:
    # 你的代码
    pass
```

<details>
<summary>📖 查看答案</summary>

```python
def isValid(s: str) -> bool:
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in pairs:          # 右括号
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
        else:                     # 左括号
            stack.append(ch)
    return not stack
```
</details>

---

### 📝 题目2：下一个更大元素 — 单调栈（⭐⭐⭐ 8分钟）

**题目**：给定一个数组 nums，返回每个元素的下一个更大元素（右侧第一个更大的值），如果没有则返回-1。

```python
def nextGreaterElement(nums):
    # 你的代码
    pass
```

<details>
<summary>📖 查看答案</summary>

```python
def nextGreaterElement(nums):
    n = len(nums)
    result = [-1] * n
    stack = []       # 存索引，维持递减
    for i in range(n):
        while stack and nums[stack[-1]] < nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]
        stack.append(i)
    return result
```
</details>

---

### 📝 题目3：反转链表（⭐⭐ 5分钟）

**题目**：反转一个单链表，返回反转后的链表头节点。

```python
def reverseList(head):
    # 你的代码
    pass
```

<details>
<summary>📖 查看答案</summary>

```python
def reverseList(head):
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    return prev
```
</details>

---

### 📝 题目4：滑动窗口最大值（⭐⭐⭐⭐ 12分钟）

**题目**：给你一个整数数组 nums，有一个大小为 k 的滑动窗口从最左移到最右。返回每个窗口中的最大值。

```python
def maxSlidingWindow(nums, k):
    # 你的代码
    pass
```

<details>
<summary>📖 查看答案</summary>

```python
from collections import deque

def maxSlidingWindow(nums, k):
    q = deque()       # 存索引，单调递减
    result = []
    
    for i, x in enumerate(nums):
        # 1. 维护递减：弹出比x小的
        while q and nums[q[-1]] < x:
            q.pop()
        q.append(i)
        
        # 2. 移除窗口外的
        if q[0] <= i - k:
            q.popleft()
        
        # 3. 记录窗口最大值
        if i >= k - 1:
            result.append(nums[q[0]])
    
    return result
```
</details>

---

### 📝 题目5：重排链表（⭐⭐⭐ 15分钟）

**题目**：给定单链表 L: L0→L1→…→Ln-1→Ln，重排为 L0→Ln→L1→Ln-1→L2→Ln-2→…

```python
def reorderList(head):
    # 你的代码
    pass
```

<details>
<summary>📖 查看答案</summary>

```python
def reorderList(head):
    if not head or not head.next:
        return
    
    # 1. 找中点
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # 2. 反转后半段
    prev, cur = None, slow.next
    slow.next = None          # 断开
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
</details>

---

### 📊 错误复盘表

| 题目 | 是否独立AC | 卡点 | 改进方向 |
|-----|-----------|------|---------|
| 有效括号 | □ 是 □ 否 | | |
| 下一个更大元素 | □ 是 □ 否 | | |
| 反转链表 | □ 是 □ 否 | | |
| 滑动窗口最大值 | □ 是 □ 否 | | |
| 重排链表 | □ 是 □ 否 | | |

---

## 九、本周知识点脑图

```
                 第3周：栈·队列·链表
                        │
        ┌───────────────┼───────────────┐
        │               │               │
       栈              队列             链表
        │               │               │
  ┌─────┴─────┐    ┌───┴───┐     ┌─────┴─────┐
  │           │    │       │     │           │
 基础       进阶  普通队列  链表基础  链表进阶
  │           │    deque   │     │           │
有效括号   单调栈    │    判环   重排链表
最小栈   字符串解码  BFS   反转   回文链表
逆波兰   柱状图最大 滑动窗口 合并   深拷贝
小行星碰撞 矩形    任务调度 两数相加 删除节点
```

### 💡 一句话心法

| 数据结构 | 心法口诀 |
|---------|---------|
| 栈 | 后进先出，括号匹配用栈，回看历史用栈 |
| 队列 | 先进先出，BFS用队列，滑动窗口用单调队列 |
| 链表 | dummy防头删，快慢找中点，反转连招三步走 |

---

## 十、今日作业

### ⭐ 简单（2题）

**1. 用队列实现栈** [L225](https://leetcode.cn/problems/implement-stack-using-queues/)
- 考验对栈和队列本质特性的理解

**2. 链表的中间节点** [L876](https://leetcode.cn/problems/middle-of-the-linked-list/)
- 快慢指针基础应用

### ⭐⭐ 中等（3题）

**3. 每日温度** [L739](https://leetcode.cn/problems/daily-temperatures/)
- 单调栈经典入门题

**4. 删除排序链表中的重复元素II** [L82](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/)
- 链表删除+dummy节点的综合应用

**5. 简化路径** [L71](https://leetcode.cn/problems/simplify-path/)
- 栈处理字符串的典型应用

### ⭐⭐⭐ 进阶（2题 — 选做）

**6. 合并K个升序链表** [L23](https://leetcode.cn/problems/merge-k-sorted-lists/)
- 分治/优先队列的经典应用

**7. 滑动窗口最大值** [L239](https://leetcode.cn/problems/sliding-window-maximum/)
- OD高频题，必须掌握单调队列

---

> 💪 **Week 3 核心收获**：
> 1. 栈是「反悔/回溯」的好帮手，括号、表达式、单调栈三板斧
> 2. 队列的FIFO特性 + 单调队列 = 滑动窗口大杀器
> 3. 链表的反转+快慢指针+三步法，简单变复杂，复杂变简单
>
> 📣 **Day 21预告**：本周最后一天，OD 100分栈队列链表实际考题演练！
>
> 🎉 **第3周第6天，连续20天打卡成功！毅力已经远超99%的人！** 💪🔥

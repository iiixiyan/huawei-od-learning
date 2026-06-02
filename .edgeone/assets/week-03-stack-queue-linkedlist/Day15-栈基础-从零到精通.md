# 📘 Day 15：栈基础 — 从零到精通

> 🎯 **学习目标**：掌握栈的「后进先出 (LIFO)」核心特性，能熟练运用栈解决括号匹配、路径简化、表达式求值、相邻消除、碰撞模拟等六大经典问题
> 📅 **华为OD备考 · 第3周 · 栈·队列·链表** | 难度：⭐⭐⭐ | 6道经典题
> 💡 **核心心法一句话**：遇到「最近关联」「成对消除」「路径回溯」「状态撤销」——立刻想栈！

---

## 📖 目录

- [一、栈是什么？](#一栈是什么)
- [二、Python中栈的实现](#二python中栈的实现)
- [三、栈的常见操作大全](#三栈的常见操作大全)
- [四、6大核心套路详解](#四6大核心套路详解)
  - [套路1：括号匹配](#套路1括号匹配)
  - [套路2：路径/状态模拟](#套路2路径状态模拟)
  - [套路3：辅助栈 — 保持额外信息](#套路3辅助栈--保持额外信息)
  - [套路4：表达式求值](#套路4表达式求值)
  - [套路5：相邻消除](#套路5相邻消除)
  - [套路6：带条件的碰撞/吞噬](#套路6带条件的碰撞吞噬)
- [五、高频题精讲](#五高频题精讲)
- [六、性能优化与常见坑](#六性能优化与常见坑)
- [七、OD机考实战指南](#七od机考实战指南)
- [八、今日作业](#八今日作业)

---

## 一、栈是什么？

### 🥞 生活类比：叠盘子

想象你在餐厅后厨叠盘子：

- 你只能从**最上面**拿走盘子（👆 取走最新叠上去的）
- 你只能把新盘子叠在**最上面**（👆 新盘子永远在顶部）
- 你**不能直接从中间抽**一个盘子出来
- 最先叠的盘子反而被压在**最底下**，最后才能拿到

这就是栈！**后进先出**（LIFO — Last In, First Out）。

```
    push('🍕')          push('🍔')          push('🌭')
    ┌─────┐            ┌─────┐            ┌─────┐
    │     │            │     │            │ 🌭  │ ← 栈顶（最新）
    │     │   →        │     │   →        │ 🍔  │
    │     │            │ 🍕  │            │ 🍕  │ ← 栈底（最旧）
    └─────┘            └─────┘            └─────┘

    pop() → 🌭         pop() → 🍔         pop() → 🍕
          ↑ 后进先出！        ↑ 后进先出！        ↑ 后进先出！
```

### 🤔 为什么需要栈？

在编程中有很多场景天然就是「后进先出」的：

| 场景 | 为什么是 LIFO | 例子 |
|------|-------------|------|
| 括号匹配 | 最后打开的括号必须最先关闭 | `{[()]}` → 先关`()`再关`[]`最后关`{}` |
| 撤销操作 | 撤销的是「最近一次」操作 | Ctrl+Z |
| 函数调用 | 最后调用的函数最先返回 | 递归、调用栈 |
| 路径回溯 | 进入目录是入栈，`..`退回是出栈 | `/a/b/../c` |
| HTML标签 | 最后打开的标签最先闭合 | `<div><p>text</p></div>` |

### 📊 栈的三大基本操作

```
操作          描述                    时间复杂度    类比
─────────────────────────────────────────────────────────
push(x)       把 x 放到栈顶            O(1)        叠一个盘子
pop()         拿走栈顶元素并返回         O(1)        取走最上面的盘子
peek()/top()  看一眼栈顶元素（不拿走）    O(1)        瞄一眼最上面的盘子
isEmpty()     判断栈是否为空            O(1)        盘子堆空了没？
```

> ⚠️ **重要**：栈的所有基本操作都是 O(1)！这就是为什么它这么高效。

---

## 二、Python中栈的实现

Python 没有内置的「栈」类，但 `list` 完全满足栈的需求：

```python
# 方式1：直接用 list（推荐，最简单）
stack = []
stack.append(1)    # push → [1]
stack.append(2)    # push → [1, 2]
stack.append(3)    # push → [1, 2, 3]
top = stack[-1]    # peek → 3
val = stack.pop()  # pop → 3，栈变为 [1, 2]
len(stack) == 0    # isEmpty → False

# 方式2：collections.deque（性能更好但不必要）
from collections import deque
stack = deque()
stack.append(1)
stack.pop()

# 方式3：用类封装（OD考试不需要，但工程中推荐）
class Stack:
    def __init__(self):
        self._data = []

    def push(self, x):
        self._data.append(x)

    def pop(self):
        return self._data.pop()

    def peek(self):
        return self._data[-1]

    def is_empty(self):
        return len(self._data) == 0

    def size(self):
        return len(self._data)
```

### 🔑 关键要点

- **`list.append()` 就是 push，`list.pop()` 就是 pop**
- **`list[-1]` 就是 peek**（看一眼栈顶）
- Python的list尾部操作都是 O(1)，非常高效
- **不要**用 `list.insert(0, x)` 和 `list.pop(0)` 来模拟栈 — 那是 O(n)！

---

## 三、栈的常见操作大全

| 操作 | Python写法 | 说明 | 示例 | 结果 |
|------|-----------|------|------|------|
| 入栈（push） | `stack.append(x)` | 元素加到末尾 | `stack.append(5)` | `[1, 2, 5]` |
| 出栈（pop） | `stack.pop()` | 移除并返回末尾元素 | `val = stack.pop()` | `val=5`，剩下`[1, 2]` |
| 查看栈顶（peek） | `stack[-1]` | 只看不移除 | `top = stack[-1]` | `top=2` |
| 判空 | `len(stack) == 0` | 或 `not stack` | `if not stack:` | `True/False` |
| 获取大小 | `len(stack)` | 元素个数 | `size = len(stack)` | `2` |
| 清空 | `stack.clear()` | 删除全部 | `stack.clear()` | `[]` |
| 安全出栈 | `stack.pop() if stack else None` | 先判空再pop | — | 不会报错 |
| 转字符串 | `''.join(stack)` | 字符栈→字符串 | `''.join(['a','b'])` | `'ab'` |
| 检查栈顶是否匹配 | `stack and stack[-1] == target` | 短路判空+比较 | — | `True/False` |

> 💡 **小技巧**：`not stack` 判空比 `len(stack) == 0` 更 Pythonic，建议使用。

### 🚨 小白常见错误

```python
stack = []

# ❌ 错误1：空栈时 pop 会报错！
stack.pop()         # IndexError: pop from empty list

# ✅ 正确：先判断
if stack:
    stack.pop()

# ❌ 错误2：空栈时访问 [-1] 也会报错！
top = stack[-1]     # IndexError

# ✅ 正确：先判断
top = stack[-1] if stack else None
```

---

## 四、6大核心套路详解

栈的题目虽然千变万化，但归纳起来就以下 **6大套路**。掌握模板，刷题事半功倍！

### 套路1：括号匹配

> **识别特征**：成对的符号需要配对，且配对关系有嵌套结构。
> **核心思路**：左符号入栈，右符号与栈顶匹配。

#### 🎯 模板代码

```python
def bracket_match(s: str) -> bool:
    """
    括号匹配通用模板
    适用范围：括号配对、HTML标签匹配、括号嵌套合法性
    """
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}  # 右→左的映射

    for ch in s:
        if ch in pairs:                      # 遇到右括号
            if not stack or stack[-1] != pairs[ch]:
                return False                 # 不匹配 → 直接返回
            stack.pop()                      # 匹配 → 弹出左括号
        else:                                # 遇到左括号
            stack.append(ch)                 # 入栈

    return not stack                         # 栈空才合法
```

#### 🔍 手把手推演

以 `s = "([])"` 为例：

```
步骤  ch   栈内容      操作            说明
─────────────────────────────────────────────────
初始        []                         空栈
 1    (     [(]         push '('       左括号入栈
 2    [     [(, []      push '['       左括号入栈
 3    ]     [(]         pop → '['       ']'匹配栈顶'['，弹出
 4    )     []          pop → '('       ')'匹配栈顶'('，弹出
结束        []          not stack=True  ✅ 有效！
```

以 `s = "([)]"` 为例：

```
步骤  ch   栈内容      操作            说明
─────────────────────────────────────────────────
初始        []                         空栈
 1    (     [(]         push '('       左括号入栈
 2    [     [(, []      push '['       左括号入栈
 3    )     [(, []      检查栈顶        ')'期望匹配'('，但栈顶是'['！
                                       不匹配 → return False ❌
```

#### 📌 适用范围

- ✅ 有效括号（`()` `[]` `{}`）
- ✅ 最长有效括号
- ✅ 括号生成
- ✅ 删除无效括号
- ✅ HTML/XML标签合法性检查

---

### 套路2：路径/状态模拟

> **识别特征**：按某种规则处理字符串或序列，遇到特定符号执行「前进」or「后退」。
> **核心思路**：正常元素入栈，特殊符号触发 pop。

#### 🎯 模板代码

```python
def path_simulate(path: str) -> str:
    """
    路径/状态模拟通用模板
    适用范围：简化路径、后退操作、撤销操作、目录导航
    """
    stack = []

    for part in path.split('/'):   # 按分隔符拆分
        if part in ('', '.'):      # 空串或当前目录 → 忽略
            continue
        if part == '..':           # 返回上一级 → 出栈
            if stack:
                stack.pop()
        else:                      # 有效目录 → 入栈
            stack.append(part)

    return '/' + '/'.join(stack)   # 拼接结果
```

#### 🔍 手把手推演

以 `path = "/home/user/Documents/../Pictures"` 为例：

```
步骤  part        栈内容                    操作
─────────────────────────────────────────────────────
初始              []
 1    ''(空)      []                        忽略
 2    'home'      ['home']                  push
 3    'user'      ['home', 'user']          push
 4    'Documents' ['home', 'user', 'Documents'] push
 5    '..'        ['home', 'user']          pop 'Documents'
 6    ''(空)      ['home', 'user']          忽略
 7    'Pictures'  ['home', 'user', 'Pictures'] push

结果: '/home/user/Pictures' ✅
```

#### 📌 适用范围

- ✅ 简化路径（L71）
- ✅ 后退按钮/浏览器历史
- ✅ 撤销重做（undo/redo）
- ✅ 文件系统导航

---

### 套路3：辅助栈 — 保持额外信息

> **识别特征**：需要在 O(1) 时间内获取栈的某种「统计信息」（最小值、最大值、频率等）。
> **核心思路**：维护一个「辅助栈」，与主栈同步更新，保存每个时刻的统计信息。

#### 🎯 模板代码 — 最小栈

```python
class MinStack:
    """O(1)时间获取最小值的栈"""
    def __init__(self):
        self.stack = []      # 主栈：存所有元素
        self.min_stack = []  # 辅助栈：存每个时刻的最小值

    def push(self, val: int) -> None:
        self.stack.append(val)
        # 如果辅助栈为空，或新值 ≤ 当前最小值 → 入辅助栈
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self) -> None:
        val = self.stack.pop()
        # 如果弹出的值恰好是最小值 → 辅助栈也弹出
        if val == self.min_stack[-1]:
            self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

#### 🔍 手把手推演

```
操作        stack        min_stack    说明
───────────────────────────────────────────────
push(-2)    [-2]         [-2]         第一个元素，最小值=-2
push(0)     [-2, 0]      [-2]         0 > -2，不入min_stack
push(-3)    [-2, 0, -3]  [-2, -3]     -3 <= -2，入min_stack
getMin()    → -3                      栈顶=-3 → 返回-3 ✅
pop()       [-2, 0]      [-2]         弹出-3，min_stack也弹出
top()       → 0                       栈顶=0
getMin()    → -2                      栈顶=-2 → 返回-2 ✅
```

> 💡 **为什么是 `≤` 而不是 `<`**？如果有重复的最小值，两个都要入辅助栈，否则 pop 后会丢失。

#### 🎯 扩展：最大频率栈模板

```python
class FreqStack:
    """O(1)时间弹出频率最高的元素"""
    def __init__(self):
        self.freq = {}           # 元素→频率
        self.group = {}          # 频率→该频率的元素栈
        self.max_freq = 0

    def push(self, val: int) -> None:
        self.freq[val] = self.freq.get(val, 0) + 1
        f = self.freq[val]
        if f not in self.group:
            self.group[f] = []
        self.group[f].append(val)
        self.max_freq = max(self.max_freq, f)

    def pop(self) -> int:
        val = self.group[self.max_freq].pop()
        self.freq[val] -= 1
        if not self.group[self.max_freq]:
            self.max_freq -= 1
        return val
```

#### 📌 适用范围

- ✅ 最小栈（L155）
- ✅ 最大频率栈（L895）
- ✅ 包含min函数的栈（剑指Offer 30）
- ✅ 队列的最大值（剑指Offer 59-II）

---

### 套路4：表达式求值

> **识别特征**：给定一个数学表达式（中缀/后缀），求值。
> **核心思路**：数字入栈，遇到运算符弹出两个操作数计算后结果入栈。

#### 🎯 模板代码 — 逆波兰表达式

```python
def evalRPN(tokens: list[str]) -> int:
    """
    逆波兰表达式（后缀表达式）求值
    特点：运算符在两个操作数之后，天然适合栈
    适用范围：后缀/前缀表达式求值、计算器实现
    """
    stack = []
    ops = {'+', '-', '*', '/'}

    for t in tokens:
        if t in ops:
            b = stack.pop()    # 第二个操作数（后弹出的）
            a = stack.pop()    # 第一个操作数（先弹出的）
            if t == '+':
                stack.append(a + b)
            elif t == '-':
                stack.append(a - b)    # ⚠️ 注意顺序：a - b
            elif t == '*':
                stack.append(a * b)
            else:
                # 向零取整：int(a/b) 在 Python3 中就是向零截断
                stack.append(int(a / b))
        else:
            stack.append(int(t))

    return stack[0]
```

#### 🔍 手把手推演

以 `tokens = ["2", "1", "+", "3", "*"]` 为例：

```
步骤  t   操作              栈内容          说明
──────────────────────────────────────────────────────
 1   '2'  push               [2]            数字入栈
 2   '1'  push               [2, 1]         数字入栈
 3   '+'  pop b=1,a=2        [3]            2+1=3 入栈
          push 3
 4   '3'  push               [3, 3]         数字入栈
 5   '*'  pop b=3,a=3        [9]            3*3=9 入栈
          push 9

结果: 9 ✅  对应 (2+1)*3 = 9
```

> ⚠️ **减法和除法的顺序**：栈顶是第二个操作数，所以是 `a - b` 而非 `b - a`！

#### 📌 适用范围

- ✅ 逆波兰表达式求值（L150）
- ✅ 基本计算器（L224 / L227）
- ✅ 中缀转后缀表达式

---

### 套路5：相邻消除

> **识别特征**：删除相邻的某些字符/元素，删除后新的相邻可能又需要删除（像消消乐）。
> **核心思路**：遍历元素，检查栈顶与当前元素是否满足删除条件，满足则pop，否则push。

#### 🎯 模板代码

```python
def adjacent_remove(s: str) -> str:
    """
    相邻消除通用模板
    适用范围：删除相邻重复、退格、星号消除、消消乐
    """
    stack = []

    for ch in s:
        if ch == '*':                  # 触发删除条件
            if stack:
                stack.pop()            # 删除最近的非星号字符
        # elif stack and ch == stack[-1]:  # 删除相邻重复（另一种变体）
        #     stack.pop()
        else:
            stack.append(ch)

    return ''.join(stack)
```

#### 🔍 手把手推演

以 `s = "leet**cod*e"` （L2390 移除星号）为例：

```
步骤  ch   栈内容          操作
────────────────────────────────────
 1   'l'   ['l']           push
 2   'e'   ['l','e']       push
 3   'e'   ['l','e','e']   push
 4   't'   ['l','e','e','t'] push
 5   '*'   ['l','e','e']   pop 't'
 6   '*'   ['l','e']       pop 'e'
 7   'c'   ['l','e','c']   push
 8   'o'   ['l','e','c','o'] push
 9   'd'   ['l','e','c','o','d'] push
10   '*'   ['l','e','c','o'] pop 'd'
11   'e'   ['l','e','c','o','e'] push

结果: "lecoe" ✅
```

#### 📌 适用范围

- ✅ 移除星号（L2390）
- ✅ 删除相邻重复项（L1047）
- ✅ 退格字符串比较（L844）
- ✅ 字符串消消乐 / 糖果粉碎

---

### 套路6：带条件的碰撞/吞噬

> **识别特征**：元素之间可能发生「碰撞」或「吞噬」，但只在特定条件下触发，且碰撞可能连锁发生。
> **核心思路**：维护栈，遍历新元素时用 while 循环不断检查栈顶与新元素是否碰撞。

#### 🎯 模板代码 — 小行星碰撞

```python
def asteroid_collision(asteroids: list[int]) -> list[int]:
    """
    带条件碰撞通用模板
    碰撞条件：栈顶向右(>0)，新来向左(<0)
    适用范围：小行星碰撞、方向冲突、吞噬类问题
    """
    stack = []

    for x in asteroids:
        # ⚠️ 只有「栈顶右 + 新来左」才碰撞（面对面）
        while stack and x < 0 < stack[-1]:
            if stack[-1] < -x:         # 栈顶小 → 栈顶被摧毁
                stack.pop()
                continue                # 继续检查新的栈顶
            elif stack[-1] == -x:      # 同归于尽
                stack.pop()
            break                       # 当前行星被摧毁

        else:                           # while正常结束（无碰撞）
            stack.append(x)             # 当前行星存活，入栈

    return stack
```

#### 🔍 手把手推演

以 `asteroids = [5, 10, -5]` 为例：

```
步骤  x    栈内容              操作
───────────────────────────────────────────
 1    5    [5]                push（第一个元素）
 2   10    [5, 10]            push（同方向，不碰撞）
 3   -5    while: 10 > 0, -5 < 0 → 碰撞！
           |-5| < 10 → break（-5被摧毁）
           [5, 10]            栈不变

结果: [5, 10] ✅
```

以 `asteroids = [10, 2, -5]` 为例（连锁碰撞）：

```
步骤  x    栈内容              操作
───────────────────────────────────────────
 1   10    [10]               push
 2    2    [10, 2]            push
 3   -5    while: 2 > 0, -5 < 0 → 碰撞！
           |-5| > 2 → pop(2)， continue
           [10]               检查新栈顶
           while: 10 > 0, -5 < 0 → 碰撞！
           |-5| < 10 → break（-5被摧毁）
           [10]               栈不变

结果: [10] ✅
```

> 🤔 **为什么用 `for...else`**？Python 的 `for...else` 在循环没有被 `break` 中断时执行 `else`。这里用 `while...else` 同理：碰撞被 break 了就不执行 else（不push），while 正常结束才 push。

#### 📌 适用范围

- ✅ 小行星碰撞（L735）
- ✅ 行星吞噬
- ✅ 单向碰撞/方向冲突类问题

---

## 五、高频题精讲

### 题1：有效的括号（L20）⭐ 简单

> **来源**：[LeetCode 20](https://leetcode.cn/problems/valid-parentheses/)
> **难度**：简单  |  **套路**：套路1（括号匹配）

#### 📝 题目

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s`，判断字符串是否有效。

有效条件：左括号必须用相同类型的右括号闭合，左括号必须以正确的顺序闭合。

```
示例：s = "()"     → true
     s = "()[]{}" → true
     s = "(]"     → false
     s = "([])"   → true
     s = "([)]"   → false
```

#### 💡 解题思路

这是栈的入门题，也是最经典的括号匹配问题：

1. 遍历字符串
2. 遇到左括号 `(` `[` `{` → 入栈
3. 遇到右括号 `)` `]` `}` → 检查栈顶是否为对应的左括号
4. 匹配则弹出，不匹配直接返回 false
5. 最后检查栈是否为空

#### 📝 完整代码

```python
def isValid(s: str) -> bool:
    """有效的括号 — 栈经典应用"""
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}

    for ch in s:
        if ch in pairs:                    # 右括号
            # 空栈 或 栈顶不匹配 → 无效
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()                    # 匹配成功，弹出
        else:                              # 左括号
            stack.append(ch)

    return not stack                       # 栈空才全部匹配


# 测试
if __name__ == '__main__':
    test_cases = ["()", "()[]{}", "(]", "([])", "([)]"]
    for s in test_cases:
        print(f"isValid('{s}') = {isValid(s)}")
```

#### 📊 复杂度分析

- **时间**：O(n) — 遍历字符串一次
- **空间**：O(n) — 最坏情况全为左括号

---

### 题2：简化路径（L71）⭐⭐ 中等

> **来源**：[LeetCode 71](https://leetcode.cn/problems/simplify-path/)
> **难度**：中等  |  **套路**：套路2（路径/状态模拟）

#### 📝 题目

给定一个 Unix 风格的绝对路径，将其转化为规范路径。

规则：
- `.` 表示当前目录
- `..` 表示上一级目录
- 多个连续斜杠视为单个斜杠
- 结果始终以 `/` 开头，不以 `/` 结尾（除非根目录）

```
示例："/home/"            → "/home"
     "/home//foo/"       → "/home/foo"
     "/home/user/Documents/../Pictures" → "/home/user/Pictures"
     "/../"              → "/"
     "/.../a/../b/c/../d/./" → "/.../b/d"
```

#### 💡 解题思路

1. 用 `/` 分割字符串
2. 遍历每个部分：
   - 空串（连续斜杠）或 `.` → 忽略
   - `..` → 出栈（如果栈不空）
   - 其他 → 入栈
3. 拼接结果：`'/' + '/'.join(stack)`

> ⚠️ **注意**：根目录的 `..` 无效，不能出栈！

#### 📝 完整代码

```python
def simplifyPath(path: str) -> str:
    """简化路径 — 栈模拟"""
    stack = []

    for part in path.split('/'):
        if part in ('', '.'):      # 空串或当前目录
            continue
        if part == '..':           # 返回上一级
            if stack:
                stack.pop()
        else:                      # 有效目录名
            stack.append(part)

    return '/' + '/'.join(stack)


# 测试
if __name__ == '__main__':
    tests = [
        "/home/",
        "/home//foo/",
        "/home/user/Documents/../Pictures",
        "/../",
        "/.../a/../b/c/../d/./",
    ]
    for t in tests:
        print(f"'{t}' → '{simplifyPath(t)}'")
```

#### 📊 复杂度分析

- **时间**：O(n) — split 和遍历
- **空间**：O(n) — stack 存储目录名

---

### 题3：最小栈（L155）⭐⭐ 中等

> **来源**：[LeetCode 155](https://leetcode.cn/problems/min-stack/)
> **难度**：中等  |  **套路**：套路3（辅助栈）

#### 📝 题目

设计一个支持 `push`，`pop`，`top` 操作，并能在 **O(1)** 时间内检索到最小元素的栈。

```
MinStack()    初始化
push(val)     将 val 推入栈
pop()         删除栈顶元素
top()         获取栈顶元素
getMin()      获取栈中最小元素 — O(1) 时间 ⚡
```

#### 💡 解题思路

核心：维护一个「辅助栈」`min_stack`，同步跟踪每个状态的最小值。

- `push` 时：如果 `val <= min_stack[-1]`，也 push 进 `min_stack`
- `pop` 时：如果弹出的值 == `min_stack[-1]`，也 pop `min_stack`
- `getMin`：直接返回 `min_stack[-1]`

#### 📝 完整代码

```python
class MinStack:
    """O(1) 时间获取最小值的栈"""

    def __init__(self):
        self.stack = []          # 主栈
        self.min_stack = []      # 辅助栈：跟踪最小值

    def push(self, val: int) -> None:
        self.stack.append(val)
        # 新值 ≤ 当前最小值时入辅助栈
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self) -> None:
        val = self.stack.pop()
        # 弹出的恰好是最小值，辅助栈同步弹出
        if val == self.min_stack[-1]:
            self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]


# 测试
if __name__ == '__main__':
    ms = MinStack()
    ms.push(-2)
    ms.push(0)
    ms.push(-3)
    print(ms.getMin())  # -3
    ms.pop()
    print(ms.top())     # 0
    print(ms.getMin())  # -2
```

#### 📊 复杂度分析

- **时间**：所有操作 O(1)
- **空间**：O(n) — 辅助栈与主栈同长（最坏情况递减序列）

---

### 题4：逆波兰表达式求值（L150）⭐⭐ 中等

> **来源**：[LeetCode 150](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)
> **难度**：中等  |  **套路**：套路4（表达式求值）

#### 📝 题目

计算逆波兰表达式（后缀表达式）的值。有效的运算符：`+` `-` `*` `/`。除法向零截断。

```
示例：["2","1","+","3","*"] → 9     ((2+1)*3)
     ["4","13","5","/","+"] → 6     (4+(13/5))
```

#### 💡 解题思路

逆波兰表达式天然适合栈：
1. 遍历 tokens
2. 数字 → 入栈
3. 运算符 → 弹出两个操作数 → 计算 → 结果入栈
4. 最终栈中只剩一个值

> 🔑 **关键**：先弹出的是右操作数 `b`，后弹出的是左操作数 `a`，减法和除法顺序是 `a - b` 和 `a / b`。

#### 📝 完整代码

```python
def evalRPN(tokens: list[str]) -> int:
    """逆波兰表达式求值"""
    stack = []
    ops = {'+', '-', '*', '/'}

    for t in tokens:
        if t in ops:
            b = stack.pop()    # 右操作数
            a = stack.pop()    # 左操作数
            if t == '+':
                stack.append(a + b)
            elif t == '-':
                stack.append(a - b)      # ⚠️ 顺序：a - b
            elif t == '*':
                stack.append(a * b)
            else:  # '/'
                stack.append(int(a / b)) # 向零取整
        else:
            stack.append(int(t))

    return stack[0]


# 测试
if __name__ == '__main__':
    print(evalRPN(["2","1","+","3","*"]))          # 9
    print(evalRPN(["4","13","5","/","+"]))          # 6
    print(evalRPN(["10","6","9","3","+","-11","*","/","*","17","+","5","+"]))  # 22
```

#### 📊 复杂度分析

- **时间**：O(n) — 遍历一次
- **空间**：O(n) — 栈最多存 n 个数字

---

### 题5：移除星号（L2390）⭐⭐ 中等

> **来源**：[LeetCode 2390](https://leetcode.cn/problems/removing-stars-from-a-string/)
> **难度**：中等  |  **套路**：套路5（相邻消除）

#### 📝 题目

给定包含星号 `*` 的字符串，每次操作可以选中一个星号，移除它**左侧最近的非星号字符**及星号本身。返回移除所有星号后的字符串。

```
示例：s = "leet**cod*e" → "lecoe"
     s = "erase*****"  → ""
```

#### 💡 解题思路

栈模拟，和「消除相邻」类似：
- 遇到普通字符 → push
- 遇到 `*` → pop（如果栈非空）

#### 📝 完整代码

```python
def removeStars(s: str) -> str:
    """移除星号 — 栈模拟"""
    stack = []
    for ch in s:
        if ch == '*':
            if stack:
                stack.pop()
        else:
            stack.append(ch)
    return ''.join(stack)


# 测试
if __name__ == '__main__':
    print(removeStars("leet**cod*e"))  # "lecoe"
    print(removeStars("erase*****"))   # ""
```

#### 📊 复杂度分析

- **时间**：O(n)
- **空间**：O(n)

---

### 题6：小行星碰撞（L735）⭐⭐ 中等

> **来源**：[LeetCode 735](https://leetcode.cn/problems/asteroid-collision/)
> **难度**：中等  |  **套路**：套路6（带条件碰撞）

#### 📝 题目

给定整数数组表示小行星，正数=向右，负数=向左，绝对值=大小。碰撞规则：面对面碰撞，较小的爆炸，相等同归于尽。返回碰撞后的结果。

```
示例：[5,10,-5]  → [5,10]      (10和-5碰撞,10存活)
     [8,-8]     → []           (同归于尽)
     [10,2,-5]  → [10]         (2和-5碰撞,-5存活;10和-5碰撞,10存活)
```

#### 💡 解题思路

栈模拟 + while 循环处理连锁碰撞：
- 遍历每个行星
- 只有「栈顶向右(>0) + 当前向左(<0)」才碰撞
- 碰撞结果分三种：栈顶小(继续)、相等(同归于尽)、栈顶大(当前消失)
- 不碰撞时正常入栈

#### 📝 完整代码

```python
def asteroidCollision(asteroids: list[int]) -> list[int]:
    """小行星碰撞 — 栈 + while"""
    stack = []

    for x in asteroids:
        # 只有右(正)对左(负)才碰撞
        while stack and x < 0 < stack[-1]:
            if stack[-1] < -x:         # 栈顶小 → 被摧毁
                stack.pop()
                continue                # 继续检查
            elif stack[-1] == -x:      # 同归于尽
                stack.pop()
            break                       # 当前行星被摧毁

        else:                           # 无碰撞 → 存活
            stack.append(x)

    return stack


# 测试
if __name__ == '__main__':
    print(asteroidCollision([5,10,-5]))       # [5,10]
    print(asteroidCollision([8,-8]))          # []
    print(asteroidCollision([10,2,-5]))       # [10]
    print(asteroidCollision([3,5,-6,2,-1,4])) # [-6,2,4]
```

#### 📊 复杂度分析

- **时间**：O(n) — 虽然每个元素可能while循环多次，但每个元素最多入栈出栈各一次
- **空间**：O(n)

---

## 六、性能优化与常见坑

### ⚠️ 坑1：空栈pop/peek

```python
# ❌ 可能崩溃
stack.pop()     # IndexError: pop from empty list
top = stack[-1] # IndexError: list index out of range

# ✅ 安全写法
if stack:
    val = stack.pop()
    top = stack[-1]
```

### ⚠️ 坑2：忘记判空（括号匹配经典错误）

```python
# ❌ 忘记判空
def isValid_bad(s):
    stack = []
    for ch in s:
        if ch == ')':
            if stack[-1] != '(':  # 如果栈空，这里就崩了！
                return False
            stack.pop()
    return not stack

# ✅ 正确写法
def isValid_good(s):
    stack = []
    for ch in s:
        if ch == ')':
            if not stack or stack[-1] != '(':
                return False
            stack.pop()
    return not stack
```

### ⚠️ 坑3：操作符顺序（减法/除法）

```python
# ❌ 错误：弹出的顺序反了
a = stack.pop()  # 这是右操作数！
b = stack.pop()  # 这是左操作数！
result = a - b   # 错误！应该是 b - a

# ✅ 正确：先弹的是右操作数
b = stack.pop()  # 右操作数（栈顶，后进）
a = stack.pop()  # 左操作数（次栈顶，先进）
result = a - b   # 正确
```

### ⚠️ 坑4：转字符串用+拼接 vs join

```python
# ❌ O(n²)：在循环中拼接字符串
result = ""
for ch in stack:
    result += ch       # 每次创建新字符串

# ✅ O(n)：用 join
result = ''.join(stack)
```

### ⚠️ 坑5：最小栈用 `<` 而非 `<=`

```python
# ❌ 如果用 <：重复最小值只入一次
if not min_stack or val < min_stack[-1]:
    min_stack.append(val)

# 问题：push(1), push(1), pop() → min_stack只有一个1，pop后丢失
#        getMin() 找不到值！

# ✅ 用 <=：重复最小值也入栈
if not min_stack or val <= min_stack[-1]:
    min_stack.append(val)
```

---

## 七、OD机考实战指南

### 📊 栈题型在OD中的分布

| 难度 | 出现频率 | 典型题型 | 分值 |
|------|---------|---------|------|
| ⭐ 简单 | 高频 | 括号匹配、退格比较 | 100分题常客 |
| ⭐⭐ 中等 | 中高频 | 最小栈、表达式求值、简化路径 | 100-200分 |
| ⭐⭐⭐ 较难 | 中低频 | 带条件碰撞、最大频率栈 | 200分题常客 |

### 🎯 刷题优先级

| 优先级 | 题型 | 理由 |
|--------|------|------|
| ⭐⭐⭐ 必刷 | 有效的括号（L20） | 栈入门，OD高频 |
| ⭐⭐⭐ 必刷 | 逆波兰表达式（L150） | 表达式求值经典 |
| ⭐⭐⭐ 必刷 | 最小栈（L155） | 辅助栈思想常考 |
| ⭐⭐ 推荐 | 简化路径（L71） | 栈模拟典型 |
| ⭐⭐ 推荐 | 小行星碰撞（L735） | while+栈连锁处理 |
| ⭐ 可选 | 移除星号（L2390） | 思路简单，练手感 |

### ⏱️ 时间分配建议

| 题型 | 建议时间 | 说明 |
|------|---------|------|
| 简单括号匹配 | 5分钟 | 模板题，背熟直接写 |
| 辅助栈类 | 8-10分钟 | 理解辅助栈同步逻辑 |
| 表达式求值 | 8-10分钟 | 注意减法和除法的顺序 |
| 碰撞模拟 | 10-12分钟 | while循环逻辑要理清 |

### 🔑 考场技巧

1. **先判空**：任何时候 pop/peek 前先检查 `if stack:`
2. **注意顺序**：减法除法的操作数顺序是反直觉的（先弹右操作数）
3. **用list**：Python中直接用 `list` 实现栈，别造轮子
4. **模板先行**：把6个套路模板背熟，考场直接套
5. **返回值检查**：括号匹配返回 `not stack` 不是 `True`！

### 🤔 常见面试追问

**Q1：为什么不用递归实现括号匹配？**
> 递归本质也是用调用栈，但递归深度有限制（Python默认约1000）。用显式栈更可控且更高效。

**Q2：最小栈能不能用「存差值」优化空间？**
> 可以！只用一个栈，push时存 `val - current_min`，通过正负判断是否需要更新最小值。但OD机考中不需要这么极致优化。

**Q3：如何用栈实现队列？**
> 用两个栈：一个用于入队(push)，一个用于出队(pop)。出队栈空时把入队栈所有元素倒入出队栈。这是Day 16的内容哦！

**Q4：中缀表达式怎么求值？**
> 需要两个栈：操作数栈 + 运算符栈。遇到运算符时比较优先级。这是Day 17的内容。

**Q5：什么时候用栈 vs 用队列？**
> 栈：需要「回溯」「撤销」「最近关联」→ LIFO；队列：需要「先进先出」「顺序处理」→ FIFO。

---

## 八、今日作业

### 必做（预计30分钟）

| # | 题目 | 难度 | 来源 | 提示 |
|---|------|------|------|------|
| 1 | 有效的括号 | ⭐ | L20 | 套模板，3分钟搞定 |
| 2 | 简化路径 | ⭐⭐ | L71 | 按 `/` split 后栈模拟 |
| 3 | 最小栈 | ⭐⭐ | L155 | 辅助栈同步更新 |

### 选做（预计30分钟）

| # | 题目 | 难度 | 来源 | 提示 |
|---|------|------|------|------|
| 4 | 逆波兰表达式求值 | ⭐⭐ | L150 | 注意操作数顺序 |
| 5 | 删除字符串中相邻重复项 | ⭐ | L1047 | 和星号消除异曲同工 |
| 6 | 比较含退格的字符串 | ⭐ | L844 | 退格=pop |

### 挑战（预计20分钟）

| # | 题目 | 难度 | 来源 | 提示 |
|---|------|------|------|------|
| 7 | 小行星碰撞 | ⭐⭐ | L735 | while + 方向判断 |
| 8 | 最长有效括号 | ⭐⭐⭐ | L32 | 栈存下标，hard题练思维 |

### 📝 自测方法

```python
# 粘贴你的代码后运行测试
if __name__ == '__main__':
    # 有效的括号
    assert isValid("()") == True
    assert isValid("()[]{}") == True
    assert isValid("(]") == False
    assert isValid("([])") == True

    # 简化路径
    assert simplifyPath("/home/") == "/home"
    assert simplifyPath("/../") == "/"
    assert simplifyPath("/a/./b/../../c/") == "/c"

    # 最小栈
    ms = MinStack()
    ms.push(-2); ms.push(0); ms.push(-3)
    assert ms.getMin() == -3
    ms.pop()
    assert ms.top() == 0
    assert ms.getMin() == -2

    print("🎉 全部测试通过！")
```

---

## 📋 今日总结

```
┌──────────────────────────────────────────────────┐
│              🏗️ 栈的6大核心套路                    │
├──────────────┬───────────┬───────────────────────┤
│ 套路1 括号匹配 │ 左入右出    │ pairs字典 + stack     │
│ 套路2 路径模拟 │ split拆分  │ ..出栈、..忽略        │
│ 套路3 辅助栈   │ 同步跟踪    │ 最小值/频率栈         │
│ 套路4 表达式   │ 数入符算    │ 注意减法除法顺序       │
│ 套路5 相邻消除 │ 遇特殊符pop │ 星号/退格/重复消除     │
│ 套路6 条件碰撞 │ while处理   │ 方向判断+连锁反应     │
└──────────────┴───────────┴───────────────────────┘
```

> 📌 **记住**：栈的心法就是「后进先出」四个字。凡是需要回溯、撤销、匹配最近的元素，第一时间想栈！今天的6大套路模板，背熟了能应付90%的栈类题型。

---

*📅 生成日期：2026-05-25 | 🏷️ 标签：栈、LIFO、括号匹配、表达式求值、辅助栈、华为OD*

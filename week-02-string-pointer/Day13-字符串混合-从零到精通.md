# 📘 Day 13：字符串混合 — 从零到精通

> 🎯 **学习目标**：掌握罗马数字双向转换、单词规律映射、子序列判断、变长/定长滑动窗口等混合题型
> 📅 **华为OD备考 · 第2周 · 第13天** | [返回目录](../README.md)

---

## 目录

- [一、混合题型总览](#一混合题型总览)
- [二、核心知识点详解](#二核心知识点详解)
- [三、核心套路模型](#三核心套路模型)
- [四、高频题精讲](#四高频题精讲)
- [五、性能优化与常见坑](#五性能优化与常见坑)
- [六、OD机考实战指南](#六od机考实战指南)
- [七、今日作业](#七今日作业)

---

## 一、混合题型总览

### 1.1 今天学什么？

Day 13 是「字符串混合」专题，我们把几种看似不相关的字符串题型放在一起学习：

```
┌─────────────────────────────────────────────────┐
│                Day 13 字符串混合                    │
├───────────┬───────────┬───────────┬──────────────┤
│ 罗马数字   │ 单词规律   │ 子序列     │ 滑动窗口     │
│ 转换       │ 双射匹配   │ 判断       │ 极值问题     │
├───────────┼───────────┼───────────┼──────────────┤
│ 哈希映射   │ 双向Hash   │ 双指针     │ 计数窗口     │
│ +贪心      │ 一一对应   │ 贪心匹配   │ 定长/变长    │
└───────────┴───────────┴───────────┴──────────────┘
```

这4种题型虽然看起来不同，但实际上都围绕一个核心——**「映射」和「窗口」**：

- **罗马数字**：字符 → 数值的映射
- **单词规律**：字符 ↔ 单词的双向映射
- **子序列**：双指针在字符串中"滑动"匹配
- **滑动窗口极值**：固定/可变窗口在数组中滑动

### 1.2 为什么要放在一起学？

因为OD机考的题目不会告诉你"这题考什么"，你得自己识别题型。今天这4种题在考场上经常交替出现，放在一起学可以训练**题型识别能力**！

### 1.3 周次定位

第2周第6天（Day 13），字符串与双指针专题的**综合实战日**。经过Day 8-12的基础学习，今天用混合题型锻炼综合运用能力。

---

## 二、核心知识点详解

### 2.1 罗马数字 — 最古老的编码系统

#### ① 基本字符映射

罗马数字用7个字母表示数值：

| 字符 | 数值 | 记忆口诀 |
|------|------|----------|
| I | 1 | 一根手指 I |
| V | 5 | V像手掌张开（5指） |
| X | 10 | 两个V合起来 |
| L | 50 | 一半的C（C是100） |
| C | 100 | Centum（拉丁语"一百"） |
| D | 500 | 一半的M |
| M | 1000 | Mille（拉丁语"一千"） |

**小白问：** 为什么罗马数字没有0？
**答：** 罗马人在数学中不使用位置记数法，所以不需要0这个占位符。

#### ② 特殊规则（减法规则）

正常情况下，大数字在左、小数字在右，直接相加。但当**小数字在大数字左边**时，表示减法：

```
IV = 5 - 1 = 4      IX = 10 - 1 = 9
XL = 50 - 10 = 40   XC = 100 - 10 = 90
CD = 500 - 100 = 400  CM = 1000 - 100 = 900
```

只有这6种减法形式！记住口诀：**「I只在V和X前，X只在L和C前，C只在D和M前」**

#### ③ 组合规则

```
罗马数字的书写规则：
1. 同一符号最多连续出现3次（III = 3，但不能写IIII）
2. 5/50/500（V/L/D）不能重复（VV是错的，应该写X）
3. 减法形式只能出现1次（不能写IIX，应该是VIII）
```

**示例：1994 = MCMXCIV**
```
M    = 1000
CM   = 900  (1000 - 100)
XC   = 90   (100 - 10)
IV   = 4    (5 - 1)
━━━━━━━━━━━━━
      1994 ✅
```

### 2.2 单词规律 — 双射映射

#### ① 什么是双射（Bijection）？

双射 = **一一对应**，就像结婚证——每个人只能有一个配偶，每个配偶也只对应一个人。

```
✅ 合法双射（一对一）：
  a → dog       dog → a
  b → cat       cat → b
  ───────────── ─────────────
  完美对应 ✅

❌ 非法情况（多对一）：
  a → dog       dog → a
  b → dog  ❌   dog → b  ← 冲突！
  ─────────────
  两个字母映射到同一个单词 ❌

❌ 非法情况（一对多）：
  a → dog       dog → a
  a → cat  ❌   cat → a  ← 冲突！
  ─────────────
  同一个字母映射到两个单词 ❌
```

#### ② 实现方式

需要**两个哈希表**：
- `char_to_word`：pattern中的字符 → s中的单词
- `word_to_char`：s中的单词 → pattern中的字符

检查时两边都要查！

### 2.3 子序列判断 — 双指针经典

#### ① 子序列 vs 子串

```
字符串: "abcdefg"

子串（必须连续）：
"abc" ✅    "cde" ✅    "ace" ❌（不连续）

子序列（可以不连续，但顺序不变）：
"abc" ✅    "ace" ✅    "ae"  ✅    "ec" ❌（顺序反了）
```

**小白问：** 子序列和子串的区别是什么？
**答：** 子串是连续的一段（像切蛋糕，不能断开），子序列可以跳着选（像挑菜，只要顺序不乱就行）！

#### ② 双指针匹配思路

```
s = "ace", t = "abcde"

指针i指向s的'a'，指针j指向t的'a'
   ↓                          ↓
   a  c  e                   a  b  c  d  e
   
① s[i]='a' == t[j]='a' → 匹配！i++, j++
      ↓                       ↓
   a  c  e                   a  b  c  d  e
   
② s[i]='c' != t[j]='b' → 不匹配，j++（t指针前进）
      ↓                          ↓
   a  c  e                   a  b  c  d  e
   
③ s[i]='c' == t[j]='c' → 匹配！i++, j++
         ↓                          ↓
   a  c  e                   a  b  c  d  e
   
④ s[i]='e' != t[j]='d' → 不匹配，j++
         ↓                             ↓
   a  c  e                   a  b  c  d  e
   
⑤ s[i]='e' == t[j]='e' → 匹配！i++, j++
            ↓                             ↓
   a  c  e                   a  b  c  d  e

⑥ i已经走完s（i == len(s)=3）→ 返回True ✅
```

### 2.4 滑动窗口 — 定长与变长

#### ① 定长滑动窗口

窗户是固定大小的，从头滑到尾：

```
字符串: "abciiidef", k = 3

窗口1: [a b c] i i i d e f  → 元音数 = 1
        └───┘
窗口2: a [b c i] i i d e f  → 元音数 = 1
          └───┘
窗口3: a b [c i i] i d e f  → 元音数 = 2
            └───┘
窗口4: a b c [i i i] d e f  → 元音数 = 3  ← 最大！
              └───┘
窗口5: a b c i [i i d] e f  → 元音数 = 2
                └───┘
窗口6: a b c i i [i d e] f  → 元音数 = 2
                  └───┘
窗口7: a b c i i i [d e f]  → 元音数 = 1
                    └───┘
最大元音数 = 3 ✅
```

每次滑动：左边出去一个字符，右边进来一个字符，更新计数。

#### ② 变长滑动窗口

窗户大小可变，但内部有限制条件：

```
数组: [1,1,0,1,0,1,1]  限制：最多1个0

l=0,r=0: [1]             1 0 1 0 1 1  → 0个0 ✅  窗口长1
l=0,r=1: [1 1]           0 1 0 1 1    → 0个0 ✅  窗口长2
l=0,r=2: [1 1 0]         1 0 1 1      → 1个0 ✅  窗口长3
l=0,r=3: [1 1 0 1]       0 1 1        → 1个0 ✅  窗口长4  ← 最大
l=0,r=4: [1 1 0 1 0]     1 1          → 2个0 ❌  超出限制！
  → 收缩左边界
l=1,r=4: [1 0 1 0]       1 1          → 2个0 ❌
l=2,r=4: [0 1 0]         1 1          → 2个0 ❌
l=3,r=4: [1 0]           1 1          → 1个0 ✅
...
```

### 2.5 常用操作速查表

| 操作 | 写法 | 示例 | 结果 |
|------|------|------|------|
| 字符串分割 | `s.split()` | `"dog cat".split()` | `['dog', 'cat']` |
| 遍历带索引 | `for i, ch in enumerate(s)` | `enumerate("ab")` | `(0,'a'), (1,'b')` |
| 判断元音 | `ch in set("aeiou")` | `'i' in set("aeiou")` | `True` |
| 字符→数值映射 | `dict(zip(keys, vals))` | `dict(zip("IV",[1,5]))` | `{'I':1,'V':5}` |
| 字符串长度 | `len(s)` | `len("hello")` | `5` |
| 子串切片 | `s[i:j]` | `"hello"[1:4]` | `"ell"` |
| 字符串拼接 | `ans += r` | `ans = ""; ans += "M"` | `"M"` |
| 列表最大值 | `max(lst)` | `max([1,3,2])` | `3` |

---

## 三、核心套路/模型

### 套路1：罗马数字双向转换

#### 适用场景
- 给罗马数字转整数（罗马→数字）
- 给整数转罗马数字（数字→罗马）

#### 核心思想

**① 罗马→整数（减法思想）**

遍历字符串，如果当前字符比下一个字符**小**，就做减法；否则做加法。

```
"IV" : I(1) < V(5) → 减法 → -1 + 5 = 4 ✅
"VI" : V(5) > I(1) → 加法 → +5 + 1 = 6 ✅
"XIV": X(10) > I(1) → +10
       I(1) < V(5)  → -1
       结果: 10 - 1 + 5 = 14 ✅
```

**② 整数→罗马（贪心思想）**

从大到小准备所有可能的值和对应的罗马符号（包括6种减法形式），每次减去最大的可表示值。

```
1994:
① 1994 ≥ 1000 → M, 余994
② 994 ≥ 900 → CM, 余94
③ 94 ≥ 90 → XC, 余4
④ 4 ≥ 4 → IV, 余0
结果: MCMXCIV ✅
```

#### 代码模板

```python
# ==================== 模板1：罗马数字 → 整数 ====================
def roman_to_int(s: str) -> int:
    """罗马数字转整数，时间复杂度 O(n)，空间复杂度 O(1)"""
    val_map = {'I': 1, 'V': 5, 'X': 10, 'L': 50,
               'C': 100, 'D': 500, 'M': 1000}
    ans = 0
    n = len(s)
    for i, ch in enumerate(s):
        v = val_map[ch]
        # 如果当前值 < 下一个值 → 做减法
        if i + 1 < n and v < val_map[s[i + 1]]:
            ans -= v
        else:
            ans += v
    return ans

# ==================== 模板2：整数 → 罗马数字 ====================
def int_to_roman(num: int) -> str:
    """整数转罗马数字，时间复杂度 O(1)，空间复杂度 O(1)"""
    # 注意：13个值包含了所有减法形式！
    vals = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
    romans = ["M", "CM", "D", "CD", "C", "XC", "L", "XL",
              "X", "IX", "V", "IV", "I"]
    ans = ""
    for v, r in zip(vals, romans):
        while num >= v:
            ans += r
            num -= v
    return ans
```

#### 手把手推演

**推演1：罗马→整数 "MCMXCIV"**

```
初始化：val_map = {...}, ans = 0

Step 1: i=0, ch='M', v=1000
  i+1 < 7? 是。v=1000 < val_map['C']=100? 否
  ans += 1000 → ans = 1000

Step 2: i=1, ch='C', v=100
  i+1 < 7? 是。v=100 < val_map['M']=1000? 是！→ ans -= 100 → ans = 900

Step 3: i=2, ch='M', v=1000
  i+1 < 7? 是。v=1000 < val_map['X']=10? 否
  ans += 1000 → ans = 1900

Step 4: i=3, ch='X', v=10
  i+1 < 7? 是。v=10 < val_map['C']=100? 是！→ ans -= 10 → ans = 1890

Step 5: i=4, ch='C', v=100
  i+1 < 7? 是。v=100 < val_map['I']=1? 否
  ans += 100 → ans = 1990

Step 6: i=5, ch='I', v=1
  i+1 < 7? 是。v=1 < val_map['V']=5? 是！→ ans -= 1 → ans = 1989

Step 7: i=6, ch='V', v=5
  i+1 < 7? 否（i+1=7 == n=7）
  ans += 5 → ans = 1994 ✅
```

**推演2：整数→罗马 3749**

```
num = 3749

v=1000, r="M": num≥1000? 是！→ ans="M", num=2749
                num≥1000? 是！→ ans="MM", num=1749
                num≥1000? 是！→ ans="MMM", num=749
                num≥1000? 否

v=900, r="CM": num≥900? 否

v=500, r="D":  num≥500? 是！→ ans="MMMD", num=249
               num≥500? 否

v=400, r="CD": num≥400? 否

v=100, r="C":  num≥100? 是！→ ans="MMMDC", num=149
               num≥100? 是！→ ans="MMMDCC", num=49
               num≥100? 否

v=90, r="XC":  num≥90? 否

v=50, r="L":   num≥50? 否

v=40, r="XL":  num≥40? 是！→ ans="MMMDCCXL", num=9
               num≥40? 否

v=10, r="X":   num≥10? 否

v=9, r="IX":   num≥9? 是！→ ans="MMMDCCXLIX", num=0
               num≥9? 否

结果: "MMMDCCXLIX" ✅
```

### 套路2：单词规律 — 双射哈希

#### 适用场景
- 判断字符串是否遵循某种模式
- 检查两个序列是否有一一对应关系

#### 核心思想
用两个哈希表维护**双向映射**：
- pattern的字符 → 单词
- 单词 → pattern的字符

遍历时检查：
1. 如果字符已在 char_to_word 中，检查映射的单词是否一致
2. 如果单词已在 word_to_char 中，检查映射的字符是否一致
3. 不一致就返回 False

#### 代码模板

```python
def word_pattern(pattern: str, s: str) -> bool:
    """检查pattern和s是否遵循双射规律"""
    words = s.split()
    if len(pattern) != len(words):
        return False
    
    char_to_word = {}
    word_to_char = {}
    
    for ch, w in zip(pattern, words):
        # 检查字符→单词映射
        if ch in char_to_word:
            if char_to_word[ch] != w:
                return False
        else:
            char_to_word[ch] = w
        
        # 检查单词→字符映射（双向检查确保一一对应）
        if w in word_to_char:
            if word_to_char[w] != ch:
                return False
        else:
            word_to_char[w] = ch
    
    return True
```

#### 手把手推演

```
pattern = "abba", s = "dog cat cat dog"

words = ["dog", "cat", "cat", "dog"]
len(pattern) = 4, len(words) = 4 → 继续

--- 第1对: ch='a', w='dog' ---
char_to_word: {'a': 'dog'}  ← 新增
word_to_char: {'dog': 'a'}  ← 新增

--- 第2对: ch='b', w='cat' ---
char_to_word: {'a': 'dog', 'b': 'cat'}  ← 新增
word_to_char: {'dog': 'a', 'cat': 'b'}  ← 新增

--- 第3对: ch='b', w='cat' ---
char_to_word['b'] = 'cat' ✅ 匹配
word_to_char['cat'] = 'b' ✅ 匹配

--- 第4对: ch='a', w='dog' ---
char_to_word['a'] = 'dog' ✅ 匹配
word_to_char['dog'] = 'a' ✅ 匹配

全部通过 → True ✅
```

**对比：pattern = "abba", s = "dog cat cat fish"**

```
前3对都通过...
第4对: ch='a', w='fish'
char_to_word['a'] = 'dog' ❌ 'dog' != 'fish' → False ❌
```

### 套路3：子序列判断 — 双指针匹配

#### 适用场景
- 判断一个字符串是否是另一个的子序列
- 进阶：大量短串匹配同一个长串

#### 核心思想
两个指针分别遍历短串和长串，字符相等时两个指针都前进，否则只有长串指针前进。如果短串指针走到了末尾，说明全部匹配。

#### 代码模板

```python
def is_subsequence(s: str, t: str) -> bool:
    """判断s是否是t的子序列"""
    i = j = 0  # i指向s，j指向t
    while i < len(s) and j < len(t):
        if s[i] == t[j]:
            i += 1  # 匹配上了，s指针也前进
        j += 1      # t指针始终前进
    return i == len(s)  # s是否全部匹配完成
```

#### 进阶：处理大量输入的S（预处理法）

当有10亿个S需要判断时，不能每次O(n)扫描T：

```python
from bisect import bisect_left
from collections import defaultdict

def preprocess(t: str):
    """预处理T，记录每个字符出现的位置列表"""
    pos = defaultdict(list)
    for i, ch in enumerate(t):
        pos[ch].append(i)
    return pos

def is_subsequence_fast(s: str, pos: dict) -> bool:
    """O(m * log n)判断，m=len(s), n=len(t)"""
    cur = -1  # 当前在T中的位置
    for ch in s:
        if ch not in pos:
            return False
        # 在pos[ch]中找第一个大于cur的位置
        idx = bisect_left(pos[ch], cur + 1)
        if idx == len(pos[ch]):  # 没有找到
            return False
        cur = pos[ch][idx]
    return True
```

### 套路4：滑动窗口极值

#### 4.1 定长滑动窗口

##### 适用场景
- 固定长度k的子数组/子串的某种统计量（最大值、最小值、和、元音数等）

##### 代码模板

```python
def fixed_sliding_window(s: str, k: int) -> int:
    """定长滑动窗口模板"""
    # 1. 初始化第一个窗口
    count = 0
    for i in range(k):
        if condition(s[i]):  # 自定义条件
            count += 1
    ans = count
    
    # 2. 滑动
    for i in range(k, len(s)):
        # 右边进来一个
        if condition(s[i]):
            count += 1
        # 左边出去一个
        if condition(s[i - k]):
            count -= 1
        # 更新答案
        ans = max(ans, count)
    
    return ans
```

#### 4.2 变长滑动窗口（最多允许1个0）

##### 适用场景
- 删除一个元素后全为1的最长连续子数组
- 等价于窗口内最多有1个0

##### 代码模板

```python
def longest_with_one_zero(nums: list[int]) -> int:
    """最多允许1个0的滑动窗口"""
    l = 0
    zeros = 0
    ans = 0
    for r, val in enumerate(nums):
        if val == 0:
            zeros += 1
        # 超过1个0 → 收缩左边界
        while zeros > 1:
            if nums[l] == 0:
                zeros -= 1
            l += 1
        # 长度为 r-l，再减去被删除的1个元素
        ans = max(ans, r - l)
    return ans
```

---

## 四、高频题精讲

### 题1：罗马数字转整数（⭐⭐ 经典必考）

**题目：**
罗马数字包含 `I`(1)、`V`(5)、`X`(10)、`L`(50)、`C`(100)、`D`(500)、`M`(1000)。小的数字在大的数字左边表示减法（如 IV=4），右边表示加法（如 VI=6）。给定一个罗马数字，转换成整数。

**示例：**
```
"III" → 3
"IV" → 4
"LVIII" → 58
"MCMXCIV" → 1994
```

**思路：**
建立字符→数值映射，遍历字符串。规则很简单：当前字符的值小于下一个字符的值就做减法，否则做加法。

```python
def romanToInt(s: str) -> int:
    # 建立映射表
    val = {'I': 1, 'V': 5, 'X': 10, 'L': 50,
           'C': 100, 'D': 500, 'M': 1000}
    ans = 0
    n = len(s)
    
    for i, ch in enumerate(s):
        v = val[ch]
        # 关键判断：当前值是否小于下一个值
        if i + 1 < n and v < val[s[i + 1]]:
            ans -= v  # 减法情况
        else:
            ans += v  # 加法情况
    
    return ans
```

**手把手推演：** 以 `"IV"` 为例

```
初始化: val = {I:1, V:5, X:10, ...}, ans=0, n=2

Step1: i=0, ch='I', v=1
  i+1=1 < n=2 ✅ → v=1 < val['V']=5? 是!
  ans -= 1 → ans = -1

Step2: i=1, ch='V', v=5
  i+1=2 < n=2? 否（越界了）
  ans += 5 → ans = 4

返回 4 ✅
```

**复杂度分析：**
- ⏱ 时间复杂度：O(n) — 只遍历一次
- 💾 空间复杂度：O(1) — 固定大小的哈希表

### 题2：整数转罗马数字（⭐⭐）

**题目：**
给定一个整数（1~3999），将其转换为罗马数字。

**示例：**
```
3749 → "MMMDCCXLIX"
58 → "LVIII"
1994 → "MCMXCIV"
```

**思路：**
从大到小准备好所有可能的数值和对应符号（包括6种减法形式），然后贪心地每次减去最大的可表示值。

```python
def intToRoman(num: int) -> str:
    # 从大到小排列所有可能的值（含减法形式）
    values = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
    symbols = ["M", "CM", "D", "CD", "C", "XC", "L", "XL",
               "X", "IX", "V", "IV", "I"]
    
    ans = ""
    for v, s in zip(values, symbols):
        # 贪心：尽量多用大的单位
        while num >= v:
            ans += s
            num -= v
    
    return ans
```

**手把手推演：** 以 58 为例

```
num = 58

v=1000 → num<1000 → 跳过
v=900  → num<900  → 跳过
v=500  → num<500  → 跳过
v=400  → num<400  → 跳过
v=100  → num<100  → 跳过
v=90   → num<90   → 跳过
v=50   → num≥50!  → ans="L", num=8
         num<50   → 停止循环
v=40   → num<40   → 跳过
v=10   → num<10   → 跳过
v=9    → num<9    → 跳过
v=5    → num≥5!   → ans="LV", num=3
v=4    → num<4    → 跳过
v=1    → num≥1!   → ans="LVI", num=2
         num≥1!   → ans="LVII", num=1
         num≥1!   → ans="LVIII", num=0

结果: "LVIII" ✅
```

**复杂度分析：**
- ⏱ 时间复杂度：O(1) — 最多循环固定次数（13个值，每个最多减3次）
- 💾 空间复杂度：O(1) — 固定数组

### 题3：单词规律（⭐⭐）

**题目：**
给定一个规律 `pattern="abba"` 和一个字符串 `s="dog cat cat dog"`，判断s是否遵循该规律。

**示例：**
```
pattern="abba", s="dog cat cat dog" → true
pattern="abba", s="dog cat cat fish" → false
pattern="aaaa", s="dog cat cat dog" → false
```

```python
def wordPattern(pattern: str, s: str) -> bool:
    words = s.split()
    
    # 长度不同 → 肯定不匹配
    if len(pattern) != len(words):
        return False
    
    char_to_word = {}
    word_to_char = {}
    
    for ch, w in zip(pattern, words):
        # 检查 char → word
        if ch in char_to_word:
            if char_to_word[ch] != w:
                return False
        else:
            char_to_word[ch] = w
        
        # 检查 word → char（双向检查！）
        if w in word_to_char:
            if word_to_char[w] != ch:
                return False
        else:
            word_to_char[w] = ch
    
    return True
```

**手把手推演：** `pattern="abba"`, `s="dog cat cat dog"`

```
words = ["dog", "cat", "cat", "dog"]
len = 4 == 4 ✅

第1对：ch='a', w='dog'
  char_to_word: {'a': 'dog'}    ← 新增
  word_to_char: {'dog': 'a'}    ← 新增

第2对：ch='b', w='cat'
  char_to_word: {'a': 'dog', 'b': 'cat'}    ← 新增
  word_to_char: {'dog': 'a', 'cat': 'b'}    ← 新增

第3对：ch='b', w='cat'
  char_to_word['b'] = 'cat' ✅
  word_to_char['cat'] = 'b' ✅

第4对：ch='a', w='dog'
  char_to_word['a'] = 'dog' ✅
  word_to_char['dog'] = 'a' ✅

全部通过 → True ✅
```

**对比：** `pattern="abba"`, `s="dog cat cat fish"`

```
前3对通过...
第4对：ch='a', w='fish'
  char_to_word['a'] = 'dog' ❌ 'dog' != 'fish'
  → False ❌
```

**复杂度分析：**
- ⏱ 时间复杂度：O(n) — 一次遍历
- 💾 空间复杂度：O(n) — 两个哈希表

### 题4：删除一个元素后全为1的最长子数组（⭐⭐ 滑动窗口）

**题目：**
给定一个二进制数组 `nums`，需要从中删掉一个元素，返回最长的只包含1的非空子数组长度。

**示例：**
```
nums = [1,1,0,1] → 3（删掉0）
nums = [0,1,1,1,0,1,1,0,1] → 5
nums = [1,1,1] → 2（必须删除一个元素）
```

**思路：**
使用滑动窗口，允许窗口内最多有1个0。最终答案 = 最大窗口长度 - 1（因为题目要求必须删除一个元素）。

```python
def longestSubarray(nums: list[int]) -> int:
    left = 0
    zero_count = 0
    ans = 0
    
    for right, val in enumerate(nums):
        if val == 0:
            zero_count += 1
        
        # 超过1个0 → 收缩左边界
        while zero_count > 1:
            if nums[left] == 0:
                zero_count -= 1
            left += 1
        
        # 窗口长度 = right - left（已经减去了被删除的1个元素）
        # 因为要删掉1个元素，所以有效长度是 right - left
        ans = max(ans, right - left)
    
    return ans
```

注意：这里 `ans = max(ans, right - left)` 而不是 `right - left + 1`，因为题目**必须删除一个元素**，所以窗口不管有没有0，都要减去1。

**手把手推演：** `nums = [1,1,0,1]`

```
初始化：left=0, zero_count=0, ans=0

r=0, val=1: zero_count=0, 窗口=[1], ans=max(0, 0-0)=0
r=1, val=1: zero_count=0, 窗口=[1,1], ans=max(0, 1-0)=1
r=2, val=0: zero_count=1, 窗口=[1,1,0], ans=max(1, 2-0)=2
r=3, val=1: zero_count=1, 窗口=[1,1,0,1], ans=max(2, 3-0)=3

返回 3 ✅
```

**第二个例子：** `nums = [1,1,1]`

```
r=0, val=1: zero_count=0, ans=max(0, 0)=0
r=1, val=1: zero_count=0, ans=max(0, 1)=1
r=2, val=1: zero_count=0, ans=max(1, 2)=2

返回 2 ✅ （全1必须删一个1）
```

**复杂度分析：**
- ⏱ 时间复杂度：O(n) — 每个元素最多进出窗口一次
- 💾 空间复杂度：O(1) — 只用几个变量

### 题5：定长子串中元音的最大数目（⭐⭐）

**题目：**
返回字符串 `s` 中长度为 `k` 的子串中，最多能包含多少个元音字母（a、e、i、o、u）。

**示例：**
```
s = "abciiidef", k = 3 → 3（"iii"包含3个元音）
s = "aeiou", k = 2 → 2
s = "leetcode", k = 3 → 2
```

**思路：**
固定长度k的滑动窗口。先算第一个窗口的元音数，然后每次滑动时：右边进来一个就加计数，左边出去一个就减计数，同时记录最大值。

```python
def maxVowels(s: str, k: int) -> int:
    vowels = set("aeiou")
    
    # 第一个窗口
    count = 0
    for i in range(k):
        if s[i] in vowels:
            count += 1
    ans = count
    
    # 滑动窗口
    for i in range(k, len(s)):
        # 右边新进来的字符
        if s[i] in vowels:
            count += 1
        # 左边出去的字符
        if s[i - k] in vowels:
            count -= 1
        # 更新最大值
        ans = max(ans, count)
    
    return ans
```

**手把手推演：** `s = "abciiidef", k = 3`

```
vowels = {'a','e','i','o','u'}

--- 第一个窗口 [0:3] = "abc" ---
s[0]='a' → 是元音 ✓ → count=1
s[1]='b' → 不是元音 ✗
s[2]='c' → 不是元音 ✗
ans = count = 1

--- 滑动到 [1:4] = "bci" ---
i=3，s[3]='i' → 是元音 ✓ → count=2
i-k=0，s[0]='a' → 是元音 ✓ → count=1
ans = max(1, 1) = 1 ❌（等一等，我算错了）
```

等等，让我重新算一下：

```
--- 第一个窗口 [0:3] = "abc" ---
s[0]='a' ∈ vowels → count=1
s[1]='b' ∉ vowels → count=1
s[2]='c' ∉ vowels → count=1
ans = 1

--- i=3: s[3]='i' ∈ vowels → count=2
--- s[3-3]=s[0]='a' ∈ vowels → count=1
ans = max(1, 1) = 1

--- i=4: s[4]='i' ∈ vowels → count=2
--- s[4-3]=s[1]='b' ∉ vowels → count=2
ans = max(1, 2) = 2

--- i=5: s[5]='i' ∈ vowels → count=3
--- s[5-3]=s[2]='c' ∉ vowels → count=3
ans = max(2, 3) = 3

--- i=6: s[6]='d' ∉ vowels → count=3
--- s[6-3]=s[3]='i' ∈ vowels → count=2
ans = max(3, 2) = 3

--- i=7: s[7]='e' ∈ vowels → count=3
--- s[7-3]=s[4]='i' ∈ vowels → count=2
ans = max(3, 2) = 3

--- i=8: s[8]='f' ∉ vowels → count=2
--- s[8-3]=s[5]='i' ∈ vowels → count=1
ans = max(3, 1) = 3

返回 3 ✅
```

**复杂度分析：**
- ⏱ 时间复杂度：O(n) — 一次遍历
- 💾 空间复杂度：O(1) — 固定大小的集合

---

## 五、性能优化与常见坑

### 5.1 易错点大全

#### ❌ 错误1：罗马数字转换忘记减法情况

```python
# ❌ 错误：只做加法
def romanToInt_WRONG(s):
    val = {'I':1, 'V':5, ...}
    ans = 0
    for ch in s:
        ans += val[ch]  # "IV" → 1+5=6 ❌
    return ans

# ✅ 正确：需要判断减法
def romanToInt(s):
    val = {'I':1, 'V':5, ...}
    ans = 0
    for i, ch in enumerate(s):
        v = val[ch]
        if i+1 < len(s) and v < val[s[i+1]]:
            ans -= v  # 减法！
        else:
            ans += v
    return ans
```

#### ❌ 错误2：单词规律只用一个哈希表

```python
# ❌ 错误：只检查一个方向
def wordPattern_WRONG(pattern, s):
    words = s.split()
    mapping = {}
    for ch, w in zip(pattern, words):
        if ch in mapping:
            if mapping[ch] != w:
                return False
        mapping[ch] = w
    return True  # "abba","dog dog dog dog" → True ❌

# ✅ 正确：双向检查
def wordPattern(pattern, s):
    words = s.split()
    char_to_word = {}
    word_to_char = {}
    for ch, w in zip(pattern, words):
        if ch in char_to_word and char_to_word[ch] != w:
            return False
        if w in word_to_char and word_to_char[w] != ch:
            return False
        char_to_word[ch] = w
        word_to_char[w] = ch
    return True
```

#### ❌ 错误3：滑动窗口忘记题目要求必须删除一个元素

```python
# ❌ 错误：没有减去1
def longestSubarray_WRONG(nums):
    left = zeros = ans = 0
    for right, val in enumerate(nums):
        if val == 0:
            zeros += 1
        while zeros > 1:
            if nums[left] == 0:
                zeros -= 1
            left += 1
        ans = max(ans, right - left + 1)  # ❌ 多加了1！
    return ans  # [1,1,1] → 错误返回3，应该返回2

# ✅ 正确：减1
def longestSubarray(nums):
    left = zeros = ans = 0
    for right, val in enumerate(nums):
        if val == 0:
            zeros += 1
        while zeros > 1:
            if nums[left] == 0:
                zeros -= 1
            left += 1
        ans = max(ans, right - left)  # ✅ 必须删一个
    return ans
```

### 5.2 边界情况汇总

| 题型 | 边界情况 | 正确做法 |
|------|----------|----------|
| 罗马→整数 | 只有一个字符"I" | 正常加法返回1 |
| 整数→罗马 | 最小值1="I"，最大值3999 | 注意数组包含所有13种组合 |
| 单词规律 | pattern长度≠单词数 | 直接返回False |
| 单词规律 | 空pattern或空字符串 | len检查会处理 |
| 子序列 | s为空串 | len(s)=0 → 返回True |
| 子序列 | t比s短 | 双指针中j会先走到头，返回False |
| 定长滑动窗口 | k=1 | 处理正常 |
| 变长滑动窗口 | 全为1 | 必须减1 |

### 5.3 性能陷阱

#### 字符串拼接性能

```python
# ❌ 慢：Python字符串不可变，每次拼接都创建新字符串
# 但这里num最多3999（最多15个字符），性能没影响
# 如果要在大量字符中拼接，用列表+join

# ✅ 如果要拼接大量字符：
chars = []
# ... 不断 chars.append(ch)
result = "".join(chars)  # O(n)
```

#### 集合查找 vs 列表查找

```python
# ✅ 快：set查找是O(1)
vowels = set("aeiou")
if ch in vowels:  # O(1)

# ❌ 慢：list查找是O(n)
vowels = ['a','e','i','o','u']
if ch in vowels:  # O(n)
```

---

## 六、OD机考实战指南

### 6.1 题型分布分析

| 本题型 | OD 100分题频率 | OD 200分题频率 | 优先级 |
|--------|---------------|---------------|--------|
| 罗马数字转换 | ⭐⭐⭐ 高频 | ⭐ 偶尔出现 | 必会 |
| 单词规律 | ⭐⭐ 中频 | ⭐ 偶尔出现 | 掌握 |
| 子序列判断 | ⭐⭐⭐ 高频 | ⭐⭐ 中等 | 必会 |
| 定长滑动窗口 | ⭐⭐ 中频 | ⭐⭐⭐ 高频（作为子问题） | 牢固掌握 |
| 变长滑动窗口 | ⭐⭐ 中频 | ⭐⭐⭐ 高频（作为子问题） | 牢固掌握 |

### 6.2 刷题优先级

| 优先级 | 题目 | 掌握程度 | 预估时间 |
|--------|------|----------|----------|
| ⭐⭐⭐ | 罗马数字转整数 | 闭着眼能写 | 10min |
| ⭐⭐⭐ | 判断子序列 | 闭着眼能写 | 5min |
| ⭐⭐ | 整数转罗马数字 | 理解贪心即可 | 10min |
| ⭐⭐ | 单词规律 | 理解双射+模板 | 10min |
| ⭐⭐ | 定长子串最大元音数 | 滑动窗口模板 | 10min |
| ⭐⭐ | 删除一个元素的滑动窗口 | 理解变长窗口 | 10min |

### 6.3 考场技巧

1. **罗马数字题**：记住「比下一个字符小就减」的口诀，代码不超过15行
2. **单词规律题**：看到"一一对应"马上想到**双向哈希表**
3. **子序列题**：双指针法最简单，进阶就用预处理法
4. **滑动窗口题**：先判断是定长还是变长：
   - 定长：先算第一个窗口，再滑动
   - 变长：用while收缩左边界
5. **时间分配**：如果5分钟没思路就先跳过，最后回来蒙个暴力解

### 6.4 小白问：OD考试中一般考哪种？

**答：** 100分题常考**罗马数字转换**和**子序列判断**，因为逻辑清晰、代码短、适合作为第一题。**单词规律**和**滑动窗口**更常见于大题的某个子问题。最实用的建议是：**子序列（双指针模板）+ 滑动窗口（定长模板）**这两个模板一定要背熟，因为它们是最常用的底层工具。

---

## 七、今日作业 🏠

### ⭐ 必做（30min）

**1. 罗马数字转整数**
实现 `romanToInt()`，测试以下用例：
- `"III"` → 3
- `"LVIII"` → 58
- `"MCMXCIV"` → 1994
- `"IV"` → 4

**2. 判断子序列**
实现 `isSubsequence()`，测试：
- `s="abc", t="ahbgdc"` → True
- `s="axc", t="ahbgdc"` → False
- `s="", t="anything"` → True
- `s="abc", t="ab"` → False

### ⭐⭐ 选做（30min）

**3. 整数转罗马数字**
实现 `intToRoman()`，测试：
- `3749` → `"MMMDCCXLIX"`
- `58` → `"LVIII"`
- `1994` → `"MCMXCIV"`

**4. 单词规律**
实现 `wordPattern()`，测试：
- `"abba", "dog cat cat dog"` → True
- `"abba", "dog cat cat fish"` → False
- `"aaaa", "dog cat cat dog"` → False

### ⭐⭐⭐ 挑战（20min）

**5. 定长滑动窗口综合**
实现 `maxVowels()`，测试：
- `"abciiidef", k=3` → 3
- `"aeiou", k=2` → 2
- `"rhythms", k=4` → 0

**6. 变长滑动窗口综合**
实现 `longestSubarray()`，测试：
- `[1,1,0,1]` → 3
- `[0,1,1,1,0,1,1,0,1]` → 5
- `[1,1,1]` → 2

**自测方法：**
把以上用例放在一个`test.py`文件里，运行 `python test.py`，全部通过说明掌握了！

```python
# test.py 示例
def test_romanToInt():
    assert romanToInt("III") == 3
    assert romanToInt("LVIII") == 58
    assert romanToInt("MCMXCIV") == 1994
    assert romanToInt("IV") == 4
    print("✅ 罗马数字转整数全部通过!")

# 运行所有测试
test_romanToInt()
# 添加其他测试...
print("🎉 全部通过！")
```

---

> 💡 **今日总结**：Day 13是字符串混合题型的关键。罗马数字（映射+贪心）、单词规律（双射哈希）、子序列（双指针）、滑动窗口（定长/变长）——这四个题型看似不同，但都是最常考的基础题型。重点掌握**罗马数字转换**和**双指针**，这是OD机考的常客！
>
> 📝 **学习笔记仓库**：https://gitee.com/iiixiyan/huawei-od-learning
>
> 📅 **明日预告**：Day 14 — 第2周复习与综合测验！

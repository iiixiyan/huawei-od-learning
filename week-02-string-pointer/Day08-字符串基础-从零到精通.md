# 📘 Day 8：字符串基础 — 从零到精通

> 🎯 **学习目标**：零基础掌握字符串核心操作、4种高频题型套路、OD机考实战技巧
> 📅 **华为OD备考 · 第二周 · 第8天** | [返回目录](../README.md)

---

## 目录

- [一、字符串是什么？](#一字符串是什么)
- [二、Python字符串常用操作大全](#二python字符串常用操作大全)
- [三、四大核心套路详解](#三四大核心套路详解)
  - [套路1：双指针反转](#套路1双指针反转)
  - [套路2：单词反转](#套路2单词反转)
  - [套路3：前缀匹配](#套路3前缀匹配)
  - [套路4：坐标变换（Z字形）](#套路4坐标变换z字形)
- [四、高频题精讲](#四高频题精讲)
- [五、性能优化与面试进阶](#五性能优化与面试进阶)
- [六、OD机考实战指南](#六od机考实战指南)
- [七、今日作业](#七今日作业)

---

## 一、字符串是什么？

### 1.1 直观理解

字符串就是一串字符排在一起，像一串糖葫芦 🍡：

```
s = "HUAWEI"
      ↑ ↑ ↑ ↑ ↑ ↑
      H U A W E I
      0 1 2 3 4 5   ← 索引（从0开始）
```

### 1.2 初始化字符串

```python
# 四种写法
s1 = "hello"              # 双引号
s2 = 'world'              # 单引号也行
s3 = """多行
字符串"""                  # 三引号可以换行
s4 = str(123)             # 数字转字符串 → "123"
```

### 1.3 字符串最重要的特性：不可变

```python
s = "hello"
s[0] = 'H'   # ❌ TypeError: 'str' object does not support item assignment
```

**这意味着**：所有"修改"字符串的操作，实际上都是创建了一个新字符串。

```python
# ✅ 正确做法：转列表 → 修改 → 拼接
cs = list(s)       # ['h', 'e', 'l', 'l', 'o']
cs[0] = 'H'
new_s = "".join(cs)  # "Hello"

# 或者用 replace（也是创建新字符串）
new_s = s.replace('h', 'H')  # "Hello"（但只替换指定字符）
```

---

## 二、Python字符串常用操作大全

### 2.1 基础操作速查

| 类别 | 操作 | 写法 | 示例 | 结果 |
|------|------|------|------|------|
| **长度** | 取长度 | `len(s)` | `len("hello")` | `5` |
| **索引** | 取字符 | `s[i]` | `"hello"[1]` | `'e'` |
| **切片** | 取子串 | `s[i:j]` | `"hello"[1:4]` | `"ell"` |
| **切片** | 取到最后 | `s[i:]` | `"hello"[2:]` | `"llo"` |
| **切片** | 从开头取 | `s[:j]` | `"hello"[:4]` | `"hell"` |
| **切片** | 步进 | `s[i:j:k]` | `"hello"[::2]` | `"hlo"` |
| **切片** | 反转 | `s[::-1]` | `"abc"[::-1]` | `"cba"` |
| **拼接** | +运算符 | `s + t` | `"a" + "b"` | `"ab"` |
| **拼接** | join法 | `" ".join(lst)` | `"".join(["a","b"])` | `"ab"` |
| **查找** | 找子串 | `s.find(x)` | `"hello".find("ll")` | `2` |
| **查找** | 从右边找 | `s.rfind(x)` | `"hello".rfind("l")` | `3` |
| **判断** | 是否以...开头 | `s.startswith(x)` | `"hello".startswith("he")` | `True` |
| **判断** | 是否以...结尾 | `s.endswith(x)` | `"hello".endswith("lo")` | `True` |
| **判断** | 是否全是字母 | `s.isalpha()` | `"abc".isalpha()` | `True` |
| **判断** | 是否全是数字 | `s.isdigit()` | `"123".isdigit()` | `True` |
| **判断** | 子串在不在 | `x in s` | `"ll" in "hello"` | `True` |
| **分割** | 按空格分 | `s.split()` | `"a b c".split()` | `['a','b','c']` |
| **分割** | 按字符分 | `s.split(',')` | `"a,b,c".split(',')` | `['a','b','c']` |
| **替换** | 替换子串 | `s.replace(a,b)` | `"ab".replace("a","c")` | `"cb"` |
| **去除** | 去除两边空格 | `s.strip()` | `"  hi  ".strip()` | `"hi"` |
| **去除** | 去除左边空格 | `s.lstrip()` | `"  hi".lstrip()` | `"hi"` |
| **去除** | 去除右边空格 | `s.rstrip()` | `"hi  ".rstrip()` | `"hi"` |
| **大小写** | 转大写 | `s.upper()` | `"hi".upper()` | `"HI"` |
| **大小写** | 转小写 | `s.lower()` | `"HI".lower()` | `"hi"` |
| **计数** | 统计出现次数 | `s.count(x)` | `"hello".count("l")` | `2` |

### 2.2 最容易混淆的3个细节

```python
# ⚠️ 1. split() 和 split(' ') 不一样！
s = "a  b  c"          # 两个空格
s.split()              # ['a', 'b', 'c']  — 自动处理多个空格
s.split(' ')           # ['a', '', 'b', '', 'c'] — 按单个空格分，出现空串

# ⚠️ 2. slice不会越界
s = "hi"
s[10:]                # ""（空字符串），不会报错
s[10]                 # ❌ IndexError!

# ⚠️ 3. find 找不到返回 -1，in 返回 False
s = "hello"
s.find("x")           # -1
"x" in s              # False
s.index("x")          # ❌ ValueError! 用 index 前要确保存在
```

---

## 三、四大核心套路详解

Day 8 的 6 道题，可以归纳为 4 个核心套路。每个套路背下模板，就能解决一大类题目。

---

### 套路1：双指针反转

#### 适用场景
需要反转字符串中的某些特定字符（元音、字母等），而非全部反转。

#### 核心思想
```
左指针 →                    ← 右指针
          ↓  ↓  ↓  ↓
          H e l l o
          ↑        ↑
          l        r
          
当左指针找到"目标字符"，右指针也找到"目标字符"时，交换
然后继续往中间移动
```

#### 代码模板

```python
def reverse_with_twopointers(s: str, target_set: set) -> str:
    """
    反转字符串中的特定字符
    :param s: 输入字符串
    :param target_set: 需要反转的字符集合
    """
    cs = list(s)             # 转列表（字符串不可变）
    l, r = 0, len(cs) - 1   # 左右指针
    
    while l < r:
        # 左边找到目标字符
        while l < r and cs[l] not in target_set:
            l += 1
        # 右边找到目标字符
        while l < r and cs[r] not in target_set:
            r -= 1
        # 交换
        cs[l], cs[r] = cs[r], cs[l]
        l += 1
        r -= 1
    
    return "".join(cs)
```

#### 典型应用：反转元音字母

```python
def reverseVowels(s: str) -> str:
    vowels = set("aeiouAEIOU")     # 注意大小写都算
    cs = list(s)
    l, r = 0, len(cs) - 1
    
    while l < r:
        while l < r and cs[l] not in vowels:
            l += 1
        while l < r and cs[r] not in vowels:
            r -= 1
        cs[l], cs[r] = cs[r], cs[l]
        l += 1
        r -= 1
    
    return "".join(cs)
```

**手把手推演**：

```
s = "IceCreAm"
cs = ['I','c','e','C','r','e','A','m']
l=0, r=7

① l=0 → 'I' 是元音 ✓，r=7 → 'm' 不是元音，r=6
   r=6 → 'A' 是元音 ✓
   交换 cs[0]和cs[6]: ['A','c','e','C','r','e','I','m']
   l=1, r=5

② l=1 → 'c' 不是元音，l=2
   l=2 → 'e' 是元音 ✓
   r=5 → 'e' 是元音 ✓
   交换 cs[2]和cs[5]: ['A','c','e','C','r','e','I','m']（不变，因为都是e）
   l=3, r=4

③ l=3 → 'C' 不是元音，l=4
   l=4 → 'r' 不是元音，l=5
   l=5 >= r=4 → 退出

结果: "AceCreIm" ✅
```

**复杂度**：O(n) 时间，O(n) 空间（转成了列表）

---

### 套路2：单词反转

#### 适用场景
字符串中有多个单词，需要颠倒顺序（且可能有多余空格）。

#### 核心思想

**方法一：split + 逆序（推荐初学者用）**
```python
def reverseWords(s: str) -> str:
    """
    核心就一行：
    split() → 自动分割单词、清理多余空格
    reversed() → 反转列表
    " ".join() → 用空格拼接
    """
    return " ".join(reversed(s.split()))

# 不熟悉的话，拆开看：
words = s.split()          # ['the', 'sky', 'is', 'blue']
rev_words = reversed(words)  # ['blue', 'is', 'sky', 'the']
result = " ".join(rev_words) # "blue is sky the"
```

**s.split() 的魔力**：
```python
s = "  hello   world  "
s.split()  # ['hello', 'world']  — 自动处理前导/尾随/中间多个空格！
s.split(' ')  # ['', '', 'hello', '', '', 'world', '', ''] — 完全不一样
```

**方法二：原地翻转（进阶，面试加分）**

```python
def reverseWords(s: str) -> str:
    # 三步走：清理 → 整体反转 → 逐个单词反转回来
    cs = list(s.strip())   # 去掉首尾空格，转列表
    
    # 第一步：整体反转
    cs.reverse()           # cs[::-1] 也可以
    
    # 第二步：逐个单词反转回来
    n = len(cs)
    i = 0
    while i < n:
        if cs[i] == ' ':
            i += 1
            continue
        # 找到一个单词的起止
        j = i
        while j < n and cs[j] != ' ':
            j += 1
        # 反转单词 cs[i:j]
        l, r = i, j - 1
        while l < r:
            cs[l], cs[r] = cs[r], cs[l]
            l += 1
            r -= 1
        i = j
    
    # 第三步：清理多余空格（合并多个空格为一个）
    return " ".join("".join(cs).split())
```

**手把手推演**（画图理解）：

```
s = "  hello world  "

① strip() + 转列表:
   cs = ['h','e','l','l','o',' ','w','o','r','l','d']

② 整体反转:
   cs = ['d','l','r','o','w',' ','o','l','l','e','h']

③ 逐个单词反转:
   - 找到单词 "dlrow" → 反转 → "world"
   - 找到单词 "olleh" → 反转 → "hello"
   cs = ['w','o','r','l','d',' ','h','e','l','l','o']

④ join + split + join 清理空格:
   "".join(cs) = "world hello"  → split后合并 = "world hello"
```

---

### 套路3：前缀匹配

#### 适用场景
找一组字符串的共同开头。

#### 核心思想

```python
def longestCommonPrefix(strs: list[str]) -> str:
    """
    拿第一个字符串当"基准"
    用后面的逐个跟基准比
    发现不匹配就缩短基准
    """
    if not strs:
        return ""
    
    prefix = strs[0]      # 基准：第一个字符串
    
    for s in strs[1:]:    # 从第二个开始遍历
        while not s.startswith(prefix):  # 匹配不上？
            prefix = prefix[:-1]         # 砍掉最后一个字符
            if not prefix:               # 砍没了？
                return ""                # 没有公共前缀
    return prefix
```

**手把手推演**：

```
strs = ["flower", "flow", "flight"]

① prefix = "flower"
   比较 "flow" → startswith("flower")? ❌
   砍掉'r' → "flowe" → startswith? ❌
   砍掉'e' → "flow" → startswith("flow")? ✅ 匹配！

② prefix = "flow"
   比较 "flight" → startswith("flow")? ❌
   砍掉'w' → "flo" → startswith? ❌
   砍掉'o' → "fl" → startswith("fl")? ✅ 匹配！

③ 遍历完了，返回 "fl" ✅
```

#### 另一种思路：纵向扫描

```python
def longestCommonPrefix(strs: list[str]) -> str:
    """
    按列扫描：同时看所有字符串的第i个字符
    """
    if not strs:
        return ""
    
    for i in range(len(strs[0])):        # 按基准的每个字符
        char = strs[0][i]
        for s in strs[1:]:               # 跟所有其他字符串比
            if i >= len(s) or s[i] != char:  # 到头了或不同了
                return strs[0][:i]           # 返回截止到i-1
    return strs[0]  # 全部匹配
```

优点：只要发现第一个不同的字符就立即返回，不用不断创建子串。

---

### 套路4：坐标变换（Z字形）

#### 适用场景
需要按特定形状重新排列字符串中的字符。

#### 核心思想：碰碰车模型 🚗

想象一辆碰碰车在行之间来回开：
- 从上往下开（down方向）
- 撞到顶（第0行）或撞到底（第numRows-1行），掉头
- 每经过一个字符，把它扔到对应行的"筐"里

```
numRows = 3 时：

行0:  P     A     H     N     ← 筐0
行1:  A  P  L  S  I  I  G     ← 筐1
行2:  Y     I     R           ← 筐2

字符编号：
P(0) A(1) Y(2) P(3) A(4) L(5) I(6) S(7) H(8) I(9) I(10) N(11) G(12)
↓    ↓    ↓    ↓    ↓    ↓    ↓    ↓    ↓    ↓    ↓     ↓     ↓
行0  行1  行2  行1  行0  行1  行2  行1  行0  行1  行2   行1    行0
```

#### 代码模板

```python
def convert(s: str, numRows: int) -> str:
    """
    Z字形变换
    """
    # 特殊情况：一行或一列
    if numRows == 1 or numRows >= len(s):
        return s
    
    rows = [""] * numRows      # 创建 numRows 个"筐"
    cur = 0                    # 当前在哪一行
    direction = 1              # 方向：1向下，-1向上
    
    for ch in s:               # 遍历每个字符
        rows[cur] += ch        # 丢进当前行的筐
        cur += direction       # 往下一个方向走
        # 碰边掉头
        if cur == 0 or cur == numRows - 1:
            direction *= -1
    
    return "".join(rows)       # 所有筐拼起来
```

**面试官可能的追问**：
> **问**：为什么 numRows=1 时要特殊处理？
> **答**：因为 numRows=1 时，cur 始终为0，direction 翻转到 -1 后 cur=-1，会越界。而且一行本身就不需要变换。

---

## 四、高频题精讲

### 题5：最后一个单词的长度（⭐ 送分题）

**题目**：给一个字符串，返回最后一个单词的长度。

```
输入：s = "   fly me   to   the moon  "
输出：4  （最后一个单词是"moon"，长度4）
```

**思路**：从后往前扫，跳过空格→数字母→遇到空格停。

```python
def lengthOfLastWord(s: str) -> int:
    count = 0
    i = len(s) - 1
    
    # 第一步：跳过末尾空格
    while i >= 0 and s[i] == ' ':
        i -= 1
    
    # 第二步：统计单词字母
    while i >= 0 and s[i] != ' ':
        count += 1
        i -= 1
    
    return count
```

**手把手推演**：
```
s = "Hello World"
               ↑
              i=10
① s[10]='d' ≠ ' ' → 开始计数
   count=1, i=9
② s[9]='l' ≠ ' ' → count=2, i=8
③ s[8]='r' ≠ ' ' → count=3, i=7
④ s[7]='o' ≠ ' ' → count=4, i=6
⑤ s[6]='W' ≠ ' ' → count=5, i=5
⑥ s[5]=' ' 是空格 → 停止
返回 count=5 ✅
```

**复杂度**：O(n) 时间，O(1) 空间（原地扫描，不需要额外数据结构）

**如果你只用一行**：
```python
def lengthOfLastWord(s: str) -> int:
    return len(s.strip().split()[-1])
    # s.strip() 去掉首尾空格
    # .split() 分割成单词列表
    # [-1] 取最后一个单词
    # len() 取长度
```

---

### 题6：找子串首次出现位置（⭐⭐ 高频题）

**题目**：在 haystack 中找 needle 第一次出现的位置。

```
haystack = "sadbutsad", needle = "sad"
返回 0（在位置0和6都出现了，返回第一个）
```

#### 解法一：暴力滑窗（推荐，面试够用）

```python
def strStr(haystack: str, needle: str) -> int:
    n, m = len(haystack), len(needle)
    
    for i in range(n - m + 1):          # 滑动窗口起点
        if haystack[i:i + m] == needle:  # 切片比较
            return i
    
    return -1
```

**手把手推演**：
```
haystack = "sadbutsad", needle = "sad" (m=3)

i=0: haystack[0:3] = "sad" == "sad"? ✅ → 返回0

如果是 haystack = "leetcode", needle = "leeto" (m=5)
i=0: haystack[0:5] = "leetc" == "leeto"? ❌
i=1: haystack[1:6] = "eetco" == "leeto"? ❌
... 一直到 i=3（最后）：haystack[3:8] = "tcode" == "leeto"? ❌
返回 -1
```

**复杂度**：O(n×m) 时间（最坏情况），O(1) 空间

#### 解法二：KMP算法（进阶）

KMP 的核心思想：**利用已匹配的信息，不让主串指针回溯**。

```python
def strStr_kmp(haystack: str, needle: str) -> int:
    """KMP算法模板"""
    if not needle:
        return 0
    
    n, m = len(haystack), len(needle)
    
    # 第一步：构建 next 数组（前缀函数）
    # next[i] = needle[0..i] 的最长相等前后缀长度
    nxt = [0] * m
    j = 0
    for i in range(1, m):
        while j > 0 and needle[i] != needle[j]:
            j = nxt[j - 1]           # 有冲突，回退
        if needle[i] == needle[j]:
            j += 1                   # 匹配上了，长度+1
        nxt[i] = j
    
    # 第二步：匹配
    j = 0
    for i in range(n):
        while j > 0 and haystack[i] != needle[j]:
            j = nxt[j - 1]           # 不匹配，跳回去
        if haystack[i] == needle[j]:
            j += 1                   # 匹配上了
        if j == m:                   # 全匹配完
            return i - m + 1
    
    return -1
```

**KMP 为什么快？**
暴力法在匹配失败时需要从头开始（i回退）。
KMP 利用 next 数组知道"前面哪些字符已经匹配过了，不用再比了"。

示例：haystack = "ABCABD", needle = "ABCABE"
```
暴力法匹配到 'D' vs 'E' 失败 → 回到 haystack[1]="B" 重新比
KMP匹配到 'D' vs 'E' 失败 → 前缀 "ABCAB" 有公共前后缀 "AB"
→ 直接把 needle 的前缀 "AB" 对齐到 haystack 已匹配的 "AB" 上
→ 从 haystack[5] 继续比，不用回溯！
```

**OD机考建议**：暴力法足够了。KMP知道原理即可，不必死磕。

---

## 五、性能优化与面试进阶

### 5.1 Python字符串性能陷阱

```python
# ❌ 陷阱1：在循环中拼接字符串
result = ""
for s in strs:
    result += s         # O(n²)！每次拼接都创建新字符串

# ✅ 正确：用列表收集，最后 join
chunks = []
for s in strs:
    chunks.append(s)
result = "".join(chunks)  # O(n)

# ❌ 陷阱2：频繁切片
for i in range(n):
    s = s[1:]  # 每次切片 O(n)，整体 O(n²)

# ✅ 正确：用指针（索引）代替切片
i = 0
while i < n:
    # 操作 s[i] 而不是 s = s[1:]
    i += 1
```

### 5.2 面试常考追问

**Q：字符串切片的时间复杂度是多少？**
> A：O(k)，k是切片长度。不是O(1)。因为它会创建新的字符串并复制字符。

**Q：split() 和 split(' ') 有什么区别？**
> A：split() 默认按任意空白符分割，自动去除连续空白和首尾空白，不会产生空串。
> split(' ') 按单个空格严格分割，连续空格会产生空串。

**Q：reverseVowels 中为什么用 set 而不用 list？**
> A：set 的 in 操作是 O(1)，list 是 O(n)。虽然本题只有10个元音影响不大，但这是好习惯。

---

## 六、OD机考实战指南

### 6.1 字符串题的常见位置

OD机考一般3道题，字符串相关题目通常在：
- **第1题（100分）**：简单字符串操作，如最后单词长度、元音反转
- **第2题（100分）**：中等字符串处理，如反转单词、公共前缀
- **第3题（200分）**：字符串+其他算法组合，如Z字形+模拟

### 6.2 刷题建议

| 优先级 | 题目 | 掌握程度 | 预估时间 |
|--------|------|----------|----------|
| ⭐⭐⭐ | 反转单词 | 面试写法+原地写法都理解 | 15min |
| ⭐⭐⭐ | 最长公共前缀 | 两种写法都掌握 | 10min |
| ⭐⭐⭐ | 找子串出现位置 | 暴力法熟练 | 10min |
| ⭐⭐ | 最后一个单词 | 秒杀 | 3min |
| ⭐⭐ | 元音反转 | 背模板 | 5min |
| ⭐ | Z字形变换 | 理解原理即可 | 15min |

### 6.3 考场技巧

1. **先写暴力**：OD机考不要求最优解，能跑通就行
2. **注意边界**：空字符串、长度为1、全是空格
3. **善用Python特性**：`split()`, `join()`, `startswith()` 都是好工具
4. **调试技巧**：卡住了就 `print()` 中间变量

```python
# 调试模板
def my_solution(s: str) -> str:
    print(f"输入: {s}")
    # ... 你的逻辑 ...
    print(f"中间结果: {something}")
    return result
```

---

## 七、今日作业 🏠

### 必做（20分钟）
1. 手打第1题（元音反转）代码，跑通
2. 手打第5题（最后单词长度）代码，跑通
3. 用 `split()` + `reversed()` 一行写完反转单词

### 选做（30分钟）
4. 理解 KMP 算法的 next 数组构建过程
5. 尝试不用 split() 实现反转单词

### 挑战（有空再做）
6. 用上述4个套路模板，各找一道LeetCode同类题练习

---

> 💡 **明日预告**：Day 9 — 双指针进阶（快慢指针、对撞指针、滑动窗口入门）
> 
> 📝 **学习笔记仓库**：https://gitee.com/year_old/huawei-od-learning

# 📘 Day 10：回文串与滑动窗口 — 从零到精通

> 🎯 **学习目标**：掌握回文串的三种解题套路（中心扩展法、双指针法、DP法）和滑动窗口的核心模板，轻松应对华为OD中的回文和子串匹配题。
> 📅 **华为OD备考 · 第2周 · 第10天** | [返回目录](../README.md)

---

## 目录

- [一、什么是回文串？什么是滑动窗口？](#一什么是回文串什么是滑动窗口)
- [二、基础知识与常见操作大全](#二基础知识与常见操作大全)
- [三、核心套路详解](#三核心套路详解)
- [四、高频题精讲](#四高频题精讲)
- [五、性能优化与常见坑](#五性能优化与常见坑)
- [六、OD机考实战指南](#六od机考实战指南)
- [七、今日作业](#七今日作业)

---

## 一、什么是回文串？什么是滑动窗口？

### 1.1 🎯 回文串 — 正着读反着读都一样

**小白问：什么是回文啊？**
**答：** 想象一面镜子放在文字中间。如果左边的字照到镜子里和右边的字一模一样，这就是回文！比如：

```
"上海自来水来自海上"  ← 正着读和反着读完全一样
"abcba"                ← a→b→c→b→a，对称！
"racecar"              ← 英文里的经典回文
```

**回文的核心特征就是「轴对称」**。从中间对折，左右两边的字符一一对应相等。

**长度分两种：**
- **奇数长度回文**：如 `"aba"`，中心是一个具体的字符 `'b'`
- **偶数长度回文**：如 `"abba"`，中心是两个字符之间的空隙

```python
# 最短的回文：单个字符
"a"       → 是回文 ✅
"aa"      → 是回文 ✅
"ab"      → 不是回文 ❌
"aba"     → 是回文 ✅
"abca"    → 不是回文 ❌（a≠c，b≠c？等一下，a==a但b≠c）
```

### 1.2 🪟 滑动窗口 — 一个会移动的"放大镜"

**小白问：滑动窗口又是啥？**
**答：** 想象你拿着一个放大镜在一行字上从左往右慢慢移动。放大镜的"框"就是窗口，每次移动一个位置，你就看到框内的内容。这个"放大镜"就是滑动窗口！

```
字符串: [a, b, c, d, e, f]
窗口大小 = 3:

第1步: [a, b, c] d, e, f   ← 窗口内容: abc
第2步:  a [b, c, d] e, f   ← 窗口向右滑一格: bcd
第3步:  a, b [c, d, e] f   ← cde
第4步:  a, b, c [d, e, f]  ← def
```

**滑动窗口最擅长解决的问题：**
1. 子串/子数组的某些性质（最大和、包含某字符集等）
2. 固定长度的字符串匹配
3. 字符串的排列/变位词检测

### 1.3 💡 核心套路一句话总结

> **回文问题** = 找中心并向两边扩展 / 双指针从两端向中间夹逼
> **滑动窗口问题** = 固定窗口大小+计数器比对 / 可变窗口大小+条件收缩

---

## 二、基础知识与常见操作大全

### 2.1 🐍 Python 字符串操作速查

在处理回文串和滑动窗口时，下面这些 Python 操作是你最常用的武器：

| 操作 | 写法 | 示例 | 结果 |
|------|------|------|------|
| 字符串反转 | `s[::-1]` | `"abc"[::-1]` | `"cba"` |
| 判断字母数字 | `s[i].isalnum()` | `'a'.isalnum()` | `True` |
|    |   | `'!'.isalnum()` | `False` |
| 转小写 | `s[i].lower()` | `'A'.lower()` | `'a'` |
| 判断字母 | `s[i].isalpha()` | `'3'.isalpha()` | `False` |
| 判断数字 | `s[i].isdigit()` | `'5'.isdigit()` | `True` |
| 字符转ASCII | `ord('a')` | `ord('a')` | `97` |
| ASCII转字符 | `chr(97)` | `chr(97)` | `'a'` |
| 切片取子串 | `s[l:r]` | `"abcde"[1:4]` | `"bcd"` |

### 2.2 ⚠️ 最容易搞混的细节

**1️⃣ 切片 `s[l:r]` 是左闭右开！**
```python
s = "abcde"
s[1:4]  # → "bcd"，不是 "bcde"！
# 索引:  0  1  2  3  4
# 字符:  a  b  c  d  e
#        [ 1  2  3 )  ← 取到索引3之前，不包括4
```

**2️⃣ `s[::-1]` 反转后与原串相等才是回文**
```python
s = "aba"
s == s[::-1]  # True ✅

s = "abca"
s == s[::-1]  # False ❌ (acba ≠ abca)
```

**3️⃣ `ord()` 把小写字母映射到 0-25**
```python
ord('a')  # 97
ord('z')  # 122
# 用 ord(ch) - ord('a') 得到 0-25 的索引
index = ord('c') - ord('a')  # 2
```

### 2.3 🛠️ 常见操作代码大全

#### 操作1：双指针判断回文（最基础）

```python
def is_palindrome(s: str) -> bool:
    """经典双指针从两端向中间夹逼"""
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

# 测试
print(is_palindrome("aba"))     # True
print(is_palindrome("hello"))   # False
print(is_palindrome("a"))       # True
print(is_palindrome(""))        # True
```

#### 操作2：忽略非字母数字的回文判断

```python
def is_palindrome_clean(s: str) -> bool:
    """只比较字母和数字，忽略大小写"""
    left, right = 0, len(s) - 1
    while left < right:
        # 跳过左侧非字母数字
        while left < right and not s[left].isalnum():
            left += 1
        # 跳过右侧非字母数字
        while left < right and not s[right].isalnum():
            right -= 1
        # 比较（不分大小写）
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True
```

#### 操作3：中心扩展找所有回文

```python
def count_palindromes(s: str) -> int:
    """统计字符串中所有回文子串的数量"""
    n = len(s)
    count = 0
    for center in range(n):
        # 奇数长度回文：中心是 center
        l = r = center
        while l >= 0 and r < n and s[l] == s[r]:
            count += 1
            l -= 1
            r += 1
        # 偶数长度回文：中心是 center 和 center+1 之间
        l, r = center, center + 1
        while l >= 0 and r < n and s[l] == s[r]:
            count += 1
            l -= 1
            r += 1
    return count
```

#### 操作4：固定长度滑动窗口

```python
def sliding_window_fixed(s: str, k: int):
    """固定窗口大小的滑动窗口模板"""
    n = len(s)
    if n < k:
        return []
    
    results = []
    # 初始化第一个窗口
    window = s[:k]
    # 处理第一个窗口...
    
    # 滑动过程
    for i in range(k, n):
        # 移除左侧字符 s[i-k]
        # 加入右侧字符 s[i]
        # 处理当前窗口...
        pass
    return results
```

---

## 三、核心套路详解

### 套路1：🔍 双指针夹逼法（Two-Pointer Palindrome）

#### 适用场景
- 判断一个字符串**整体**是否是回文
- 判断子串 `s[l:r+1]` 是否是回文
- 允许删除字符后的回文判断

#### 核心思想
```
源串:  [a] [b] [c] [b] [a]
       ↑                ↑
      left             right
          ↑         ↑
         left      right
              ↑↑
            left==right → 全通过，是回文 ✅
```

用两个指针分别从**最左和最右**出发，每步比较两个字符：
- ✅ 相等 → 向中间移动
- ❌ 不相等 → 不是回文，直接返回 False
- 相遇重叠 → 所有字符都匹配，是回文

#### 代码模板（直接背诵！）

```python
def is_palindrome(s: str) -> bool:
    """🌟 模板：双指针判断回文"""
    l, r = 0, len(s) - 1
    while l < r:
        if s[l] != s[r]:
            return False
        l += 1
        r -= 1
    return True
```

#### 带"跳过"的加强版模板

```python
def is_palindrome_with_skip(s: str) -> bool:
    """🌟 模板：允许跳过非字母数字字符"""
    l, r = 0, len(s) - 1
    while l < r:
        # 跳过不关心的字符
        while l < r and not s[l].isalnum():
            l += 1
        while l < r and not s[r].isalnum():
            r -= 1
        # 比较
        if s[l].lower() != s[r].lower():
            return False
        l += 1
        r -= 1
    return True
```

#### 允许最多删除一个字符的模板

```python
def valid_palindrome(s: str) -> bool:
    """🌟 模板：最多删一个字符，判断能否成为回文"""
    def check(l: int, r: int) -> bool:
        """辅助函数：判断 s[l..r] 是否是回文"""
        while l < r:
            if s[l] != s[r]:
                return False
            l += 1
            r -= 1
        return True
    
    l, r = 0, len(s) - 1
    while l < r:
        if s[l] != s[r]:
            # 两个分支：删左边 or 删右边
            return check(l + 1, r) or check(l, r - 1)
        l += 1
        r -= 1
    return True
```

#### 手把手推演

```
输入: s = "abca"

初始化: l=0, r=3
        a  b  c  a
        0  1  2  3
        ↑        ↑
        l        r

Step 1: s[l]='a' == s[r]='a' ✅ → l=1, r=2
        a  b  c  a
            ↑  ↑
            l  r

Step 2: s[l]='b' != s[r]='c' ❌ → 尝试两种删除
        分支1: check(2, 2) → s[2..2] = "c"，长度为1，是回文 ✅
        分支2: check(1, 1) → s[1..1] = "b"，长度为1，是回文 ✅
        有一个为True → 返回 True

输出: True ✅（删除 'b' 或 'c' 都能得到回文）
```

---

### 套路2：🎯 中心扩展法（Center Expansion）

#### 适用场景
- 统计回文子串的数量
- 寻找**最长**回文子串
- 不能简单地"判断整体回文"，需要考察所有子串

#### 核心思想

每个回文都有**一个中心**（奇数长度）或**两个中心**（偶数长度）：

```
奇数长度回文 "aba":    偶数长度回文 "abba":
    a  b  a               a  b  b  a
       ↑                      ↑↑
     中心                 两个中心
```

从每个可能的"中心"出发，向左右两边扩展，只要两边的字符相等，就说明找到了一个新的回文子串。

**总共有多少个可能的中心？**
- 对于长度为 n 的字符串
- 奇数中心：n 个（每个字符）
- 偶数中心：n-1 个（每两个字符之间）
- **总数：2n - 1 个中心**

```
举例 s = "abc":
奇数中心：a, b, c           → 3个
偶数中心：a和b之间, b和c之间  → 2个
总共：5个中心
```

#### 代码模板（直接背诵！）

```python
def center_expand(s: str, l: int, r: int) -> str:
    """从(l,r)向两边扩展，返回能扩展的最长回文子串"""
    while l >= 0 and r < len(s) and s[l] == s[r]:
        l -= 1
        r += 1
    # 注意：退出循环时 s[l]!=s[r]，回文范围是 (l+1) 到 (r-1)
    return s[l + 1:r]

def longest_palindrome(s: str) -> str:
    """🌟 模板：中心扩展法求最长回文子串"""
    longest = ""
    for i in range(len(s)):
        # 奇数长度
        odd = center_expand(s, i, i)
        # 偶数长度
        even = center_expand(s, i, i + 1)
        if len(odd) > len(longest):
            longest = odd
        if len(even) > len(longest):
            longest = even
    return longest

def count_palindromes(s: str) -> int:
    """🌟 模板：中心扩展法统计回文子串数量"""
    n = len(s)
    count = 0
    for i in range(n):
        # 奇数中心
        l, r = i, i
        while l >= 0 and r < n and s[l] == s[r]:
            count += 1
            l -= 1
            r += 1
        # 偶数中心
        l, r = i, i + 1
        while l >= 0 and r < n and s[l] == s[r]:
            count += 1
            l -= 1
            r += 1
    return count
```

#### 手把手推演

```
输入: s = "aaa"，统计回文子串数量

中心扩展过程：

=== 中心 i=0（奇数）===
l=0, r=0: s[0]='a' == s[0]='a' ✅ → count=1, l=-1, r=1 → 越界退出

=== 中心 i=0（偶数）===
l=0, r=1: s[0]='a' == s[1]='a' ✅ → count=2, l=-1, r=2 → 越界退出

=== 中心 i=1（奇数）===
l=1, r=1: s[1]='a' == s[1]='a' ✅ → count=3, l=0, r=2
l=0, r=2: s[0]='a' == s[2]='a' ✅ → count=4, l=-1, r=3 → 越界退出

=== 中心 i=1（偶数）===
l=1, r=2: s[1]='a' == s[2]='a' ✅ → count=5, l=0, r=3 → 越界退出

=== 中心 i=2（奇数）===
l=2, r=2: s[2]='a' == s[2]='a' ✅ → count=6, l=1, r=3 → 越界退出

=== 中心 i=2（偶数）===
l=2, r=3: r=3 越界退出

输出: 6 ✅
回文子串: "a"(3个), "aa"(2个), "aaa"(1个) → 共6个
```

---

### 套路3：🪟 滑动窗口 + 计数（Sliding Window with Counting）

#### 适用场景
- 判断一个字符串的**排列/变位词**是否出现在另一个字符串中
- 字符串包含问题的快速判断
- 固定长度子串的统计性质

#### 核心思想

当我们需要判断一个固定长度的窗口是否包含某个字符集合时，可以用**计数数组**来快速比较。

```
s1 = "ab"  → 计数: a=1, b=1
s2 = "eidbaooo"

窗口大小 = len(s1) = 2

窗口滑动过程：
[e, i]    → e=1, i=1     ≠ s1计数 ❌
[i, d]    → i=1, d=1     ≠ ❌
[d, b]    → d=1, b=1     ≠ ❌
[b, a]    → b=1, a=1     == s1计数 ✅ 找到！
```

#### 代码模板（直接背诵！）

```python
def check_inclusion(s1: str, s2: str) -> bool:
    """🌟 模板：判断 s2 是否包含 s1 的排列"""
    n1, n2 = len(s1), len(s2)
    if n1 > n2:
        return False
    
    # 计数数组（小写字母范围）
    cnt1 = [0] * 26  # s1 的计数
    cnt2 = [0] * 26  # 当前窗口的计数
    
    # 统计 s1
    for ch in s1:
        cnt1[ord(ch) - ord('a')] += 1
    
    # 滑动窗口
    for i, ch in enumerate(s2):
        # 加入新字符
        cnt2[ord(ch) - ord('a')] += 1
        # 移除旧字符（窗口大小固定为 n1）
        if i >= n1:
            cnt2[ord(s2[i - n1]) - ord('a')] -= 1
        # 比较两个计数数组
        if cnt1 == cnt2:
            return True
    
    return False
```

#### 为什么用数组而不用哈希表？
- 题目限定小写字母 → 只有26种字符
- 数组比较比哈希表快得多（`cnt1 == cnt2` 是 O(26) = O(1)）
- 数组内存连续，缓存友好

#### 手把手推演

```
输入: s1 = "ab", s2 = "eidbaooo"

初始化:
cnt1 = [1, 1, 0, 0, ..., 0]  (a=1, b=1)
cnt2 = [0, 0, 0, 0, ..., 0]

遍历 s2: "e i d b a o o o"

i=0, ch='e': cnt2[e_idx=4] += 1 → [0,0,0,0,1,0,...]  i<2 不移除
i=1, ch='i': cnt2[i_idx=8] += 1 → [0,0,0,0,1,0,0,0,1,0,...]  i<2 不移除
  cnt1==cnt2? No

i=2, ch='d': cnt2[d_idx=3] += 1 → [0,0,0,1,1,0,0,0,1,0,...]
  移除 s2[0]='e': cnt2[e_idx=4] -= 1 → [0,0,0,1,0,0,0,0,1,0,...]
  cnt1==cnt2? No

i=3, ch='b': cnt2[b_idx=1] += 1 → [0,1,0,1,0,0,0,0,1,0,...]
  移除 s2[1]='i': cnt2[i_idx=8] -= 1 → [0,1,0,1,0,0,0,0,0,0,...]
  cnt1==cnt2? No

i=4, ch='a': cnt2[a_idx=0] += 1 → [1,1,0,1,0,0,0,0,0,0,...]
  移除 s2[2]='d': cnt2[d_idx=3] -= 1 → [1,1,0,0,0,0,0,0,0,0,...]
  cnt1==cnt2? → [1,1,0...] == [1,1,0...] → True ✅ 找到！

输出: True ✅
```

---

### 套路4：🔄 可变窗口 + 条件收缩（Advanced Sliding Window）

#### 适用场景
- 找最长/最短满足某条件的子串
- 无重复字符的最长子串
- 包含所有目标字符的最短子串

#### 通用模板

```python
def sliding_window(s: str) -> int:
    """🌟 模板：可变大小滑动窗口"""
    n = len(s)
    window = {}  # 或 [0]*128 等
    left = 0
    result = 0  # 或其他初始值
    
    for right in range(n):
        # 1. 扩展窗口（加入 s[right]）
        add_char(s[right], window)
        
        # 2. 收缩窗口（当窗口不满足条件时）
        while not is_valid(window):
            remove_char(s[left], window)
            left += 1
        
        # 3. 更新答案
        result = max(result, right - left + 1)
    
    return result
```

---

## 四、高频题精讲

### 题1：验证回文串（⭐ 简单）

**题目：** 给定一个字符串 `s`，只考虑字母和数字字符，忽略大小写，判断它是否是回文串。

```
示例1: "A man, a plan, a canal: Panama" → True
示例2: "race a car" → False
示例3: " " → True
```

**思路：** 双指针从两端夹逼。非字母数字字符直接跳过，字母数字忽略大小写比较。

**代码：**

```python
def isPalindrome(s: str) -> bool:
    """双指针 + 跳过非字母数字字符"""
    l, r = 0, len(s) - 1
    while l < r:
        # 左指针跳过非字母数字
        while l < r and not s[l].isalnum():
            l += 1
        # 右指针跳过非字母数字
        while l < r and not s[r].isalnum():
            r -= 1
        # 比较（统一转小写）
        if s[l].lower() != s[r].lower():
            return False
        l += 1
        r -= 1
    return True
```

**手把手推演：**

```
输入: "A man, a plan, a canal: Panama"
                     ↓
简化: amanaplanacanalpanama（去除非字母数字 + 转小写）

A man, a plan, a canal: Panama
↑                             ↑
l=0                           r=29
s[l]='A'.lower()='a'          s[r]='a'          相等 ✅

A man, a plan, a canal: Panama
 ↑                           ↑
l=1                          r=28
s[l]=' ' → 不是字母数字，跳过
 ↑                          ↑
l=2                         r=28
s[l]='m'                    s[r]='m'           相等 ✅

...（持续向中间移动）

A man, a plan, a canal: Panama
                     ↑↑
                    l,r 相遇 → 全部通过 ✅

输出: True
```

**复杂度：** O(n) 时间，O(1) 空间

---

### 题2：验证回文串 II（⭐⭐ 高频）

**题目：** 给定一个非空字符串 `s`，**最多删除一个字符**，判断是否能成为回文串。

```
示例1: "aba" → True（本来就是回文）
示例2: "abca" → True（删除'c'得到"aba"或删除'b'得到"aca"）
示例3: "abc" → False（删一个字符后："bc","ac","ab"都不是回文）
```

**思路：** 双指针遇到不匹配时，有两种选择——删左字符或删右字符。分别检查剩余子串是否回文，只要一个成功即可。

**代码：**

```python
def validPalindrome(s: str) -> bool:
    def check(l: int, r: int) -> bool:
        """判断 s[l:r+1] 是否是回文"""
        while l < r:
            if s[l] != s[r]:
                return False
            l += 1
            r -= 1
        return True
    
    l, r = 0, len(s) - 1
    while l < r:
        if s[l] != s[r]:
            # 关键：尝试删左边或删右边
            return check(l + 1, r) or check(l, r - 1)
        l += 1
        r -= 1
    return True
```

**手把手推演：**

```
输入: "abca"

l=0, r=3: s[0]='a' == s[3]='a' ✅ → l=1, r=2
l=1, r=2: s[1]='b' != s[2]='c' ❌

分支1: check(2, 3) → s[2:4] = "ca"
  l=2, r=3: s[2]='c' != s[3]='a' ❌ → 返回 False

分支2: check(1, 2) → s[1:3] = "bc"
  l=1, r=2: s[1]='b' != s[2]='c' ❌ → 返回 False

等等！让我重新检查...

实际上对于 "abca":
- 删除 b: "aca" → a==a ✅ → 是回文
- 删除 c: "aba" → a==a, b==b ✅ → 是回文

让我重新推演：

l=0, r=3: a==a ✅ → l=1, r=2
l=1, r=2: b!=c ❌

分支1 (check(2, 3)): s="ca"
  l=2, r=3: c!=a ❌ → False

分支2 (check(1, 2)): s="bc"  
  l=1, r=2: b!=c ❌ → False

Hmm... 看起来"abca"不能通过删除一个字符成为回文？

等等，让我用 Python 试一下：
"abca" 删除 'c' → "aba" → 是回文 ✅
"abca" 删除 'b' → "aca" → 是回文 ✅

所以正确的结果应该是 True！

问题出在哪里？让我重新推演 check 的参数：

check(l+1, r)  → check(2, 2)  NOT check(2, 3)!
check(l, r-1)  → check(1, 1)  NOT check(1, 2)!

因为当 l=1, r=2 时：
- 删左边（删 s[1]='b'）：检查 s[2:3] = "c" → 长度为1，是回文 ✅
- 删右边（删 s[2]='c'）：检查 s[1:2] = "b" → 长度为1，是回文 ✅

check(l+1, r) 中的参数是 (2, 2)，对应 s[2..2]="c"
check(l, r-1) 中的参数是 (1, 1)，对应 s[1..1]="b"

哦！我推演错了。check(l, r) 检查的是 s[l:r+1]（注意是闭区间）。

check(2, 2): l=2, r=2, while l<r 不执行 → 返回 True ✅
check(1, 1): l=1, r=1, while l<r 不执行 → 返回 True ✅

所以 check(l+1, r) or check(l, r-1) = True ✅

输出: True ✅
```

**复杂度：** O(n) 时间（整个串只遍历一次，最多额外检查一个子串），O(1) 空间

**⚠️ 易错点：** 注意 check 的参数是 (l+1, r) 和 (l, r-1)，不是 (l+1, r-1)！

---

### 题3：回文子串（⭐⭐⭐ 高频）

**题目：** 统计字符串中所有回文子串的个数。不同起始位置或结束位置的子串就算不同的回文子串。

```
示例1: "abc" → 3（"a","b","c"）
示例2: "aaa" → 6（"a"×3, "aa"×2, "aaa"×1）
```

**思路：** 中心扩展法。每个字符（奇数中心）和每对相邻字符之间（偶数中心）作为中心向外扩展，每成功扩展一步就计数+1。

**代码：**

```python
def countSubstrings(s: str) -> int:
    """中心扩展法统计回文子串"""
    n = len(s)
    ans = 0
    
    for i in range(n):
        # 奇数长度：中心为 i
        l = r = i
        while l >= 0 and r < n and s[l] == s[r]:
            ans += 1
            l -= 1
            r += 1
        
        # 偶数长度：中心为 i 和 i+1 之间
        l, r = i, i + 1
        while l >= 0 and r < n and s[l] == s[r]:
            ans += 1
            l -= 1
            r += 1
    
    return ans
```

**手把手推演：**

```
输入: "abc"

i=0 奇数: l=0,r=0 → 'a'=='a' ✅ ans=1 → l=-1,r=1 越界
i=0 偶数: l=0,r=1 → 'a'!='b' ❌ → 退出
i=1 奇数: l=1,r=1 → 'b'=='b' ✅ ans=2 → l=0,r=2 → 'a'!='c' ❌
i=1 偶数: l=1,r=2 → 'b'!='c' ❌
i=2 奇数: l=2,r=2 → 'c'=='c' ✅ ans=3 → l=1,r=3 越界
i=2 偶数: l=2,r=3 → r=3 越界

ans=3 ✅
```

**复杂度：** O(n²) 时间（2n-1个中心，每个中心可能扩展O(n)次），O(1) 空间

---

### 题4：最长回文子串（⭐⭐⭐ 必考）

**题目：** 给定一个字符串 `s`，找到 `s` 中最长的回文子串。

```
示例1: "babad" → "bab" 或 "aba"
示例2: "cbbd" → "bb"
示例3: "a" → "a"
示例4: "ac" → "a" 或 "c"
```

**思路：** 中心扩展法。遍历每个中心，向两边扩展得到回文，记录最长的一个。

**代码：**

```python
def longestPalindrome(s: str) -> str:
    """中心扩展法求最长回文子串"""
    def expand(l: int, r: int) -> str:
        """从(l,r)扩展，返回找到的最长回文子串"""
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1
            r += 1
        # 注意退出时 l 和 r 已经越界了
        # 回文范围是 [l+1, r-1]
        return s[l + 1:r]
    
    longest = ""
    for i in range(len(s)):
        # 奇数长度回文
        odd = expand(i, i)
        # 偶数长度回文
        even = expand(i, i + 1)
        
        if len(odd) > len(longest):
            longest = odd
        if len(even) > len(longest):
            longest = even
    
    return longest
```

**手把手推演：**

```
输入: "babad"
n=5

i=0: odd=expand(0,0): "b"     → "b"
     even=expand(0,1): "ba" ❌ → ""  (b!=a)
     longest="b"

i=1: odd=expand(1,1): "b"→"aba"  (b==b,a==a) → "aba"
     even=expand(1,2): "ab" ❌ → "" (a!=b)
     longest="aba" (len=3 > len=1)

i=2: odd=expand(2,2): "b"→"bab" (b==b,a==a,d==d? no) → "bab"
     even=expand(2,3): "ab" ❌ → "" (a!=b)
     longest="aba" (len=3, "bab"也是3)

i=3: odd=expand(3,3): "a"→"ada"? s[3]='a',s[2]='b'≠'a' ❌ → "a"
     even=expand(3,4): "ad" ❌ → "" (a!=d)
     longest="aba"

i=4: odd=expand(4,4): "d" → "d"
     even=expand(4,5): 越界 → ""
     longest="aba"

输出: "aba"
```

**复杂度：** O(n²) 时间，O(1) 空间（比动态规划的 O(n²) 空间好得多！）

---

### 题5：字符串的排列（⭐⭐⭐ OD常考）

**题目：** 给你两个字符串 `s1` 和 `s2`，判断 `s2` 是否包含 `s1` 的排列。`s1` 的排列之一是 `s2` 的子串。

```
示例1: s1="ab", s2="eidbaooo" → True（"ba" 是 "ab" 的排列）
示例2: s1="ab", s2="eidboaoo" → False
```

**思路：** 固定窗口大小为 `len(s1)` 的滑动窗口 + 计数数组。每次窗口滑动时，加入新字符、移除旧字符，比较两个计数数组。

**代码：**

```python
def checkInclusion(s1: str, s2: str) -> bool:
    """滑动窗口 + 计数数组"""
    n1, n2 = len(s1), len(s2)
    if n1 > n2:
        return False
    
    cnt1 = [0] * 26
    cnt2 = [0] * 26
    
    # 统计 s1
    for ch in s1:
        cnt1[ord(ch) - ord('a')] += 1
    
    # 在 s2 上滑动
    for i, ch in enumerate(s2):
        # 加入当前字符
        cnt2[ord(ch) - ord('a')] += 1
        
        # 窗口超出大小，移除左侧字符
        if i >= n1:
            cnt2[ord(s2[i - n1]) - ord('a')] -= 1
        
        # 比较计数
        if cnt1 == cnt2:
            return True
    
    return False
```

**手把手推演：**

```
输入: s1="ab", s2="eidbaooo"
预处理: cnt1 = [1, 1, 0, 0, ..., 0]

遍历 s2:
i=0 ch='e': cnt2[e]=1 → 不移除 → cnt1!=cnt2
i=1 ch='i': cnt2[i]=1 → 不移除 → cnt1!=cnt2
i=2 ch='d': cnt2[d]=1, 移除 s2[0]='e': cnt2[e]=0 → cnt1!=cnt2
i=3 ch='b': cnt2[b]=1, 移除 s2[1]='i': cnt2[i]=0 → cnt1!=cnt2
i=4 ch='a': cnt2[a]=1, 移除 s2[2]='d': cnt2[d]=0
           → cnt2=[1,1,0...]  == cnt1=[1,1,0...] → True ✅

输出: True ✅
```

**复杂度：** O(n₂) 时间（仅需遍历一次 s2），O(1) 空间（固定大小数组）

---

### 题6：反转字符串（⭐ 基础）

**题目：** 将字符数组原地反转。

```
输入: ["h","e","l","l","o"]
输出: ["o","l","l","e","h"]
```

**思路：** 左右指针交换字符。

**代码：**

```python
def reverseString(s: list[str]) -> None:
    """双指针原地反转"""
    l, r = 0, len(s) - 1
    while l < r:
        s[l], s[r] = s[r], s[l]  # Python 优雅交换
        l += 1
        r -= 1
    # 无需返回值，原地修改
```

**手把手推演：**

```
输入: ["h","e","l","l","o"]
l=0, r=4: swap "h"↔"o" → ["o","e","l","l","h"]  l=1, r=3
l=1, r=3: swap "e"↔"l" → ["o","l","l","e","h"]  l=2, r=2
l=2, r=2: l<r 不成立 → 退出

输出: ["o","l","l","e","h"] ✅
```

**复杂度：** O(n) 时间，O(1) 空间

---

## 五、性能优化与常见坑

### 5.1 ❌ 常见错误对比

#### 错误1：忽略非字母数字字符

```python
# ❌ 错误写法
def isPalindrome(s: str) -> bool:
    return s == s[::-1]  # 没处理标点符号和大小写！

# ✅ 正确写法
def isPalindrome(s: str) -> bool:
    cleaned = [c.lower() for c in s if c.isalnum()]
    return cleaned == cleaned[::-1]
```

#### 错误2：字符串拼接造成O(n²)

```python
# ❌ 错误写法：字符串不可变，每次拼接都创建新对象
def clean_string(s: str) -> str:
    result = ""
    for c in s:
        if c.isalnum():
            result += c  # O(n²)！每次拼接都复制
    return result.lower()

# ✅ 正确写法：用列表收集
def clean_string(s: str) -> str:
    chars = [c.lower() for c in s if c.isalnum()]
    return "".join(chars)  # O(n)，一次性拼接
```

#### 错误3：中心扩展的边界条件

```python
# ❌ 错误写法：退出后直接返回 s[l:r]
def expand(l, r, s):
    while l >= 0 and r < len(s) and s[l] == s[r]:
        l -= 1
        r += 1
    return s[l:r]  # 错误！此时 s[l]!=s[r]，应该返回 s[l+1:r]

# ✅ 正确写法
def expand(l, r, s):
    while l >= 0 and r < len(s) and s[l] == s[r]:
        l -= 1
        r += 1
    return s[l+1:r]  # 回文范围是 (l+1) 到 (r-1)
```

### 5.2 ⚡ 性能对比

| 方法 | 最长回文子串 | 统计回文子串 | 验证回文串 | 空间 |
|------|------------|------------|-----------|------|
| 中心扩展法 | O(n²) | O(n²) | — | O(1) |
| 动态规划 | O(n²) | O(n²) | — | O(n²) |
| Manacher | O(n) | O(n) | — | O(n) |
| 双指针夹逼 | — | — | O(n) | O(1) |

**💡 小白的建议：**
- 回文验证 → 用双指针夹逼
- 最长回文/回文计数 → 用中心扩展法（比DP好理解，且空间O(1)）
- Manacher 算法面试不常见，作为扩展了解即可

### 5.3 ❓ 小白常见疑问

**Q：为什么中心扩展法的时间复杂度是 O(n²)？**
> 有 2n-1 个中心点，每个最坏情况扩展 O(n) 次，所以 O((2n-1)×n/2) ≈ O(n²)

**Q：滑动窗口为什么不用字典（dict）而用数组？**
> 当字符范围固定且很小（比如26个小写字母）时，数组比较 `==` 很快且直观。字典比较需要逐个 key 对比，性能较差。但如果字符集不确定（如Unicode），可以用 `collections.Counter`。

**Q：中心扩展和动态规划哪个好？**
> 中心扩展法更好！同样 O(n²) 时间，但中心扩展法空间 O(1)，动态规划需要 O(n²) 空间。不过中心扩展法不能处理"最长不重复子串"这类问题——每种方法有自己擅长的领域。

**Q：华为OD考不考Manacher算法？**
> 基本不考。华为OD更注重基础算法的应用能力，中心扩展法已经完全够用。Manacher 算法代码复杂且容易写错，面试不是最优选择。

---

## 六、OD机考实战指南

### 6.1 🎯 本主题在OD中的出题特点

**回文串题型：**
- 100分题常考：验证回文串、验证回文串II（删一个字符）
- 100分题也常出现：最长回文子串、回文子串计数
- 200分题会结合其他知识点：如回文+DP、回文+字符串处理

**滑动窗口题型：**
- 100分题必考方向：字符串排列/变位词检测
- 常与哈希表、双指针结合考察
- 2024年OD考试中，滑动窗口出现频率大幅上升

### 6.2 ⏰ 时间分配建议

| 题型 | 建议时间 | 优先级 |
|------|---------|-------|
| 验证回文串（双指针） | 5分钟 | ⭐⭐⭐ 必会 |
| 验证回文串 II | 8分钟 | ⭐⭐⭐ 必会 |
| 最长回文子串 | 12分钟 | ⭐⭐ 高频 |
| 字符串的排列（滑动窗口） | 10分钟 | ⭐⭐ 高频 |
| 反转字符串 | 3分钟 | ⭐⭐ 基础 |
| 回文子串计数 | 10分钟 | ⭐ 进阶 |

### 6.3 🏆 考场策略

1. **先写暴力/简单解法** → 拿到基础分再优化
2. **验证回文串**记住双指针模板（写对，不要超时）
3. **滑动窗口题**记住固定窗口的计数数组模板
4. **Python 的优势**：字符串操作简洁，`isalnum()`、`lower()`、切片都是C实现的，很快
5. **边界情况**：空字符串、单字符、全相同字符、全部非字母数字

**💡 考试小贴士：**
- 如果时间紧张，中心扩展法的高复杂度 O(n²) 在 n≤1000 时完全够用（OD考试的数据范围一般不会太大）
- 滑动窗口的数组比较 `cnt1 == cnt2` 在 Python 中是非常快的操作

---

## 七、今日作业 🏠

### ⭐ 必做题（预计30分钟）

**题1：验证回文串**
- 链接：[LeetCode 125](https://leetcode.cn/problems/valid-palindrome/)
- 要求：用双指针法，不依赖额外空间
- 自测：`"A man, a plan, a canal: Panama"` → True，`"race a car"` → False

**题2：反转字符串**
- 链接：[LeetCode 344](https://leetcode.cn/problems/reverse-string/)
- 要求：原地修改，O(1) 额外空间
- 自测：`["h","e","l","l","o"]` → `["o","l","l","e","h"]`

### ⭐⭐ 选做题（预计40分钟）

**题3：验证回文串 II**
- 链接：[LeetCode 680](https://leetcode.cn/problems/valid-palindrome-ii/)
- 要求：理解「最多删除一个字符」的含义，用双指针 + 辅助函数
- 自测：`"abca"` → True，`"abc"` → False

**题4：字符串的排列**
- 链接：[LeetCode 567](https://leetcode.cn/problems/permutation-in-string/)
- 要求：用固定窗口滑动，不要用暴力法
- 自测：`s1="ab", s2="eidbaooo"` → True

### ⭐⭐⭐ 挑战题（有时间再做）

**题5：最长回文子串**
- 链接：[LeetCode 5](https://leetcode.cn/problems/longest-palindromic-substring/)
- 要求：用中心扩展法，不用 DP
- 自测：`"babad"` → `"bab"` 或 `"aba"`，`"cbbd"` → `"bb"`

**题6：回文子串计数**
- 链接：[LeetCode 647](https://leetcode.cn/problems/palindromic-substrings/)
- 要求：用中心扩展法计数
- 自测：`"abc"` → 3，`"aaa"` → 6

---

## 📌 今日总结

| 套路 | 一句话记住 | 复杂度 |
|------|-----------|--------|
| 🔍 **双指针夹逼** | 两边向中间走，不等就False | O(n), O(1) |
| 🎯 **中心扩展法** | 2n-1个中心，向两边扩 | O(n²), O(1) |
| 🪟 **滑动窗口+计数** | 固定窗口大小，数组比计数 | O(n), O(1) |
| 🔄 **可变滑动窗口** | 扩展→收缩→更新 | O(n), O(1) |

> 💡 **明天预告**：Day 11 — 双指针与字符串综合应用
>
> 📝 **学习笔记仓库**：https://gitee.com/iiixiyan/huawei-od-learning
>
> 💪 **加油！每天进步一点点，56天通关OD！**

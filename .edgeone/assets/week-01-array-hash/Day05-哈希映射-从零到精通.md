# Day05：哈希映射——从零到精通

> 适用人群：华为OD备考、零基础转码、算法新手
> 学习目标：彻底搞懂 HashMap 的核心用法，掌握6道高频考题
> 预计阅读时间：60-90分钟（含代码实践）

---

## 📌 前言：从"判断存在"到"建立关系"

昨天我们学了 HashSet——一个只能回答"在不在"的简单工具。今天我们要升级到**哈希映射（HashMap）**，它不仅能说"在不在"，还能说"对应的值是什么"。

**生活类比**：
- **HashSet** = 校园门口的门禁系统——只记录"这个人是本校学生吗？"
- **HashMap** = 全班通讯录——不仅知道"张三在不在这个班"，还知道他家的电话号码

**HashMap = HashSet + 额外信息**。HashSet 只存 key，HashMap 存 key→value 的映射关系。

### HashMap 的三大核心应用

| 应用模式 | 生活类比 | 对应题目 |
|---------|---------|---------|
| **计数统计** | 统计班级里每种水果有多少个 | 有效字母异位词、独一无二出现次数 |
| **映射关系** | 把英文名翻译成中文名（一对一） | 同构字符串、单词规律 |
| **分组归类** | 把相同类型的东西放到一个盒子里 | 字母异位词分组 |

### Python 中的 HashMap 实现

```python
# 1. 基本 dict
d = {}                     # 空字典
d = {"apple": 3, "banana": 5}  # 直接初始化
d["orange"] = 2            # 添加/更新
d.get("apple", 0)          # 安全获取，不存在返回默认值
d.pop("banana")            # 删除并返回值

# 2. defaultdict（自动初始化默认值）
from collections import defaultdict
d = defaultdict(list)      # 默认值是空列表
d["fruits"].append("apple")  # 不需要先判断 key 是否存在

# 3. Counter（计数专用）
from collections import Counter
c = Counter("aabbbccc")    # Counter({'b': 3, 'c': 3, 'a': 2})
c["a"]                     # 2
c.most_common(2)           # [('b', 3), ('c', 3)]
```

**💡 defaultdict 的重要性**：在刷题中，80% 的 HashMap 场景用 `defaultdict` 比普通 `dict` 更方便。它省去了 `if key not in d: d[key] = []` 这样的模板代码。

---

## 🧱 第一章：Counter——Python 的计数神器

### 1.1 Counter 是什么？

`Counter` 是 Python 标准库 `collections` 模块中的一个类，专门用来做**计数**。它继承自 `dict`，所以有 dict 的所有功能，还额外加了一些计数专用方法。

### 1.2 基本用法

```python
from collections import Counter

# 从字符串创建
c = Counter("hello world")
print(c)  # Counter({'l': 3, 'o': 2, 'h': 1, 'e': 1, ' ': 1, 'w': 1, 'r': 1, 'd': 1})

# 从列表创建
c = Counter([1, 2, 2, 3, 3, 3])
print(c)  # Counter({3: 3, 2: 2, 1: 1})

# 手动更新
c.update([3, 4, 4])
print(c)  # Counter({3: 4, 2: 2, 4: 2, 1: 1})

# 获取最常见的元素
c.most_common(2)  # [(3, 4), (2, 2)]

# 相减（从第一个计数中减去第二个）
c1 = Counter(a=3, b=1)
c2 = Counter(a=1, b=2)
c1 - c2  # Counter({'a': 2})  — b 的结果为 0 被自动移除
```

### 1.3 为什么 Counter 比手动 dict 好？

**手动写法**：
```python
count = {}
for num in nums:
    if num not in count:
        count[num] = 0
    count[num] += 1
```

**Counter 写法**：
```python
count = Counter(nums)  # 一行搞定！
```

**当需要计数时，永远优先用 Counter。**

---

## 🎯 第二章：核心套路一——"排序/计数作为key分组"（字母异位词分组）

### 2.1 问题：什么是字母异位词？

**字母异位词（Anagram）** 是指由相同字符通过不同排列组成的单词。

```
"eat" 和 "tea" 是字母异位词（都有 e,a,t）
"nat" 和 "tan" 是字母异位词（都有 n,a,t）
"bat" 没有其他词和它一组
```

> 给你一个字符串数组，请你将字母异位词组合在一起。

**示例**：
```python
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"], ["nat", "tan"], ["ate", "eat", "tea"]]
```

### 2.2 核心思想：找到"标准化表示"

怎么判断两个词是不是字母异位词？我们需要找到一个**标准化表示**——任何两个字母异位词应该有相同的标准化表示。

**方法一：排序法**
- 把字符串中的字符按字母顺序排序
- "eat"→排序→"aet"，"tea"→排序→"aet"
- 两个词排序后结果相同 → 它们是字母异位词

**方法二：计数法**
- 统计每个字母出现的次数（26个位置）
- "eat" → [1,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0]
- "tea" → [1,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0]
- 两个计数数组相同 → 它们是字母异位词

**生活类比**：你要把一堆乐高积木按颜色分类。两种方法：
1. **排序法**：把每个积木按颜色排好，然后看哪些积木的"颜色序列"一样
2. **计数法**：统计每种颜色各有几个，颜色分布一样的放一起

### 2.3 算法推演（排序法）

```
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
groups = {}  # key=排序后的字符串, value=[原始字符串列表]

第1步：遍历 "eat"
  sorted("eat") = ['a', 'e', 't'] → "".join → "aet"
  groups["aet"] = ["eat"]

第2步：遍历 "tea"
  sorted("tea") = ['a', 'e', 't'] → "aet"
  groups["aet"] = ["eat", "tea"]

第3步：遍历 "tan"
  sorted("tan") = ['a', 'n', 't'] → "ant"
  groups["ant"] = ["tan"]

第4步：遍历 "ate"
  sorted("ate") = ['a', 'e', 't'] → "aet"
  groups["aet"] = ["eat", "tea", "ate"]

第5步：遍历 "nat"
  sorted("nat") = ['a', 'n', 't'] → "ant"
  groups["ant"] = ["tan", "nat"]

第6步：遍历 "bat"
  sorted("bat") = ['a', 'b', 't'] → "abt"
  groups["abt"] = ["bat"]

结果：groups.values() → [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

### 2.4 代码实现（两种方法）

**方法一：排序法**（推荐，代码简洁）

```python
from collections import defaultdict

def groupAnagrams(strs: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    
    for s in strs:
        # key = 排序后的字符串
        key = "".join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```

**复杂度分析**：
- 时间复杂度：O(n·k·log k)，其中 n 是字符串数量，k 是字符串平均长度
- 空间复杂度：O(n·k)

**方法二：计数法**（避免排序，适合字符串较长的场景）

```python
from collections import defaultdict

def groupAnagrams(strs: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    
    for s in strs:
        # 计数数组：26个位置对应a-z
        count = [0] * 26
        for ch in s:
            # ord(ch) - ord('a') 将 'a'→0, 'b'→1, ... 'z'→25
            count[ord(ch) - ord('a')] += 1
        # 转成元组才能作为字典的 key（列表不可哈希）
        key = tuple(count)
        groups[key].append(s)
    
    return list(groups.values())
```

**复杂度分析**：
- 时间复杂度：O(n·k)，不用排序，只遍历每个字符串一次
- 空间复杂度：O(n·k)

### 2.5 哪种方法更好？

| 维度 | 排序法 | 计数法 |
|------|-------|-------|
| 代码简洁度 | ⭐⭐⭐ 更简洁 | ⭐⭐ 稍长 |
| 时间复杂度 | O(n·k·log k) | O(n·k) 更快 |
| 适用场景 | 字符串较短 | 字符串很长 |
| key 可读性 | "aet" 人类可读 | (1,0,0,0,1,...) 不易读 |

**OD 机考建议**：用排序法。代码短、不易出错，在面试中更易解释。

### 2.6 面试追问

**追问**：如果字符串包含 Unicode 字符（如中文）怎么办？
答：计数法需要改成 `Counter`，或者用排序法（`sorted` 对 Unicode 也有效）。排序法天然支持 Unicode。

**追问**：如果要求 O(n·k) 时间但不想用计数元组呢？
答：可以用质数映射法——给每个字母分配一个质数，两个词的质数乘积相等。但可能遇到大数溢出问题（Python 的任意精度整数可以处理，但速度较慢）。

---

## 🎯 第三章：核心套路二——"统计字符频率"（有效字母异位词）

### 3.1 问题

> 给定两个字符串 s 和 t，判断 t 是否是 s 的字母异位词。

**示例**：
```
s = "anagram", t = "nagaram" → true
s = "rat", t = "car" → false
```

### 3.2 核心思路

**思路一：排序比较**
```python
def isAnagram(s: str, t: str) -> bool:
    return sorted(s) == sorted(t)
```
简单粗暴，但排序是 O(n log n)。

**思路二：计数数组**
因为题目说字符只有小写字母（26个），我们可以用一个长度为 26 的数组计数。

```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    
    count = [0] * 26
    
    # 统计 s 中每个字符出现次数
    for ch in s:
        count[ord(ch) - ord('a')] += 1
    
    # 减去 t 中每个字符出现次数
    for ch in t:
        idx = ord(ch) - ord('a')
        count[idx] -= 1
        if count[idx] < 0:  # t 中的某个字符比 s 多 → 不是异位词
            return False
    
    return True
```

**算法推演**：

```
s = "anagram", t = "nagaram"
count = [0]*26

第1遍（遍历s）：
  'a' → count[0] = 1
  'n' → count[13] = 1
  'a' → count[0] = 2
  'g' → count[6] = 1
  'r' → count[17] = 1
  'a' → count[0] = 3
  'm' → count[12] = 1

第2遍（遍历t）：
  'n' → count[13] = 0
  'a' → count[0] = 2
  'g' → count[6] = 0
  'a' → count[0] = 1
  'r' → count[17] = 0
  'a' → count[0] = 0
  'm' → count[12] = 0

count 全部为 0，返回 True ✅
```

### 3.3 复杂度分析

- 时间复杂度：O(n) —— 只遍历两个字符串各一次
- 空间复杂度：O(1) —— 固定大小的数组（26）

### 3.4 进阶问题：如果包含 Unicode 字符

```python
from collections import Counter

def isAnagram_unicode(s: str, t: str) -> bool:
    return Counter(s) == Counter(t)
```

一行搞定！Counter 自动处理任意字符。

---

## 🎯 第四章：核心套路三——"双射（双向映射）"（同构字符串 + 单词规律）

### 4.1 问题：同构字符串

> 给定两个字符串 s 和 t，判断它们是否是同构的。同构意味着 s 中的字符可以按某种映射关系替换得到 t。

**示例**：
```
s = "egg", t = "add" → true
  映射：e→a, g→d

s = "foo", t = "bar" → false
  因为 o 需要同时映射到 a 和 r，违反一对一规则

s = "paper", t = "title" → true
  映射：p→t, a→i, p→t(OK), e→l, r→e
```

### 4.2 核心思路：为什么要双向映射？

**错误做法**：只用一个方向映射（s→t）

```
s = "abc", t = "fff"
s→t 映射：
  a→f ✅
  b→f ❌ a和b都映射到f，但题目说"不同字符不能映射到同一个字符"
```

光检查 s→t 不够！我们还需要检查 t→s——确保没有两个不同的字符映射到同一个目标。

这就是**双射（Bijection）**——必须是一一对应的关系。

**生活类比**：班级里的座位安排。每个学生坐一个座位（s→t），每个座位上也只有一个学生（t→s）。不能出现两个学生抢一个座位，也不能一个学生坐两个座位。

### 4.3 算法推演

```
s = "egg", t = "add"
map_s_to_t = {}
map_t_to_s = {}

第1步：ch_s='e', ch_t='a'
  e 不在 map_s_to_t 中，a 不在 map_t_to_s 中 → 建立映射
  map_s_to_t['e'] = 'a'
  map_t_to_s['a'] = 'e'

第2步：ch_s='g', ch_t='d'
  g 不在 map_s_to_t 中，d 不在 map_t_to_s 中 → 建立映射
  map_s_to_t['g'] = 'd'
  map_t_to_s['d'] = 'g'

第3步：ch_s='g', ch_t='d'
  g 在 map_s_to_t 中，值为 'd' == ch_t='d' ✅
  d 在 map_t_to_s 中，值为 'g' == ch_s='g' ✅
  不需要更新

遍历结束，返回 True ✅
```

**反例推演**：s = "foo", t = "bar"

```
第1步：ch_s='f', ch_t='b'
  建立映射：f→b, b→f

第2步：ch_s='o', ch_t='a'
  建立映射：o→a, a→o

第3步：ch_s='o', ch_t='r'
  o 在 map_s_to_t 中，值为 'a' != 'r' → 返回 False ❌
```

### 4.4 代码实现

```python
def isIsomorphic(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    
    map_s_to_t = {}  # s 中的字符 → t 中的字符
    map_t_to_s = {}  # t 中的字符 → s 中的字符
    
    for ch_s, ch_t in zip(s, t):
        # 检查两个方向的映射是否一致
        if (ch_s in map_s_to_t and map_s_to_t[ch_s] != ch_t) or \
           (ch_t in map_t_to_s and map_t_to_s[ch_t] != ch_s):
            return False
        
        # 建立双向映射
        map_s_to_t[ch_s] = ch_t
        map_t_to_s[ch_t] = ch_s
    
    return True
```

### 4.5 引申：单词规律（一模一样的问题）

**问题**：给定一种规律 pattern 和一个字符串 s，判断 s 是否遵循相同的规律。

```
pattern = "abba", s = "dog cat cat dog" → true
pattern = "abba", s = "dog cat cat fish" → false
pattern = "aaaa", s = "dog cat cat dog" → false
```

**这就是同构字符串的变体！** 只不过映射的单位从"字符间"变成了"字符→单词"。

```python
def wordPattern(pattern: str, s: str) -> bool:
    words = s.split()
    if len(pattern) != len(words):
        return False
    
    char_to_word = {}
    word_to_char = {}
    
    for ch, word in zip(pattern, words):
        if ch in char_to_word and char_to_word[ch] != word:
            return False
        if word in word_to_char and word_to_char[word] != ch:
            return False
        char_to_word[ch] = word
        word_to_char[word] = ch
    
    return True
```

**双射模板**（适用于所有双射问题）：

```python
def bijection_check(a_list, b_list):
    """检查 a_list 和 b_list 之间是否存在一一对应映射"""
    if len(a_list) != len(b_list):
        return False
    
    a_to_b = {}
    b_to_a = {}
    
    for a, b in zip(a_list, b_list):
        if (a in a_to_b and a_to_b[a] != b) or \
           (b in b_to_a and b_to_a[b] != a):
            return False
        a_to_b[a] = b
        b_to_a[b] = a
    
    return True
```

---

## 🎯 第五章：核心套路四——"Set集合运算"（两个数组交集）

### 5.1 问题

> 给你两个整数数组 nums1 和 nums2，返回 answer，其中 answer[0] 是 nums1 中所有不存在于 nums2 中的不同整数，answer[1] 是 nums2 中所有不存在于 nums1 中的不同整数。

**示例**：
```
nums1 = [1,2,3], nums2 = [2,4,6]
answer = [[1,3], [4,6]]

nums1 = [1,2,3,3], nums2 = [1,1,2,2]
answer = [[3], []]
```

### 5.2 核心思路：Set 差集

Python 的 set 支持"差集"运算：`set1 - set2` 返回所有在 set1 但不在 set2 中的元素。

```python
def findDifference(nums1: list[int], nums2: list[int]) -> list[list[int]]:
    set1 = set(nums1)
    set2 = set(nums2)
    # set1 - set2: 在 nums1 但不在 nums2 中
    # set2 - set1: 在 nums2 但不在 nums1 中
    return [list(set1 - set2), list(set2 - set1)]
```

**一行代码搞定！** ✨

### 5.3 Set 集合运算大全

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a & b       # 交集：{3, 4}
a | b       # 并集：{1, 2, 3, 4, 5, 6}
a - b       # 差集：{1, 2}
b - a       # 差集：{5, 6}
a ^ b       # 对称差集：{1, 2, 5, 6}（等价于 (a-b)|(b-a)）
```

### 5.4 延伸：求两个数组的交集

```python
def intersection(nums1, nums2):
    """返回两个数组的交集（不重复）"""
    return list(set(nums1) & set(nums2))
```

---

## 🎯 第六章：核心套路五——"Counter + Set 判唯一"（独一无二出现次数）

### 6.1 问题

> 给你一个整数数组 arr，如果每个数的出现次数都是独一无二的，就返回 true；否则返回 false。

**示例**：
```
arr = [1,2,2,1,1,3] → true
  1出现3次，2出现2次，3出现1次 → 所有次数都不同

arr = [1,2] → false
  1出现1次，2出现1次 → 出现次数相同
```

### 6.2 核心思路

两步走：
1. 用 Counter 统计每个数字的出现次数
2. 检查这些次数是否互不相同

检查"互不相同"的方法：用 set 去重，如果去重后的 set 大小和原来的次数个数相同，说明没有重复的次数。

```python
from collections import Counter

def uniqueOccurrences(arr: list[int]) -> bool:
    count = Counter(arr)         # 统计每个数字出现次数
    occurrences = count.values() # 所有出现次数
    return len(set(occurrences)) == len(occurrences)
```

**算法推演**：

```
arr = [1, 2, 2, 1, 1, 3]

第1步：Counter(arr)
  count = {1: 3, 2: 2, 3: 1}

第2步：count.values()
  occurrences = [3, 2, 1]

第3步：set(occurrences)
  {1, 2, 3}

第4步：比较大小
  len({1, 2, 3}) = 3 == len([3, 2, 1]) = 3 → True ✅
```

**反例推演**：
```
arr = [1, 2]

第1步：Counter(arr) → {1: 1, 2: 1}
第2步：occurrences = [1, 1]
第3步：set(occurrences) = {1}
第4步：len({1}) = 1 ≠ len([1, 1]) = 2 → False ❌
```

### 6.3 复杂度分析

- 时间复杂度：O(n) —— Counter 是 O(n)，set 操作也是 O(n)
- 空间复杂度：O(n) —— 存储 Counter 和 set

---

## 🧪 综合实战：6道题刷题路线图

### 刷题顺序建议

```
第1步：有效的字母异位词（⭐）
  → 理解字符频率统计
  → 数组/ Counter 两种写法

第2步：独一无二的出现次数（⭐）
  → Counter + Set 组合
  → 学会 count.values()

第3步：两个数组的交集（⭐）
  → Set 集合运算
  → 差集、交集、并集

第4步：同构字符串（⭐⭐）
  → 双射概念入门
  → 双向映射模板

第5步：单词规律（⭐⭐）
  → 双射的变体应用
  → 确认理解同构字符串

第6步：字母异位词分组（⭐⭐⭐）
  → 排序/计数作为 key
  → OD 高频题
```

### 代码模板速查表

| 套路 | 代码模板 | 适用场景 |
|------|---------|---------|
| 计数统计 | `Counter(s)` | 频率统计、字符比较 |
| 分组归类 | `defaultdict(list); key = "".join(sorted(s)); groups[key].append(s)` | 字母异位词分组 |
| 双射检查 | `a_to_b={}; b_to_a={}; if (a in a_to_b and a_to_b[a] != b): return False` | 同构字符串、单词规律 |
| Set 运算 | `set1 - set2`, `set1 & set2` | 数组交集/差集 |
| 判唯一次数 | `len(set(Counter(arr).values())) == len(Counter(arr))` | 独一无二出现次数 |

---

## 💡 OD机考实战建议

### 1. HashMap 高频考点

| 考点 | 频率 | 难度 | 说明 |
|------|------|------|------|
| 字母异位词分组 | ⭐⭐⭐ | 中等 | 必须掌握两种 key 的构造方法 |
| 同构字符串 | ⭐⭐ | 简单 | 双射的典型应用 |
| 单词规律 | ⭐⭐ | 简单 | 同构字符串的变体 |
| 有效字母异位词 | ⭐ | 简单 | 计数数组的基础用法 |
| 独一无二出现次数 | ⭐ | 简单 | Counter 和 Set 的组合 |

### 2. 面试官常问的问题

**Q1：Python 中 dict 的底层实现是什么？**
A：Python 的 dict 基于哈希表实现。Python 3.6+ 使用紧凑哈希表（compact dict），同时保持了插入顺序。查找、插入、删除的平均时间复杂度都是 O(1)。

**Q2：为什么 dict 的 key 必须是不可变类型？**
A：因为哈希表依赖哈希值来确定存储位置。可变对象（如列表）修改后哈希值变化，导致无法正确查找。

**Q3：defaultdict 和普通 dict 有什么区别？**
A：defaultdict 在访问不存在的 key 时，会自动调用工厂函数创建默认值，而不是抛出 KeyError。例如 `defaultdict(list)` 会自动创建空列表。

### 3. 调试技巧

**技巧1：打印 HashMap 的中间状态**
```python
def groupAnagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = "".join(sorted(s))
        groups[key].append(s)
        print(f"当前字符串: {s}, key: {key}, groups: {dict(groups)}")
    return list(groups.values())
```

**技巧2：边缘用例自测**
```python
# 字母异位词分组
print(groupAnagrams(["eat","tea","tan","ate","nat","bat"]))
print(groupAnagrams([""]))       # 空字符串
print(groupAnagrams(["a"]))      # 单个字符

# 同构字符串
print(isIsomorphic("egg", "add"))    # True
print(isIsomorphic("foo", "bar"))    # False
print(isIsomorphic("paper", "title"))  # True
print(isIsomorphic("badc", "baba"))   # False（双射失效）

# 单词规律
print(wordPattern("abba", "dog cat cat dog"))      # True
print(wordPattern("abba", "dog cat cat fish"))     # False
print(wordPattern("aaaa", "dog cat cat dog"))      # False
print(wordPattern("abba", "dog dog dog dog"))      # False
```

### 4. 常见错误大全

| 错误 | 原因 | 修正 |
|------|------|------|
| 分组时忘记初始化列表 | 用普通 dict 没检查 key 是否存在 | 用 `defaultdict(list)` |
| 双射只检查一个方向 | 漏掉 t→s 方向的检查 | 必须双向检查 |
| 用列表作为 dict 的 key | 列表不可哈希 | 转成 tuple 或用 join 转字符串 |
| Counter 误用 | `Counter` 返回 0 不会报错，但可能没意识到 | 用 `in` 运算符检查是否存在 |
| Set 运算顺序搞错 | `set1 - set2` 和 `set2 - set1` 结果不同 | 仔细看清题目要求 |

### 5. 性能优化建议

对于大数组（n > 10^5）：
- 用数组代替 dict（当 key 范围有限时，如小写字母 → 26个）
- 用 `Counter` 替代手动计数
- 用 `defaultdict` 替代手动初始化

---

## 📚 今日总结

### 5个必须刻在脑子里的核心套路

1. **排序/计数作为 key 分组**（字母异位词分组）→ 找到标准化表示
2. **双射（双向映射）**（同构字符串）→ 两个方向都要检查
3. **Counter 计数统计**（有效字母异位词）→ 计数的最优工具
4. **Set 集合运算**（两个数组交集）→ 差集、交集一行搞定
5. **Counter + Set 判唯一性**（独一无二出现次数）→ 去重后比较长度

### 哈希映射 vs 哈希集合对比

| 维度 | HashSet | HashMap |
|------|---------|---------|
| 存储内容 | 只有 key | key→value |
| 典型场景 | 判断存在 | 建立关系/统计 |
| Python 实现 | `set()` | `dict` / `defaultdict` / `Counter` |
| 时间复杂度 | O(1) 增删查 | O(1) 增删查 |

### 一句话总结

> **HashMap 的核心是"从 key 到 value 的映射"——你需要一对一的翻译（双射）、多对一的分组（分组 key）、还是值到值的统计（Counter）？不同的关系选不同的工具。**

### 明日预告

Day6 我们将学习**前缀和与区间合并**。你将学到：
- 一维前缀和：O(1) 时间求任意区间和
- 二维前缀和：容斥原理求子矩阵和
- 区间合并三步法：排序→判断重叠→合并
- 贪心射气球：按右端点排序的最优策略

---

> **学习建议**：今天的题目大多可以用多种方法解决。尝试用两种方法写同一道题（如字母异位词分组用排序法和计数法），加深理解。动手写代码才是真正的学习。

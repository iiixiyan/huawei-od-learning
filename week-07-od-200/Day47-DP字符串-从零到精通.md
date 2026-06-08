# 📘 Day 47：DP字符串 — 从零到精通

> 🎯 **学习目标**：掌握DP字符串核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第7周 · 第47天** | [返回目录](../README.md)

---

## 📑 目录

- [一、核心知识点详解](#一核心知识点详解)
- [二、核心套路/模型](#二核心套路模型)
- [三、高频题精讲](#三高频题精讲)
- [四、面试追问与进阶](#四面试追问与进阶)
- [五、OD机考实战指南](#五od机考实战指南)
- [六、今日作业](#六今日作业)

---

## 一、核心知识点详解

# D47 — DP 字符串 (6题)

---

## 1. Palindromic Substrings (O)
**来源**：[O](https://leetcode.cn/problems/palindromic-substrings/)
**难度**：中等
**题目**：给你一个字符串 `s` ，请你统计并返回这个字符串中 **回文子串** 的数目。

**回文字符串** 是正着读和倒过来读一样的字符串。

**子字符串** 是字符串中的由连续字符组成的一个序列。

**示例 1：**
```
输入：s = "abc"
输出：3
解释：三个回文子串: "a", "b", "c"
```
**示例 2：**
```
输入：s = "aaa"
输出：6
解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"
```
**提示：**

- `1
**思路**：中心扩展法，统计所有回文子串 每个字符或每对相邻字符作为中心向两边扩展
**代码**：
```python
def countSubstrings(s: str) -> int:
    n = len(s)
    ans = 0
    for i in range(n):
        # 奇数长度回文
        l = r = i
        while l >= 0 and r < n and s[l] == s[r]:
            ans += 1
            l -= 1
            r += 1
        # 偶数长度回文
        l, r = i, i + 1
        while l >= 0 and r < n and s[l] == s[r]:
            ans += 1
            l -= 1
            r += 1
    return ans

# 时间 O(n^2), 空间 O(1)
```
## 2. Longest Fibonacci Subsequence (O)
**来源**：[O](https://leetcode.cn/problems/length-of-longest-fibonacci-subsequence/)
**

### 操作速查表

| 操作 | 写法 | 说明 | 复杂度 |
|------|------|------|--------|
| 核心操作1 | `func(x)` | 说明 | O(n) |
| 核心操作2 | `func(x, y)` | 说明 | O(n) |
| 核心操作3 | `func(z)` | 说明 | O(1) |

### 常见API大全

```python
# 该主题常用的Python操作汇总
# 每个操作附示例和结果
```

---

## 二、核心套路/模型

### 套路1：标准模板

```python
# 核心代码模板（可背诵）
def template(args):
    # 初始化
    result = []
    # 主逻辑
    return result
```

**手把手推演**：
```
输入 → 步骤1 → 步骤2 → ... → 输出
```

### 套路2：进阶变体

对于更复杂的场景，可以基于标准模板做以下调整：
- 变体1：...
- 变体2：...

---

## 三、高频题精讲

（具体题目请参考原题链接）

---

## 四、面试追问与进阶

### Q&A
**Q1：这个算法的核心思路是什么？**
A：...

**Q2：时间复杂度如何分析？**
A：...

**Q3：空间复杂度能否优化？**
A：...

### 性能优化
1. ❌ 错误写法：...
2. ✅ 正确写法：...

---

## 五、OD机考实战指南

| 题型 | 出现频率 | 分值 |
|------|---------|------|
| 基础题 | ⭐⭐⭐⭐ | 100分 |
| 进阶题 | ⭐⭐⭐ | 200分 |

### 考场策略
1. **优先写模板**：先写出标准框架
2. **注意边界**：空输入、单元素、极端值
3. **测试验证**：至少测试3个用例
4. **时间分配**：20分钟思考 + 20分钟编码 + 10分钟测试

---

## 六、今日作业

### 必做题（⭐⭐）
1. 题目1 — 链接
2. 题目2 — 链接

### 选做题（⭐⭐⭐）
3. 题目3 — 链接

### 挑战题
4. 题目4 — 链接

> 💪 **第7周第47天，坚持就是胜利！**

### 最长回文子串

```python
def longestPalindrome(s):
    n = len(s)
    dp = [[False]*n for _ in range(n)]
    start, max_len = 0, 1
    for i in range(n):
        dp[i][i] = True
    for i in range(n-1):
        if s[i] == s[i+1]:
            dp[i][i+1] = True
            start, max_len = i, 2
    for length in range(3, n+1):
        for i in range(n-length+1):
            j = i+length-1
            if s[i] == s[j] and dp[i+1][j-1]:
                dp[i][j] = True
                start, max_len = i, length
    return s[start:start+max_len]
```

### 正则表达式匹配
```python
def isMatch(s, p):
    m, n = len(s), len(p)
    dp = [[False]*(n+1) for _ in range(m+1)]
    dp[0][0] = True
    for j in range(2, n+1):
        if p[j-1] == '*':
            dp[0][j] = dp[0][j-2]
    for i in range(1, m+1):
        for j in range(1, n+1):
            if p[j-1] == '.' or p[j-1] == s[i-1]:
                dp[i][j] = dp[i-1][j-1]
            elif p[j-1] == '*':
                dp[i][j] = dp[i][j-2]  # 匹配0次
                if p[j-2] == '.' or p[j-2] == s[i-1]:
                    dp[i][j] = dp[i][j] or dp[i-1][j]  # 匹配1+次
    return dp[m][n]
```

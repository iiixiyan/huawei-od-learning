# 📘 Day 46：DP高级 — 从零到精通

> 🎯 **学习目标**：掌握DP高级核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第7周 · 第46天** | [返回目录](../README.md)

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

# D46 — DP 高级 (6题)

---

## 1. Edit Distance (L75/T150)
**来源**：[](https://leetcode.cn/problems/edit-distance/)
**难度**：中等
**题目**：给你两个单词 `word1` 和 `word2`， *请返回将 `word1` 转换成 `word2` 所使用的最少操作数*  。

你可以对一个单词进行如下三种操作：

- 插入一个字符

- 删除一个字符

- 替换一个字符

**示例 1：**
```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```
**示例 2：**
```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```
**提示：**

- `0
**思路**：**状态定义**：`dp[i][j]` = word1[:i] → word2[:j] 的最小编辑距离
**转移**：
**代码**：
```python
def minDistance(word1: str, word2: str) -> int:
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    for i in range(1, m + 1):
        for j in range

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

> 💪 **第7周第46天，坚持就是胜利！**

### 区间DP

**核心思想**：`dp[i][j]` 表示区间[i,j]的最优解，通过枚举分割点k来转移。

```python
# 区间DP通用模板
n = len(nums)
dp = [[0]*n for _ in range(n)]
for length in range(2, n+1):      # 区间长度从小到大
    for i in range(n-length+1):    # 左端点
        j = i + length - 1         # 右端点
        for k in range(i, j):      # 分割点
            dp[i][j] = max(dp[i][j], dp[i][k] + dp[k+1][j] + cost)
```

### 记忆化搜索（递归版DP）

```python
from functools import lru_cache

@lru_cache(None)
def dfs(state):
    if state == 目标:
        return 基础值
    # 状态转移
    for 选择 in 所有可能选择:
        res = max(res, dfs(新状态) + 收益)
    return res
```

### 状态压缩DP（位运算）

```python
# 用二进制位表示集合状态
# n个元素, 状态范围 0 ~ (1<<n)-1
for mask in range(1 << n):
    for i in range(n):
        if mask & (1 << i):  # i在集合中
            ...处理...
```

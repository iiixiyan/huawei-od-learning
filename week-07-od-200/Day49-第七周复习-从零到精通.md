# 📘 Day 49：第七周复习 — 从零到精通

> 🎯 **学习目标**：掌握第七周复习核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第7周 · 第49天** | [返回目录](../README.md)

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

# D49 — 周复习 (DP 全题型回顾 + 限时小测)

---

## 📋 本周题型总览

| 分类 | 题型 | 代表题目 | 核心技巧 |
|------|------|----------|----------|
| 一维 DP | 线性递推 | Climbing Stairs, Tribonacci | 滚动变量 O(1) |
| 一维 DP | 最值优化 | Min Cost Climbing, Coin Change | 取 min/max |
| 一维 DP | 约束选择 | House Robber I/II | 环形拆线性 |
| 一维 DP | 划分型 | Word Break | 枚举分割点 |
| 一维 DP | 子序列 | LIS | 耐心排序 O(n log n) |
| 一维 DP | 背包 | Partition Equal, Target Sum, Comb Sum IV | 0-1 / 完全背包 |
| 二维 DP | 路径计数 | Unique Paths I/II | 滚动数组 |
| 二维 DP | 路径最值 | Min Path Sum, Triangle | 自底向上 |
| 二维 DP | 双序列 | LCS, Edit Distance, Interleaving | 匹配/不匹配转移 |
| 二维 DP | 正方形 | Maximal Square | 右下角推导 |
| DP 字符串 | 回文 | Palindromic Substrings, LPS | 中心扩展 |
| DP 字符串 | 子序列计数 | Distinct Subsequences | 字符匹配累加 |
| DP 高级 | 股票交易 | Best Time III/IV | 状态机 k 次 |
| DP 高级 | 序列组合 | Longest Fib Subseq | 双索引 map |
| 图/并查集 | 连通性 | Redundant Connection, Similar Groups | 并查集 |
| 图/并查集 | 拓扑排序 | Alien Dictionary, Sequence Reconstruction | 入度 BFS |
| 图/并查集 | 记忆化 DFS | Longest Increasing Path | D

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

### 题 1：最长有效括号
给定一个只包含 `(` 和 `)` 的字符串，找出最长有效（格式正确且连续）括号子串的长度。

```
输入: s = ")()())"
输出: 4  → "()()"
```

```python
def longestValidParentheses(s: str) -> int:
    dp = [0] * len(s)
    ans = 0
    for i in range(1, len(s)):
        if s[i] == ')':
            if s[i - 1] == '(':
                dp[i] = (dp[i - 2] if i >= 2 else 0) + 2
            elif i - dp[i - 1] > 0 and s[i - dp[i - 1] - 1] == '(':
                dp[i] = dp[i - 1] + 2 + (dp[i - dp[i - 1] - 2] if i - dp[i - 1] >= 2 else 0)
            ans = max(ans, dp[i])
    return ans
# 时间 O(n), 空间 O(n)
```

---

### 题 2：最大子数组和
找出和最大的连续子数组，返回最大和。

```
输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6  → [4,-1,2,1]
```

```python
def maxSubArray(nums: list[int]) -> int:
    cur = ans = nums[0]
    for x in nums[1:]:
        cur = max(x, cur + x)
        ans = max(ans, cur)
    return ans
# 时间 O(n), 空间 O(1)  — Kadane 算法
```

---

### 题 3：最长递增子序列（计数）
返回 LIS 的长度和个数。

```python
def findNumberOfLIS(nums: list[int]) -> int:
    n = len(nums)
    length = [1] * n
    count = [1] * n
    for i in range(n):
        for j in range(i):
            if nums[j] < nums[i]:
                if length[j] + 1 > length[i]:
                    length[i] = length[j] + 1
                    count[i] = count[j]
                elif length[j] + 1 == length[i]:
                    count[i] += count[j]
    max_len = max(length)
    return sum(c for l, c in zip(length, count) if l == max_len)
# 时间 O(n²), 空间 O(n)
```

---

### 题 4：完全平方数
给定 n，返回最少需要几个完全平方数（1,4,9,16,…）之和等于 n。

```
输入: n = 12
输出: 3  → 4+4+4
```

```python
def numSquares(n: int) -> int:
    dp = [float('inf')] * (n + 1)
    dp[0] = 0
    for i in range(1, int(n ** 0.5) + 1):
        sq = i * i
        for j in range(sq, n + 1):
            dp[j] = min(dp[j], dp[j - sq] + 1)
    return dp[n]
# 时间 O(n√n), 空间 O(n)
```

---

### 题 5：乘积最大子数组
找出乘积最大的连续子数组。

```
输入: [2,3,-2,4]
输出: 6  → [2,3]
```

```python
def maxProduct(nums: list[int]) -> int:
    cur_max = cur_min = ans = nums[0]
    for x in nums[1:]:
        candidates = (x, cur_max * x, cur_min * x)
        cur_max, cur_min = max(candidates), min(candidates)
        ans = max(ans, cur_max)
    return ans
# 时间 O(n), 空间 O(1)
```

---

### 题 6：戳气球（选做，面试 hard）
有 n 个气球，戳破 i 获得 `nums[i-1]*nums[i]*nums[i+1]`，求最大得分。

```python
def maxCoins(nums: list[int]) -> int:
    nums = [1] + nums + [1]
    n = len(nums)
    dp = [[0] * n for _ in range(n)]
    for length in range(2, n):
        for left in range(0, n - length):
            right = left + length
            for k in range(left + 1, right):
                dp[left][right] = max(dp[left][right],
                    nums[left] * nums[k] * nums[right] + dp[left][k] + dp[k][right])
    return dp[0][n - 1]
# 时间 O(n³), 空间 O(n²)
```

---

## 🎯 复习建议

1. **一维 DP 模板**：状态定义 → 转移方程 → 初始条件 → 压缩空间
2. **二维 DP 模板**：双循环 → 匹配/不匹配分支 → 滚动数组优化
3. **背包问题**：0-1 背包（逆序）vs 完全背包（正序）vs 排列（外金额内物品）
4. **股票问题**：状态机推演，定义好 k 次交易的 buy/sell 数组
5. **回文问题**：中心扩展比 DP 更优（O(1) 空间）
6. **图/并查集**：拓扑排序唯一性判断、连通分量计数


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

> 💪 **第7周第49天，坚持就是胜利！**

### 第7周知识点总览

| Day | 主题 | 核心内容 | 难度 |
|-----|------|---------|------|
| 43 | DP一维入门 | 爬楼梯、打家劫舍、最大子数组和 | ⭐⭐ |
| 44 | DP一维进阶 | LIS、摆动序列 | ⭐⭐⭐ |
| 45 | DP二维 | 不同路径、最小路径和、LCS | ⭐⭐⭐ |
| 46 | DP高级 | 区间DP、树形DP、状态压缩 | ⭐⭐⭐⭐ |
| 47 | DP字符串 | 最长回文子串、正则匹配 | ⭐⭐⭐⭐ |
| 48 | 综合 | 图+并查集+DP混合题 | ⭐⭐⭐⭐ |

### DP五大模型
1. **线性DP**：dp[i]依赖dp[i-1], dp[i-2]...（爬楼梯、打家劫舍）
2. **区间DP**：dp[i][j]依赖内部子区间（回文串、矩阵链乘）
3. **二维DP**：dp[i][j]依赖左上角（LCS、编辑距离）
4. **树形DP**：子树递归合并（打家劫舍III）
5. **状态压缩DP**：用二进制表示状态（TSP、旅行商）

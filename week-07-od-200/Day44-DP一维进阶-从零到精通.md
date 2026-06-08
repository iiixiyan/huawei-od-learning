# 📘 Day 44：DP一维进阶 — 从零到精通

> 🎯 **学习目标**：掌握DP一维进阶核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第7周 · 第44天** | [返回目录](../README.md)

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

# D44 — DP 一维进阶 (6题)

---

## 1. Word Break (T150)

**思路**：`dp[i]` = s[:i] 能否被拆分成字典中的词
**转移**：`dp[i] = any(dp[j] and s[j:i] in wordSet for j in range(i))`
**剪枝**：只检查长度不超过最长单词的 j

```python
def wordBreak(s: str, wordDict: list[str]) -> bool:
    wordSet = set(wordDict)
    max_len = max(len(w) for w in wordDict)
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    for i in range(1, n + 1):
        for j in range(max(0, i - max_len), i):
            if dp[j] and s[j:i] in wordSet:
                dp[i] = True
                break
    return dp[n]

# 时间 O(n * max_len), 空间 O(n + wordDict)
```

---

## 2. Coin Change (T150/O)

**状态定义**：`dp[i]` = 凑成金额 i 的最少硬币数
**转移**：`dp[i] = min(dp[i - coin] + 1 for coin in coins if i >= coin)`
**初始**：`dp[0] = 0`，其余为 `inf`

```python
def coinChange(coins: list[int], amount: int) -> int:
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for i in range(1, amount + 1):
        for c in coins:
            if i >= c:
                dp[i] = min

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

> 💪 **第7周第44天，坚持就是胜利！**

### 最长递增子序列（LIS）

```python
def lengthOfLIS(nums):
    '''O(n²)解法：dp[i] = 以nums[i]结尾的最长递增子序列长度'''
    if not nums:
        return 0
    dp = [1] * len(nums)
    for i in range(len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```

### 贪心+二分优化（O(nlogn)）

```python
def lengthOfLIS_optimized(nums):
    import bisect
    tails = []  # tails[i] = 长度为i+1的递增子序列的最小末尾值
    for num in nums:
        pos = bisect.bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
    return len(tails)
```

**核心思想**：我们只关心「每个长度的递增子序列的最小末尾值」，这个值越小，后面接新数字的可能性越大。

### 手把手推演：LIS

```
nums = [10, 9, 2, 5, 3, 7, 101, 18]

dp数组变化:
i=0(10): dp[0]=1 → [1]
i=1(9):  9<10?→j=0,dp[1]=max(1,1+1=2?)→9<10❌,dp=1 → [1,1]
i=2(2):  j=0: 2<10✅ dp=1+1=2; j=1: 2<9✅ dp=max(1,1+1=2) → dp[2]=2 → [1,1,2]
i=3(5):  j=0:5<10✅,j=1:5<9✅,j=2:5>2✅ dp=max(2,2+1=3) → dp[3]=3 → [1,1,2,3]
i=4(3):  j=2:3>2✅ dp=max(2,2+1=3) → dp[4]=3 → [1,1,2,3,3]
i=5(7):  j=4:7>3✅ dp=max(3,3+1=4) → dp[5]=4 → [1,1,2,3,3,4]
i=6(101):dp[6]=5 → [1,1,2,3,3,4,5]
i=7(18): dp[7]=5 → [1,1,2,3,3,4,5,5]

最长递增子序列长度 = 5
```

### 摆动序列
```python
def wiggleMaxLength(nums):
    '''最长摆动子序列：交替的上升/下降'''
    up = down = 1
    for i in range(1, len(nums)):
        if nums[i] > nums[i-1]:
            up = down + 1
        elif nums[i] < nums[i-1]:
            down = up + 1
    return max(up, down)
```

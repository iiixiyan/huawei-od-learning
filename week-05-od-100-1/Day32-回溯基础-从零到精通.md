# 📘 Day 32：回溯基础 — 从零到精通

> 🎯 **学习目标**：掌握回溯基础核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第5周 · 第32天** | [返回目录](../README.md)

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

**回溯(Backtracking)** — 穷举搜索+剪枝.核心模板:
```python
def backtrack(path, choices):
    if 满足条件:
        res.append(path[:])
        return
    for choice in choices:
        做选择
        backtrack(path, choice)
        撤销选择
```
**适用场景**:组合、排列、子集、分割、棋盘问题.
**剪枝**:排序+跳过重复元素、提前判断可行性.

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

### 1. Letter Combinations of a Phone Number（⭐⭐）
**来源**：[L75/T150](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/)
**难度**：中等
**题目**：给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。答案可以按 **任意顺序** 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

*

**示例 1：**
```
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```
**示例 2：**
```
输入：digits = "2"
输出：["a","b","c"]
```
**提示：**

- `1
**思路**：数字到字母映射,回溯生成所有组合.每次选择一个字母,递归处理下一个数字.
**代码**：
```python
def letterCombinations(self, digits):
    if not digits:
        return []
    mapping = {"2":"abc","3":"def","4":"ghi","5":"jkl",
               "6":"mno","7":"pqrs","8":"tuv","9":"wxyz"}
    res = []

    def backtrack(idx, path):
        if idx == len(digits):
            res.append("".join(path))
            return
        for c in mapping[digits[idx]]:
            path.append(c)
            backtrack(idx + 1, path)
            path.pop()

    backtrack(0, [])
    return res
```
### 2. Combinations（⭐⭐）
**来源**：[T150/O](https://leetcode.cn/problems/combinations/)
**难度**：中等
**题目**：给定两个整数 `n` 和 `k`，返回范围 `[1, n]` 中所有可能的 `k` 个数的组合。

你可以按 **任何顺序** 返回答案。

**示例 1：**
```
输入：n = 4, k = 2
输出：
[
[2,4],
[3,4],
[2,3],
[1,2],
[1,3],
[1,4],
]
```
**示例 2：**
```
输入：n = 1, k = 1
输出：[[1]]
```
**提示：**

- `1
**思路**：从1~n中选k个数的组合.回溯时限制起始位置避免重复组合.
**代码**：
```python
def combine(self, n, k):
    res = []

    def backtrack(start, path):
        if len(path) == k:
            res.append(path[:])
            return
        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()

    backtrack(1, [])
    return res
```
### 3. Combination Sum III（⭐⭐）
**来源**：[L75](https://leetcode.cn/problems/combination-sum-iii/)
**难度**：中等
**题目**：找出所有相加之和为 `n`  *的 `k` 个数的组合，且满足下列条件：

- 只使用数字1到9

- 每个数字 **最多使用一次**

返回 *所有可能的有效组合的列表* 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

**示例 1:**
```
输入: k = 3, n = 7
输出: [[1,2,4]]
解释:
1 + 2 + 4 = 7
没有其他符合的组合了。
```
**示例 2:**
```
输入: k = 3, n = 9
输出: [[1,2,6], [1,3,5], [2,3,4]]
解释:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
没有其他符合的组合了。
```
**示例 3:**
```
输入: k = 4, n = 1
输出: []
解释: 不存在有效的组合。
在[1,9]范围内使用4个不同的数字，我们可以得到的最小和是1+2+3+4 = 10，因为10 > 1，没有有效的组合。
```
**提示:**

- `2
**思路**：从1~9中选k个数,和为n.回溯+剪枝:当前和超过n或可选数不够时提前返回.
**代码**：
```python
def combinationSum3(self, k, n):
    res = []

    def backtrack(start, path, cur_sum):
        if len(path) == k:
            if cur_sum == n:
                res.append(path[:])
            return
        for i in range(start, 10):
            if cur_sum + i > n:
                break
            path.append(i)
            backtrack(i + 1, path, cur_sum + i)
            path.pop()

    backtrack(1, [], 0)
    return res
```
### 4. Permutations（⭐⭐）
**来源**：[T150/O](https://leetcode.cn/problems/permutations/)
**难度**：中等
**题目**：给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

**示例 1：**
```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```
**示例 2：**
```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```
**示例 3：**
```
输入：nums = [1]
输出：[[1]]
```
**提示：**

- `1
**思路**：全排列回溯.用used数组标记已选元素,每次从未选元素中选择.
**代码**：
```python
def permute(self, nums):
    res = []
    used = [False] * len(nums)

    def backtrack(path):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                path.append(nums[i])
                backtrack(path)
                path.pop()
                used[i] = False

    backtrack([])
    return res
```
### 5. Permutations II（⭐⭐）
**来源**：[O](https://leetcode.cn/problems/permutations/)
**难度**：中等
**题目**：给定一个不含重复数字的数组 `nums` ，返回其 *所有可能的全排列* 。你可以 **按任意顺序** 返回答案。

**示例 1：**
```
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```
**示例 2：**
```
输入：nums = [0,1]
输出：[[0,1],[1,0]]
```
**示例 3：**
```
输入：nums = [1]
输出：[[1]]
```
**提示：**

- `1
**思路**：含重复元素的全排列.先排序,剪枝条件:同一层中跳过重复元素(i>0且nums[i]==nums[i-1]且used[i-1]为False).
**代码**：
```python
def permuteUnique(self, nums):
    nums.sort()
    res = []
    used = [False] * len(nums)

    def backtrack(path):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
                continue
            used[i] = True
            path.append(nums[i])
            backtrack(path)
            path.pop()
            used[i] = False

    backtrack([])
    return res
```
### 6. Generate Parentheses（⭐⭐）
**来源**：[T150/O](https://leetcode.cn/problems/generate-parentheses/)
**难度**：中等
**题目**：数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的 **括号组合。

**示例 1：**
```
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```
**示例 2：**
```
输入：n = 1
输出：["()"]
```
**提示：**

- `1
**思路**：回溯生成合法括号对.维护左括号和右括号计数,保证任何时刻左括号数≥右括号数.
**代码**：
```python
def generateParenthesis(self, n):
    res = []

    def backtrack(left, right, path):
        if len(path) == 2 * n:
            res.append("".join(path))
            return
        if left < n:
            path.append('(')
            backtrack(left + 1, right, path)
            path.pop()
        if right < left:
            path.append(')')
            backtrack(left, right + 1, path)
            path.pop()

    backtrack(0, 0, [])
    return res
```

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

> 💪 **第5周第32天，坚持就是胜利！**

## 3.3 组合总和III — 综合练习

```python
def combinationSum3(k, n):
    '''找出所有相加之和为n的k个数的组合，只使用1-9'''
    def backtrack(start, path, target):
        if len(path) == k and target == 0:
            res.append(path[:])
            return
        if len(path) > k or target < 0:
            return
        # 剪枝：剩余数字不够
        for i in range(start, 10):
            if 9 - i + 1 < k - len(path):
                break
            path.append(i)
            backtrack(i + 1, path, target - i)
            path.pop()
    res = []
    backtrack(1, [], n)
    return res
```

## 3.4 组合总和II — 去重+剪枝综合

```python
def combinationSum2(candidates, target):
    candidates.sort()
    def backtrack(start, path, target):
        if target == 0:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            if candidates[i] > target:
                break
            if i > start and candidates[i] == candidates[i-1]:
                continue
            path.append(candidates[i])
            backtrack(i + 1, path, target - candidates[i])
            path.pop()
    res = []
    backtrack(0, [], target)
    return res
```

## 3.5 全排列II — 含重复元素去重

```python
def permuteUnique(nums):
    nums.sort()
    res = []
    def backtrack(path, used):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            if i > 0 and nums[i] == nums[i-1] and not used[i-1]:
                continue
            path.append(nums[i])
            used[i] = True
            backtrack(path, used)
            used[i] = False
            path.pop()
    backtrack([], [False]*len(nums))
    return res
```

### 时间复杂度分析（重要！）

| 问题 | 时间复杂度 | 空间复杂度 | 说明 |
|------|-----------|-----------|------|
| 全排列(n) | O(n! × n) | O(n) | n!个排列，每个复制O(n) |
| 子集(n) | O(2^n × n) | O(n) | 2^n个子集 |
| 组合(n,k) | O(C(n,k) × k) | O(k) | C(n,k)种组合 |
| 去重排列 | O(n! × n) | O(n) | 剪枝后稍好 |

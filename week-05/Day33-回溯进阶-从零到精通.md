# 📘 Day 33：回溯进阶 — 从零到精通


> 🎯 **学习目标**：N皇后、解数独、子集去重、组合总和II、IP地址
> 📅 **华为OD备考 · 第5周 · 第33天** | [返回目录](../README.md)

---

## 📑 目录

- [一、核心知识点](#一核心知识点)
- [二、核心套路/模型](#二核心套路模型)
- [三、高频题精讲](#三高频题精讲)
- [四、面试追问与进阶](#四面试追问与进阶)
- [五、OD机考实战指南](#五od机考实战指南)
- [六、今日作业](#六今日作业)

---

## 一、核心知识点

**进阶回溯技巧**:
- **子集枚举**: 每个元素选或不选,或使用循环+start.
- **可重复使用元素**: 递归时start不变(Combination Sum).
- **不可重复使用+去重**: start+1 + 排序剪枝(Combination Sum II).
- **棋盘/矩阵搜索**: 方向数组+visited或原地修改(Word Search).
- **分割问题**: 在不同位置切分,验证合法性(Restore IP).


### 操作速查表

| 操作 | 写法 | 复杂度 |
|------|------|--------|
| 创建 | ... | O(1) |
| 查询 | ... | O(1) |
| 修改 | ... | O(1) |

## 二、核心套路/模型

### 套路1：标准模板

```python
# 模板代码
```

**手把手推演**：
```
推演过程
```

## 三、高频题精讲

### 1. Subsets（（⭐⭐））

**来源**：[O](https://leetcode.cn/problems/subsets/)
**难度**：中等
**题目**：给你一个整数数组 `nums` ，数组中的元素 **互不相同**。返回该数组所有可能的子集（幂集）。

解集**不能**包含重复的子集。你可以按**任意顺序** 返回解集。

**示例 1：**
```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```
**示例 2：**
```
输入：nums = [0]
输出：[[],[0]]
```
**提示：**

- `1
**思路**：枚举所有子集.每个元素有选与不选两种选择,回溯穷举.
**代码**：
```python
def subsets(self, nums):
    res = []

    def backtrack(start, path):
        res.append(path[:])
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()

    backtrack(0, [])
    return res
```

**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

### 2. Combination Sum（（⭐⭐））

**来源**：[T150/O](https://leetcode.cn/problems/combination-sum/)
**难度**：中等
**题目**：给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有* ***不同组合**，并以列表形式返回。你可以按**任意顺序**返回这些组合。

`candidates` 中的**同一个**数字可以**无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

**示例 1：**
```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```
**示例 2：**
```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```
**示例 3：**
```
输入: candidates = [2], target = 1
输出: []
```
**提示：**

- `1
**思路**：从candidates中选可重复使用的元素求和为target.回溯时start位置不变(可重复选),当前和超过target剪枝.
**代码**：
```python
def combinationSum(self, candidates, target):
    res = []

    def backtrack(start, path, cur_sum):
        if cur_sum == target:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            if cur_sum + candidates[i] > target:
                continue
            path.append(candidates[i])
            backtrack(i, path, cur_sum + candidates[i])
            path.pop()

    backtrack(0, [], 0)
    return res
```

**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

### 3. Combination Sum II（（⭐⭐））

**来源**：[O](https://leetcode.cn/problems/combination-sum/)
**难度**：中等
**题目**：给你一个 **无重复元素** 的整数数组 `candidates` 和一个目标整数 `target` ，找出 `candidates` 中可以使数字和为目标数 `target` 的 所有* ***不同组合**，并以列表形式返回。你可以按**任意顺序**返回这些组合。

`candidates` 中的**同一个**数字可以**无限制重复被选取** 。如果至少一个数字的被选数量不同，则两种组合是不同的。

对于给定的输入，保证和为 `target` 的不同组合数少于 `150` 个。

**示例 1：**
```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```
**示例 2：**
```
输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```
**示例 3：**
```
输入: candidates = [2], target = 1
输出: []
```
**提示：**

- `1
**思路**：每个元素只能用一次+有重复.先排序,同层跳过重复元素,递归时start+1.
**代码**：
```python
def combinationSum2(self, candidates, target):
    candidates.sort()
    res = []

    def backtrack(start, path, cur_sum):
        if cur_sum == target:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            if cur_sum + candidates[i] > target:
                break
            if i > start and candidates[i] == candidates[i-1]:
                continue
            path.append(candidates[i])
            backtrack(i + 1, path, cur_sum + candidates[i])
            path.pop()

    backtrack(0, [], 0)
    return res
```

**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

### 4. N-Queens II（（⭐⭐⭐））

**来源**：[T150](https://leetcode.cn/problems/n-queens-ii/)
**难度**：困难
**题目**：**n 皇后问题**研究的是如何将 `n` 个皇后放置在 `n × n` 的棋盘上，并且使皇后彼此之间不能相互攻击。

给你一个整数 `n` ，返回**n 皇后问题** 不同的解决方案的数量。

**示例 1：**
```
*
输入：n = 4
输出：2
解释：如上图所示，4 皇后问题存在两个不同的解法。
```
**示例 2：**
```
输入：n = 1
输出：1
```
**提示：**

- `1
**思路**：回溯放置皇后.用三个集合记录被占用的列、主对角线(row-col)、副对角线(row+col).每行放一个皇后,DFS所有行.
**代码**：
```python
def totalNQueens(self, n):
    cols = set()
    diag1 = set()  # row - col
    diag2 = set()  # row + col

    def backtrack(row):
        if row == n:
            return 1
        count = 0
        for col in range(n):
            d1, d2 = row - col, row + col
            if col in cols or d1 in diag1 or d2 in diag2:
                continue
            cols.add(col); diag1.add(d1); diag2.add(d2)
            count += backtrack(row + 1)
            cols.remove(col); diag1.remove(d1); diag2.remove(d2)
        return count

    return backtrack(0)
```

**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

### 5. Word Search（（⭐⭐⭐））

**来源**：[T150](https://leetcode.cn/problems/word-search/)
**难度**：中等
**题目**：给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

**示例 1：**
```
*
输入：board = [['A','B','C','E'],['S','F','C','S'],['A','D','E','E']], word = "ABCCED"
输出：true
```
**示例 2：**
```
*
输入：board = [['A','B','C','E'],['S','F','C','S'],['A','D','E','E']], word = "SEE"
输出：true
```
**示例 3：**
```
*
输入：board = [['A','B','C','E'],['S','F','C','S'],['A','D','E','E']], word = "ABCB"
输出：false
```
**提示：**

- `m == board.length`

- `n = board[i].length`

- `1

**进阶：**你可以使用搜索剪枝的技术来优化解决方案，使其在 `board` 更大的情况下可以更快解决问题？
**思路**：在网格中回溯搜索单词.从每个匹配首字母的位置出发,四方向DFS,已使用的字母标记为'#'避免重复使用.
**代码**：
```python
def exist(self, board, word):
    m, n = len(board), len(board[0])

    def dfs(i, j, idx):
        if idx == len(word):
            return True
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] != word[idx]:
            return False
        temp, board[i][j] = board[i][j], '#'
        found = (dfs(i+1, j, idx+1) or dfs(i-1, j, idx+1) or
                 dfs(i, j+1, idx+1) or dfs(i, j-1, idx+1))
        board[i][j] = temp
        return found

    for i in range(m):
        for j in range(n):
            if board[i][j] == word[0] and dfs(i, j, 0):
                return True
    return False
```

**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

### 6. Restore IP Addresses（（⭐⭐））

**来源**：[O](https://leetcode.cn/problems/restore-ip-addresses/)
**难度**：中等
**题目**：**有效 IP 地址**正好由四个整数（每个整数位于 `0` 到 `255` 之间组成，且不能含有前导 `0`），整数之间用 `'.'` 分隔。

- 例如：`"0.1.2.201"` 和` "192.168.1.1"` 是**有效**IP 地址，但是 `"0.011.255.245"`、`"192.168.1.312"` 和 `"192.168@1.1"` 是**无效** IP 地址。

给定一个只包含数字的字符串 `s` ，用以表示一个 IP 地址，返回所有可能的**有效 IP 地址**，这些地址可以通过在 `s` 中插入 `'.'` 来形成。你 **不能**重新排序或删除 `s` 中的任何数字。你可以按**任何** 顺序返回答案。

**示例 1：**
```
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
```
**示例 2：**
```
输入：s = "0000"
输出：["0.0.0.0"]
```
**示例 3：**
```
输入：s = "101023"
输出：["1.0.10.23","1.0.102.3","10.1.0.23","10.10.2.3","101.0.2.3"]
```
**提示：**

- `1
**思路**：分割字符串为4段合法IP.回溯在s的不同位置切割,每段需在0~255且无前导零(除非为0).
**代码**：
```python
def restoreIpAddresses(self, s):
    res = []

    def backtrack(start, path):
        if len(path) == 4:
            if start == len(s):
                res.append(".".join(path))
            return
        for end in range(start + 1, min(start + 4, len(s) + 1)):
            segment = s[start:end]
            if (len(segment) > 1 and segment[0] == '0') or int(segment) > 255:
                continue
            path.append(segment)
            backtrack(end, path)
            path.pop()

    backtrack(0, [])
    return res
```
## 📝 总结
- 子集 & 组合: start控制不回头,避免重复组合.
- 可重复选 vs 不可重复: start不变 vs start+1.
- 去重: 排序 + i > start跳过同层重复.
- N皇后: 对角线巧妙映射(col±row).
- 网格搜索: 方向数组 + 原地修改回溯.
- 分割问题: 验证每段合法性.


**手把手推演**：
```
逐步推演过程...
```

**复杂度**：O(n)

## 四、面试追问与进阶

### Q&A

**Q1: 核心考点是什么？**
A: ...

**Q2: 如何优化？**
A: ...


## 五、OD机考实战指南

| 题型 | 频率 | 分值 |
|------|------|------|
| 基础题 | ⭐⭐⭐⭐ | 100分 |
| 进阶题 | ⭐⭐⭐ | 200分 |

### 考场策略
1. 先写模板框架
2. 注意边界条件
3. 测试小规模用例


## 六、今日作业

### 必做题
1. 题目1
2. 题目2

### 选做题
3. 题目3

### 挑战题
4. 题目4


> 💪 坚持就是胜利！

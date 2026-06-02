# Day07：第一周复习 — 从零到精通

> 本文档专为华为OD备考设计，适合零基础小白。涵盖Day1~Day7全部知识点、代码模板、复习题详解、常见错误与周测验解析。

---

## 📋 目录

- [一、本周知识全景大表](#一本周知识全景大表)
- [二、核心技巧代码模板](#二核心技巧代码模板)
- [三、混合复习题精讲](#三混合复习题精讲)
- [四、初学者最容易犯的错](#四初学者最容易犯的错)
- [五、周测验解析](#五周测验解析)
- [六、本周总结与掌握程度建议](#六本周总结与掌握程度建议)

---

## 一、本周知识全景大表

以下大表总结Day1~Day7所有核心知识点，按天排列，方便复习时快速定位。

| 天数 | 专题 | 核心技巧 | 代表题型 | 关键要点 |
|:----:|:----|:---------|:---------|:---------|
| Day1 | 数组基础操作 | **快慢指针（读写指针）** | 移除元素、删除有序重复项I/II、合并有序数组 | slow指向已处理末尾，fast遍历；删除重复比较nums[fast]与nums[slow-k] |
| Day1 | 数组基础操作 | **头尾指针（从后往前）** | 合并两个有序数组 | 从后往前填充，避免移动元素 |
| Day1 | 数组基础操作 | **Boyer-Moore投票算法** | 多数元素 | 不同元素抵消，剩余candidate即众数 |
| Day2 | 数组进阶操作 | **三次翻转** | 轮转数组 | 整体翻转→前k翻转→后n-k翻转，空间O(1) |
| Day2 | 数组进阶操作 | **一次遍历+维护最小值** | 买卖股票I | 只交易一次，找最低买点+最大差价 |
| Day2 | 数组进阶操作 | **贪心（累加正收益）** | 买卖股票II | 只要今天>昨天就交易，累加所有上涨段 |
| Day2 | 数组进阶操作 | **贪心（维护最远可达）** | 跳跃游戏I | 遍历中更新max_reach，超过当前位置即可达 |
| Day2 | 数组进阶操作 | **贪心+BFS思想** | 跳跃游戏II | 用cur_end和farthest记录步数边界 |
| Day2 | 数组进阶操作 | **计数排序思想** | H指数 | 引用次数超过n的按n处理，从大到小累加 |
| Day3 | 数组+数学 | **前缀积+后缀积** | 除自身以外数组乘积 | answer存前缀积，suffix变量从右乘回来 |
| Day3 | 数组+数学 | **贪心+总油量判断** | 加油站 | 若cur_gas<0则重置起点i+1；总油量<0则无解 |
| Day3 | 数组+数学 | **左右两次遍历** | 分发糖果 | 左→右保证递增链，右→左取max保证递减链 |
| Day3 | 数组+数学 | **双指针+短板效应** | 接雨水 | 左右指针向中移动，较矮侧决定水位 |
| Day3 | 数组+数学 | **贪心（检查空位）** | 种花问题 | 检查当前位置和左右邻居均为0才种 |
| Day4 | 哈希集合 | **哈希表补数查找** | 两数之和 | 存已遍历元素，查target-当前值 |
| Day4 | 哈希集合 | **HashSet判循环** | 快乐数 | 用set记录出现的数字，检测循环 |
| Day4 | 哈希集合 | **滑动窗口+HashSet** | 存在重复元素II | 维护大小k的窗口，用set查重复 |
| Day4 | 哈希集合 | **只从序列起点计数** | 最长连续序列 | set存所有数，仅当num-1不在set时开始计数 |
| Day4 | 哈希集合 | **数学性质（gcd）** | 字符串最大公因子 | str1+str2==str2+str1则有公因子，长度为gcd |
| Day5 | 哈希映射 | **排序/计数作为key** | 字母异位词分组 | sorted字符串或len(26)计数元组作为key |
| Day5 | 哈希映射 | **双射（双向映射）** | 同构字符串、单词规律 | 两个dict分别记录s→t和t→s映射 |
| Day5 | 哈希映射 | **Counter计数** | 赎金信、独一无二出现次数 | Counter统计频率，set检查是否唯一 |
| Day5 | 哈希映射 | **集合运算** | 两个数组的差集 | Python set的-运算求差集 |
| Day6 | 前缀和+区间 | **一维前缀和** | 寻找最高海拔、中心下标 | prefix[r+1] - prefix[l] 快速求区间和 |
| Day6 | 前缀和+区间 | **二维前缀和+容斥** | 二维区域和检索 | 公式：p[i2+1][j2+1] - p[i1][j2+1] - p[i2+1][j1] + p[i1][j1] |
| Day6 | 前缀和+区间 | **区间合并（排序+合并）** | 合并区间、插入区间 | 按左端点排序，检查重叠，合并右端点 |
| Day6 | 前缀和+区间 | **贪心（按右端点排序）** | 用最少数量的箭引爆气球 | 按右端点排序，每次尽可能多地射击 |
| Day7 | 综合复习 | **多种技巧混合** | 移动零、有效数独、盛水容器、矩阵置零、缺失的正数、O(1)删除 | 见下文详细讲解 |

---

## 二、核心技巧代码模板

以下每个模板都是面试高频考点，建议**逐行理解并熟练手写**。

### 模板1：快慢指针（删除/移动元素）

```python
def two_pointers_template(nums: list[int]) -> int:
    """快慢指针通用模板：slow指向已处理末尾，fast遍历"""
    slow = 0
    for fast in range(len(nums)):
        if 需要保留的条件(nums[fast]):   # 条件因题而异
            nums[slow] = nums[fast]     # 写入slow位置
            slow += 1
    return slow  # 新长度
```

**适用场景**：移除元素、移动零、删除重复项（比较nums[fast]与nums[slow-1]或nums[slow-2]）

### 模板2：头尾指针（从后往前合并）

```python
def merge_template(nums1: list[int], m: int, nums2: list[int], n: int) -> None:
    """从后往前填充，避免元素移动"""
    p1, p2, p = m - 1, n - 1, m + n - 1
    while p1 >= 0 and p2 >= 0:
        if nums1[p1] >= nums2[p2]:
            nums1[p] = nums1[p1]; p1 -= 1
        else:
            nums1[p] = nums2[p2]; p2 -= 1
        p -= 1
    # 若nums2还有剩余，复制到nums1开头
    if p2 >= 0:
        nums1[:p2 + 1] = nums2[:p2 + 1]
```

**适用场景**：合并两个有序数组（注意nums1预留了空间）

### 模板3：贪心（维护最远可达范围）

```python
def greedy_farthest_template(nums: list[int]) -> bool:
    """贪心模板：维护当前能到达的最远位置"""
    max_reach = 0
    for i, step in enumerate(nums):
        if i > max_reach:
            return False  # 当前位置已无法到达
        max_reach = max(max_reach, i + step)
    return True
```

**适用场景**：跳跃游戏、加油站（判断能否走完）

### 模板4：左右两次遍历

```python
def left_right_template(arr: list[int]) -> list[int]:
    """左右两次遍历模板"""
    n = len(arr)
    res = [1] * n  # 初始值因题而异
    
    # 从左到右
    for i in range(1, n):
        if arr[i] > arr[i - 1]:  # 条件因题而异
            res[i] = res[i - 1] + 1
    
    # 从右到左
    for i in range(n - 2, -1, -1):
        if arr[i] > arr[i + 1]:  # 条件因题而异
            res[i] = max(res[i], res[i + 1] + 1)
    
    return res
```

**适用场景**：分发糖果、除自身以外数组乘积（前缀积+后缀积变体）

### 模板5：哈希集合查补数

```python
def hashset_lookup_template(nums: list[int], target: int) -> list[int]:
    """哈希表查找模板：存已遍历，查补数"""
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**适用场景**：两数之和、三数之和的变体

### 模板6：双射（双向映射）

```python
def bijection_template(s: str, t: str) -> bool:
    """双射模板：需要两个方向都一一对应"""
    map_s_to_t = {}
    map_t_to_s = {}
    for ch_s, ch_t in zip(s, t):
        if (ch_s in map_s_to_t and map_s_to_t[ch_s] != ch_t) or \
           (ch_t in map_t_to_s and map_t_to_s[ch_t] != ch_s):
            return False
        map_s_to_t[ch_s] = ch_t
        map_t_to_s[ch_t] = ch_s
    return True
```

**适用场景**：同构字符串、单词规律

### 模板7：前缀和（一维）

```python
def prefix_sum_template(nums: list[int]) -> list[int]:
    """一维前缀和模板"""
    n = len(nums)
    prefix = [0] * (n + 1)
    for i in range(n):
        prefix[i + 1] = prefix[i] + nums[i]
    # 查询区间 [l, r] 的和：prefix[r+1] - prefix[l]
    return prefix
```

### 模板8：二维前缀和

```python
class NumMatrixTemplate:
    """二维前缀和模板"""
    def __init__(self, matrix: list[list[int]]):
        m, n = len(matrix), len(matrix[0])
        self.p = [[0] * (n + 1) for _ in range(m + 1)]
        for i in range(m):
            row_sum = 0
            for j in range(n):
                row_sum += matrix[i][j]
                self.p[i + 1][j + 1] = self.p[i][j + 1] + row_sum
    
    def sumRegion(self, r1: int, c1: int, r2: int, c2: int) -> int:
        """查询子矩阵和 — 容斥原理公式"""
        p = self.p
        return (p[r2 + 1][c2 + 1] - p[r1][c2 + 1] - p[r2 + 1][c1] + p[r1][c1])
```

### 模板9：区间合并

```python
def merge_intervals_template(intervals: list[list[int]]) -> list[list[int]]:
    """区间合并三步法：排序→判断重叠→合并"""
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])  # 第一步：按左端点排序
    merged = [intervals[0]]
    for interval in intervals[1:]:
        if interval[0] > merged[-1][1]:  # 第二步：不重叠
            merged.append(interval)
        else:  # 第三步：重叠，合并右端点
            merged[-1][1] = max(merged[-1][1], interval[1])
    return merged
```

### 模板10：原地哈希

```python
def in_place_hash_template(nums: list[int]) -> int:
    """原地哈希模板：把数字x放到下标x-1的位置"""
    n = len(nums)
    i = 0
    while i < n:
        correct_idx = nums[i] - 1
        if 1 <= nums[i] <= n and nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    return n + 1
```

**适用场景**：缺失的第一个正数

### 模板11：O(1)交换删除

```python
import random

class RandomizedSetTemplate:
    """O(1)插入、删除、随机获取 — 哈希表+数组"""
    def __init__(self):
        self.nums = []
        self.val_to_idx = {}

    def insert(self, val: int) -> bool:
        if val in self.val_to_idx:
            return False
        self.val_to_idx[val] = len(self.nums)
        self.nums.append(val)
        return True

    def remove(self, val: int) -> bool:
        if val not in self.val_to_idx:
            return False
        idx = self.val_to_idx[val]
        last_val = self.nums[-1]
        self.nums[idx] = last_val          # 把末尾元素移到被删位置
        self.val_to_idx[last_val] = idx    # 更新映射
        self.nums.pop()                    # 删除末尾
        del self.val_to_idx[val]           # 删除映射
        return True

    def getRandom(self) -> int:
        return random.choice(self.nums)
```

---

## 三、混合复习题精讲

本节逐一讲解Day7的6道混合复习题，包含：题目分析 → 思路拆解 → 代码实现 → 复杂度分析 → 图解类比。

### 复习题1：移动零 ⭐

**题目**：给定数组 `nums`，将所有0移到末尾，保持非零元素相对顺序，原地操作。

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

#### 思路拆解

这道题的本质是「移除元素」的变体——把0视为要"移除"的值，只是移除后末尾用0填充而非"删除"。

**快慢指针法**：
- `slow` 指向下一个非零元素应放置的位置
- `fast` 遍历整个数组
- 当 `nums[fast] != 0` 时，将其交换到 `slow` 位置，`slow++`
- 最终 `slow` 之前全是非零，之后全是零

**可视化过程**（nums = [0,1,0,3,12]）：

```
初始: slow=0, fast=0
fast=0: nums[0]=0 → 跳过
fast=1: nums[1]=1≠0 → 交换 nums[0]↔nums[1] → [1,0,0,3,12], slow=1
fast=2: nums[2]=0 → 跳过
fast=3: nums[3]=3≠0 → 交换 nums[1]↔nums[3] → [1,3,0,0,12], slow=2
fast=4: nums[4]=12≠0 → 交换 nums[2]↔nums[4] → [1,3,12,0,0], slow=3
```

#### 代码

```python
def moveZeroes(nums: list[int]) -> None:
    slow = 0
    for fast in range(len(nums)):
        if nums[fast] != 0:
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1
```

#### 复杂度分析
- 时间复杂度：O(n)，一次遍历
- 空间复杂度：O(1)，原地交换

#### 初学者易错点
1. ❌ 用了额外数组复制 → 不符合"原地"要求
2. ❌ 交换后忘记 slow+1
3. ❌ 用remove+append（会改变相对顺序且效率低）

---

### 复习题2：有效的数独 ⭐⭐

**题目**：判断一个9×9数独是否有效，检查每行、每列、每个3×3宫内数字1-9是否重复。

```
输入: board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
输出: true
```

#### 思路拆解

**用三个集合数组**：
- `rows[r]`：记录第r行已出现的数字
- `cols[c]`：记录第c列已出现的数字
- `boxes[box_idx]`：记录第box_idx个3×3子数独已出现的数字

**子数独索引公式**：`box_idx = (r // 3) * 3 + (c // 3)`

为什么是这个公式？
- `r // 3`：将9行压缩为3个行块（0,1,2）
- `c // 3`：将9列压缩为3个列块（0,1,2）
- `(r // 3) * 3 + (c // 3)`：行块×3+列块 → 得到0~8的子数独编号

```
行块0: (r//3)=0 → 子数独0,1,2
行块1: (r//3)=1 → 子数独3,4,5
行块2: (r//3)=2 → 子数独6,7,8
```

#### 代码

```python
def isValidSudoku(board: list[list[str]]) -> bool:
    rows = [set() for _ in range(9)]
    cols = [set() for _ in range(9)]
    boxes = [set() for _ in range(9)]
    
    for r in range(9):
        for c in range(9):
            val = board[r][c]
            if val == ".":
                continue
            box_idx = (r // 3) * 3 + (c // 3)
            if val in rows[r] or val in cols[c] or val in boxes[box_idx]:
                return False
            rows[r].add(val)
            cols[c].add(val)
            boxes[box_idx].add(val)
    return True
```

#### 复杂度分析
- 时间复杂度：O(1)，固定遍历81个格子
- 空间复杂度：O(1)，三个固定大小的集合数组

#### 初学者易错点
1. ❌ 忘记处理 `"."`（空位），直接检查非数字字符
2. ❌ 子数独索引公式写错，写成 `(r // 3) + (c // 3) * 3` 或其他变体
3. ❌ 不在检查通过后才 `add()`，而是先add再检查，永远返回False

---

### 复习题3：盛最多水的容器 ⭐⭐

**题目**：给定高度数组，找两条垂线使得容器能装最多水。

```
输入: [1,8,6,2,5,4,8,3,7]
输出: 49
```

#### 思路拆解

**双指针从两端向中间移动**：
- 面积 = min(height[left], height[right]) × (right - left)
- 每次移动**较矮的那一侧**指针
- 为什么移动较矮的？因为宽度在减小，只有高度可能变大才有可能得到更大面积。如果移动较高的，高度只会≤当前高度，面积必减小。

**核心直觉**：短板效应——水的高度由较短的木板决定。想要更大面积，必须把短板换掉。

**可视化**：
```
[1,8,6,2,5,4,8,3,7]
 L→                  ←R
left=0, right=8, h=min(1,7)=1, w=8, area=8
→ 移动较矮的left（因为height[0]=1 < height[8]=7）
    
[1,8,6,2,5,4,8,3,7]
    L→              ←R
left=1, right=8, h=min(8,7)=7, w=7, area=49 → 更新max
→ 移动较矮的right（因为height[8]=7 < height[1]=8）
...以此类推
```

#### 代码

```python
def maxArea(height: list[int]) -> int:
    left, right = 0, len(height) - 1
    max_water = 0
    while left < right:
        h = min(height[left], height[right])
        w = right - left
        max_water = max(max_water, h * w)
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    return max_water
```

#### 复杂度分析
- 时间复杂度：O(n)，一次遍历
- 空间复杂度：O(1)

#### 初学者易错点
1. ❌ 移动了较高的指针 → 答案错误（违反了最短板的原理）
2. ❌ 相等时不知道移哪边 → 随便移一边均可，因为如果两边相等，移动任何一侧都不会丢失最优解（可以证明）
3. ❌ 面积公式写错成 `min * (right - left + 1)` → 宽度是 right - left 不是 +1

---

### 复习题4：O(1)时间插入、删除和获取随机元素 ⭐⭐⭐

**题目**：实现 `RandomizedSet`，支持平均O(1)时间的插入、删除和随机获取。

#### 思路拆解

**核心设计**：**哈希表 + 动态数组**
- 数组 `nums`：存储所有元素
- 哈希表 `val_to_idx`：值 → 在数组中的索引

**插入**：检查是否已存在，不存在则在数组末尾追加，记录索引。

**删除**（关键技巧）：**交换删除法**
1. 从哈希表获取待删元素的索引 `idx`
2. 取数组最后一个元素 `last_val`
3. 将 `last_val` 覆盖到 `idx` 位置
4. 更新 `last_val` 的索引为 `idx`
5. pop() 删除数组末尾
6. 从哈希表删除 `val`

这样避免了对数组中间元素进行删除后需要移动大量元素（O(n)）的问题。

**可视化交换删除**：
```
删除 val=2
初始: nums=[3,1,2,4], val_to_idx={3:0, 1:1, 2:2, 4:3}

1. idx = val_to_idx[2] = 2
2. last_val = nums[-1] = 4
3. nums[2] = 4 → nums=[3,1,4,4]
4. val_to_idx[4] = 2 → {3:0, 1:1, 2:2, 4:2}
5. nums.pop() → nums=[3,1,4]
6. del val_to_idx[2] → {3:0, 1:1, 4:2}
```

#### 代码

```python
import random

class RandomizedSet:
    def __init__(self):
        self.nums = []
        self.val_to_idx = {}

    def insert(self, val: int) -> bool:
        if val in self.val_to_idx:
            return False
        self.val_to_idx[val] = len(self.nums)
        self.nums.append(val)
        return True

    def remove(self, val: int) -> bool:
        if val not in self.val_to_idx:
            return False
        idx = self.val_to_idx[val]
        last_val = self.nums[-1]
        self.nums[idx] = last_val
        self.val_to_idx[last_val] = idx
        self.nums.pop()
        del self.val_to_idx[val]
        return True

    def getRandom(self) -> int:
        return random.choice(self.nums)
```

#### 复杂度分析
- 时间复杂度：O(1) 均摊（插入/删除/随机获取）
- 空间复杂度：O(n)

#### 初学者易错点
1. ❌ 忘记处理只有一个元素的边界情况（交换删除时自己和自己交换 → 没问题，代码天然支持）
2. ❌ 删除后没有更新 `last_val` 的索引 → 后续再操作 `last_val` 就找不到正确位置了
3. ❌ 用 `list.remove(val)` 或 `del nums[idx]` → 这是O(n)操作
4. ❌ `getRandom` 时数组为空 → 题目不保证，但实际应处理

---

### 复习题5：缺失的第一个正数 ⭐⭐⭐

**题目**：未排序数组，找出没出现的最小正整数。要求 O(n) 时间，O(1) 空间。

```
输入: [3,4,-1,1]
输出: 2
```

#### 思路拆解

**原地哈希**：把数组本身当作哈希表。

核心思想：**理想情况下，数字x应该放在下标x-1的位置**。
- 数字1 → 下标0
- 数字2 → 下标1
- 数字3 → 下标2
- ...

**步骤**：
1. 遍历数组，对于每个位置i的数字nums[i]：
   - 如果 `1 <= nums[i] <= n` 且 `nums[i] != nums[nums[i]-1]`
   - 说明它没在正确位置，交换到正确位置
   - 交换后，当前位置的新数字要继续检查（所以用while）
2. 再次遍历，第一个 `nums[i] != i+1` 的位置，返回 `i+1`
3. 如果全部对号入座，返回 `n+1`

**可视化**（nums=[3,4,-1,1], n=4）：
```
初始: [3, 4, -1, 1]
i=0: nums[0]=3, 3应该在下标2, nums[2]=-1, 交换 → [-1, 4, 3, 1]
     交换后nums[0]=-1, 不在1~4内, i+=1
i=1: nums[1]=4, 4应该在下标3, nums[3]=1, 交换 → [-1, 1, 3, 4]
     交换后nums[1]=1, 1应该在下标0, nums[0]=-1, 交换 → [1, -1, 3, 4]
     交换后nums[1]=-1, 不在1~4内, i+=1
i=2: nums[2]=3, 3应该在下标2, nums[2]=3 ✓, i+=1
i=3: nums[3]=4, 4应该在下标3, nums[3]=4 ✓, i+=1

检查: nums[0]=1✓, nums[1]=-1≠2 → 返回 2
```

#### 代码

```python
def firstMissingPositive(nums: list[int]) -> int:
    n = len(nums)
    i = 0
    while i < n:
        correct_idx = nums[i] - 1
        if 1 <= nums[i] <= n and nums[i] != nums[correct_idx]:
            nums[i], nums[correct_idx] = nums[correct_idx], nums[i]
        else:
            i += 1
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    return n + 1
```

#### 复杂度分析
- 时间复杂度：O(n)，每个元素最多被交换两次就到正确位置
- 空间复杂度：O(1)，原地交换

#### 初学者易错点
1. ❌ 忘记处理越界：`nums[i] - 1` 可能 < 0 或 >= n，必须先检查 `1 <= nums[i] <= n`
2. ❌ 用for循环而非while循环 → 交换后当前位置变成了新数字，for循环会跳过它，导致遗漏
3. ❌ 交换条件写成 `nums[i] != i + 1` → 这不是原地哈希的交换条件，正确条件是 `nums[i] != nums[nums[i]-1]`
4. ❌ 最后返回值写成 `n` → 如果1~n都在数组中，应该返回 n+1

---

### 复习题6：矩阵置零 / 生命游戏 ⭐⭐⭐

**题目**：生命游戏（康威生命）。根据周围8个细胞的活细胞数量，同时更新所有细胞状态。

规则：
1. 活细胞周围活细胞<2 → 死（人口不足）
2. 活细胞周围活细胞=2或3 → 活（恰好）
3. 活细胞周围活细胞>3 → 死（人口过剩）
4. 死细胞周围活细胞=3 → 活（繁殖）

#### 思路拆解

**原地标记法**（关键技巧）：
- 用特殊状态表示变化，避免同时更新导致互相影响
- 2 表示：原来是1（活），将要变成0（死）
- -1 表示：原来是0（死），将要变成1（活）

**为什么需要特殊标记？**
因为所有细胞是同时更新的。如果先更新一个细胞为0，它旁边的细胞在计算时就会少算一个活细胞。所以需要用中间状态记录"将要变化"，最后统一处理。

**步骤**：
1. 第一次遍历：对每个细胞统计周围8个方向的活细胞数
   - 「活细胞」的判断：绝对值|board[nr][nc]| == 1（因为-1也是1变来的，但还没变回去）
   - 根据规则标记为2（活→死）或-1（死→活）
2. 第二次遍历：将2→0, -1→1

#### 代码

```python
def gameOfLife(board: list[list[int]]) -> None:
    m, n = len(board), len(board[0])
    directions = [(-1,-1), (-1,0), (-1,1), (0,-1),
                  (0,1), (1,-1), (1,0), (1,1)]

    def count_live(r: int, c: int) -> int:
        cnt = 0
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < m and 0 <= nc < n and abs(board[nr][nc]) == 1:
                cnt += 1
        return cnt

    for r in range(m):
        for c in range(n):
            live = count_live(r, c)
            if board[r][c] == 1 and (live < 2 or live > 3):
                board[r][c] = 2   # 活→死
            if board[r][c] == 0 and live == 3:
                board[r][c] = -1  # 死→活

    for r in range(m):
        for c in range(n):
            if board[r][c] == 2:
                board[r][c] = 0
            elif board[r][c] == -1:
                board[r][c] = 1
```

#### 复杂度分析
- 时间复杂度：O(m×n)，每个细胞检查8个邻居
- 空间复杂度：O(1)，原地修改

#### 初学者易错点
1. ❌ 忘记用 `abs(board[nr][nc]) == 1` 判断活细胞 → 如果没有abs，值为-1的细胞会被判为死细胞（但-1本来是活的）
2. ❌ 计数时没有检查边界 → 数组越界
3. ❌ 用两个独立的新数组存储结果 → 空间O(m×n)，不符合原地要求
4. ❌ 同时对同一个细胞应用多个规则 → 用if-elif避免，或使用两个独立的if（因为规则1/3与规则2互斥，规则4只适用于死细胞）

---

## 四、初学者最容易犯的错

以下汇总本周所有题型中，初学者最常见的错误，分为六大类。

### 4.1 双指针类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 快慢指针的交换条件写反 | `if nums[fast] == val` | 应写 `if nums[fast] != val`（保留不等于val的） |
| 忘记移动slow指针 | 只写赋值不写slow+=1 | 每次写入后必须slow+=1 |
| 从前往后合并有序数组 | 从前往后导致大量移动 | 从后往前，利用nums1末尾的空位 |
| 盛水容器移动较高指针 | `if left<right: left+=1 else: right-=1` | 移动较矮的：`if height[left] < height[right]: left+=1` |
| 接雨水忘记更新最大值 | 只计算water不加left_max/right_max | 先更新max再计算差值 |

### 4.2 哈希表类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 两数之和先存再查 | `seen[num]=i; if complement in seen` | 先查后存，否则可能自己匹配自己 |
| 同构字符串只用单向映射 | 只检查s→t，不检查t→s | 必须双向映射（双射） |
| 最长连续序列没有去重 | 从原数组遍历，重复元素导致重复计数 | 用set去重，从set遍历或只从起点开始 |
| 字母异位词分组key用错 | 直接用原始字符串 | 需要标准化表示：排序或计数元组 |

### 4.3 贪心类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 跳跃游戏忘了检查当前位置是否可达 | 直接更新max_reach | 先判断`if i > max_reach: return False` |
| 股票II认为必须手写买卖点 | 用复杂的状态机 | 贪心累加所有正值：`profit += max(0, prices[i]-prices[i-1])` |
| 加油站忘了总油量判断 | 只处理局部负数但不检查总量 | 检查`total_gas >= 0`，否则返回-1 |
| 种花问题忘了边界处理 | 检查`flowerbed[i-1]`时不判断i==0 | 用`(i==0) or` 短路处理 |

### 4.4 前缀和/区间类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 一维前缀和数组长度用n | `prefix = [0]*n` | 需要`n+1`长度，prefix[0]=0 |
| 二维前缀和容斥公式记错 | `p[i2][j2] - p[i1][j2] - p[i2][j1] + p[i1][j1]` | 索引要+1：`p[i2+1][j2+1] - p[i1][j2+1] - p[i2+1][j1] + p[i1][j1]` |
| 区间合并没排序 | 直接遍历 | 必须按左端点排序 |
| 射气球按左端点排序 | 按左端排序导致不能射最多 | 按右端点排序（活动选择问题的标准解法） |

### 4.5 原地哈希类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 用for循环处理交换 | `for i in range(n): if ...: swap` | 必须用while，因为交换后当前位置有新值需要继续处理 |
| 交换条件写错 | `nums[i] != i+1` 就交换 | 应比较 `nums[i] != nums[nums[i]-1]` |
| 忘记检查范围 | 直接取 `nums[i]-1` 做索引 | 必须先检查 `1 <= nums[i] <= n` |
| 返回值写成n | `return n` | 如果1~n全有，应返回 `n+1` |

### 4.6 标记法类错误

| 错误 | 错误示例 | 正确做法 |
|:-----|:---------|:---------|
| 生命游戏没处理同时更新 | 直接修改board为0/1 | 用中间状态（2/-1）标记变化 |
| 判断活细胞时没考虑标记 | `board[nr][nc] == 1` | 应使用 `abs(board[nr][nc]) == 1` |
| 矩阵置零用额外空间记录 | 新建m×n数组 | 用第一行/第一列作为标记位 |

---

## 五、周测验解析

以下10道自测题，每题给出详细解析。

### 测验1：合并两个有序数组（从后往前）的时间复杂度？空间复杂度？

**答案**：O(m+n) 时间，O(1) 空间

**解析**：从后往前遍历，每次比较两个数组的末尾元素，取较大的放到nums1末尾。每个元素只比较一次，总比较次数不超过m+n。不使用额外数组，空间O(1)。

### 测验2：快慢指针删除重复元素时，slow和fast分别代表什么？

**答案**：slow指向已处理（写入）部分的末尾，即下一个可写入位置；fast遍历未处理的剩余部分。

**解析**：fast像侦察兵，到处查看哪些元素需要保留；slow像工兵，在已处理区域的边界等待接收需要保留的元素。

### 测验3：Boyer-Moore投票算法的前提条件是什么？

**答案**：多数元素出现次数 > n/2（即超过一半）。

**解析**：因为多数元素超过一半，所以用"不同元素抵消"的思路，最终剩下的candidate一定是多数元素。如果不满足这个条件，算法结果不可靠。

### 测验4：接雨水的双指针法中，哪一侧决定当前水位？

**答案**：较矮的一侧（短板效应）。

**解析**：接雨水的水位由较短的柱子决定，就像木桶能装多少水取决于最短的那块木板。当height[left] < height[right]时，left侧决定水位；反之right侧决定。

### 测验5：最长连续序列的优化关键是什么？

**答案**：只从序列起点（num-1不在set中）开始计数。

**解析**：如果不加这个优化，对于每个元素都向后遍历，时间复杂度为O(n²)。加上"只从起点计数"后，每个元素最多被访问两次（一次外层循环，一次内层计数），总复杂度O(n)。

### 测验6：字母异位词分组有哪两种常见的key设计？

**答案**：
1. **排序后的字符串**：如 "eat"→"aet", "tea"→"aet"
2. **长度为26的计数元组**：如 "eat"→(1,0,0,0,1,0,...,1,0,...)

**解析**：两种方式都能将字母异位词映射到相同的key。排序法简单直观但需要O(k log k)；计数法避免排序，O(k)时间，但需要创建长度为26的元组。

### 测验7：二维前缀和的容斥原理公式是什么？

**答案**：
```
sum = p[i2+1][j2+1] - p[i1][j2+1] - p[i2+1][j1] + p[i1][j1]
```
其中 p 是二维前缀和数组，p[i+1][j+1] 表示从(0,0)到(i,j)的子矩阵和。

**解析**：容斥原理——大矩形减去两个小矩形，再加回多减去的重叠部分。记住口诀：大-左-上+左上。

### 测验8：区间合并的第一步是什么？

**答案**：按区间左端点排序。

**解析**：只有排序后，才能保证相邻的区间可能有重叠。如果不排序，无法通过一次遍历完成合并。

### 测验9：原地哈希的核心思想是什么？（缺失的第一个正数）

**答案**：将数组本身当作哈希表，把数字x放到下标x-1的位置。

**解析**：对于范围在[1, n]内的数字，理想情况下每个数字x应该出现在数组的x-1位置。通过交换把数字放到"正确"位置后，再次遍历检查即可发现缺失的数字。

### 测验10：随机集合删除元素时如何保证O(1)？

**答案**：将待删元素与数组末尾元素交换，然后删除末尾元素并更新哈希表。

**解析**：数组中间的删除操作需要移动后续所有元素（O(n)）。通过把待删元素和末尾元素交换，删除末尾（O(1)），同时更新哈希表中末尾元素的索引，就能实现O(1)删除。

---

## 六、本周总结与掌握程度建议

### 6.1 知识脉络总览

本周从最基础的**双指针原地操作**开始，逐步深入到**贪心策略**、**左右遍历**、**哈希表**（集合+映射）、**前缀和**、**区间合并**，最后进行综合复习。整体知识脉络如下：

```
Day1 双指针（原地修改） ───→ Day2 贪心/状态机 ───→ Day3 左右遍历/数学
                                                           ↓
Day6 前缀和/区间合并 ←── Day5 哈希映射 ←─── Day4 哈希集合
                                                           ↓
                                                   Day7 综合复习
```

### 6.2 掌握程度三档建议

以下标注每道题的**建议掌握程度**，分为三档：

- 🔴 **Bug-free（必须能无错手写）**：面试最常考，必须闭着眼睛也能写出
- 🟡 **理解思路（理解核心思想，10分钟内写出）**：面试次常考，理解原理可应对变体
- 🟢 **了解即可（知道解法，面试不太会直接考）**：辅助理解，拓宽思路

| 题型 | 掌握程度 | 原因 |
|:-----|:--------:|:-----|
| 移除元素 | 🔴 Bug-free | 快慢指针最基础模板 |
| 删除有序数组重复项I | 🔴 Bug-free | 快慢指针最基础模板 |
| 合并两个有序数组 | 🔴 Bug-free | 头尾指针经典应用 |
| 移动零 | 🔴 Bug-free | 移除元素的变体，经常考 |
| 两数之和 | 🔴 Bug-free | 哈希表入门，秒杀 |
| 买卖股票I/II | 🔴 Bug-free | 贪心/状态机基础 |
| 跳跃游戏I | 🔴 Bug-free | 贪心经典题 |
| 有效的数独 | 🟡 理解思路 | 考察代码组织能力 |
| 盛最多水的容器 | 🟡 理解思路 | 双指针高级应用 |
| 接雨水 | 🟡 理解思路 | 经典难题，理解短板原理 |
| 最长连续序列 | 🟡 理解思路 | 哈希set优化思想 |
| 字母异位词分组 | 🟡 理解思路 | 哈希key设计思想 |
| 合并区间 | 🟡 理解思路 | 区间题基础模板 |
| 同构字符串/单词规律 | 🟡 理解思路 | 双射概念 |
| 除自身以外数组乘积 | 🟡 理解思路 | 前缀积后缀积 |
| 加油站 | 🟡 理解思路 | 贪心+油量判断 |
| 分发糖果 | 🟢 了解即可 | 左右两次遍历，难度偏高 |
| 跳跃游戏II | 🟢 了解即可 | 贪心+BFS边界 |
| 缺失的第一个正数 | 🟢 了解即可 | 原地哈希，代码有技巧 |
| O(1)随机集合 | 🟢 了解即可 | 哈希+数组组合 |
| 生命游戏 | 🟢 了解即可 | 标记法，题目较长 |
| 二维前缀和 | 🟢 了解即可 | 模板固定，理解公式即可 |
| 射气球 | 🟢 了解即可 | 贪心+区间，按右端点排序 |

### 6.3 一周速查口诀

为了帮助记忆，这里总结本周核心技巧的"一句话口诀"：

```
快慢指针删元素，slow写来fast读
头尾合并倒着来，三次翻转轮数组
贪心跳跃管最远，股票累加正收益
前后遍历分糖果，前缀后缀乘积出
接雨短板定水位，加油总油理清楚
两数和用哈希查，快乐循环看重复
连续序列找起点，异位排序或用计数
同构映射需双向，前缀和里空间换
区间合并先排序，射气球要看右端
原地哈希把自己放入正确位
交换删除O1，学习模版不嫌累
```

### 6.4 下一步建议

1. **Day1~Day3（🔴优先）**：双指针、贪心、左右遍历是最高频考点，确保每道题都能bug-free手写
2. **Day4~Day5（🟡重点）**：哈希表是"万金油"，两数之和、最长连续序列、字母异位词分组是面试常客
3. **Day6（🟢了解）**：前缀和和区间合并理解模板公式即可，知道什么场景下使用
4. **综合实战**：尝试用LeetCode的随机一题功能练习，限时30分钟，模拟面试节奏

---

> 💡 **写在最后**：第一周覆盖了数组和哈希表的全部核心技巧。这些技巧贯穿整个算法面试，理解透它们，后续学习链表、字符串、滑动窗口都会事半功倍。每天花15分钟复习本周的代码模板，一周后想不记住都难！

---

*文档生成时间：2026年5月18日*
*总字数：约18,000字*

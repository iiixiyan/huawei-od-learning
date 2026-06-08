# 📘 Day 36：二分查找 — 从零到精通

> 🎯 **学习目标**：掌握二分查找核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第6周 · 第36天** | [返回目录](../README.md)

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

**二分搜索模板 (标准版)**
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```
**二分搜索模板 (找边界)**
```python
# 第一个 >= target 的位置
def lower_bound(arr, target):
    left, right = 0, len(arr)
    while left < right:
        mid = left + (right - left) // 2
        if arr[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left
# 第一个 > target 的位置
def upper_bound(arr, target):
    left, right = 0, len(arr)
    while left < right:
        mid = left + (right - left) // 2
        if arr[mid] > target:
            right = mid
        else:
            left = mid + 1
    return left
```
**核心场景**：
1. 有序数组精确查找 → 标准二分
2. 有序数组找边界 → lower_bound / upper_bound
3. 旋转数组 → 画图找到有序区间，判断 target 是否在其中
4. 二维矩阵 → 把矩阵展开成一维或逐行二分
5. 峰值查找 → 比较 mid 和 mid+1 决定方向
6. 按值二分（答案二分）→ 在值域上二分判定可行性

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

### 1. 搜索插入位置（⭐⭐）来源：T150 / O
**来源**：[T150](https://leetcode.cn/problems/search-insert-position/)
**难度**：简单
**题目**：给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 `O(log n)` 的算法。

**示例 1:**
```
输入: nums = [1,3,5,6], target = 5
输出: 2
```
**示例 2:**
```
输入: nums = [1,3,5,6], target = 2
输出: 1
```
**示例 3:**
```
输入: nums = [1,3,5,6], target = 7
输出: 4
```
**提示:**

- `1 4`

- `-104 4`

- `nums` 为 **无重复元素 **的 **升序 **排列数组

- `-104 4`
**思路**：经典 lower_bound。在有序数组中找到第一个 >= target 的位置。如果所有元素 < target，则返回 len(nums)。直接在模板上套用。
**代码**：
```python
def searchInsert(nums, target):
    left, right = 0, len(nums)
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] >= target:
            right = mid
        else:
            left = mid + 1
    return left
```
### 2. 搜索二维矩阵（⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/search-a-2d-matrix/)
**难度**：中等
**题目**：给你一个满足下述两条属性的 `m x n` 整数矩阵：

- 每行中的整数从左到右按非严格递增顺序排列。

- 每行的第一个整数大于前一行的最后一个整数。

给你一个整数 `target` ，如果 `target` 在矩阵中，返回 `true` ；否则，返回 `false` 。

**示例 1：**
```
*
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
输出：true
```
**示例 2：**
```
*
输入：matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
输出：false
```
**提示：**

- `m == matrix.length`

- `n == matrix[i].length`

- `1 4 4`
**思路**：二维矩阵每一行递增，且每行第一个元素 > 上一行最后一个元素 → 拉直后是一个递增的一维数组。二分定位即可，mid // n 是行号，mid % n 是列号。
**代码**：
```python
def searchMatrix(matrix, target):
    m, n = len(matrix), len(matrix[0])
    left, right = 0, m * n - 1
    while left <= right:
        mid = left + (right - left) // 2
        val = matrix[mid // n][mid % n]
        if val == target:
            return True
        elif val < target:
            left = mid + 1
        else:
            right = mid - 1
    return False
```
### 3. 寻找峰值（⭐⭐）来源：L75 / T150
**来源**：[L75](https://leetcode.cn/problems/find-peak-element/)
**难度**：中等
**题目**：峰值元素是指其值严格大于左右相邻值的元素。

给你一个整数数组 `nums`，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 **任何一个峰值** 所在位置即可。

你可以假设 `nums[-1] = nums[n] = -∞` 。

你必须实现时间复杂度为 `O(log n)`* *的算法来解决此问题。

**示例 1：**
```
输入：nums = [1,2,3,1]
输出：2
解释：3 是峰值元素，你的函数应该返回其索引 2。
```
**示例 2：**
```
输入：nums = [1,2,1,3,5,6,4]
输出：1 或 5
解释：你的函数可以返回索引 1，其峰值元素为 2；
或者返回索引 5， 其峰值元素为 6。
```
**提示：**

- `1 31 31 - 1`

- 对于所有有效的 `i` 都有 `nums[i] != nums[i + 1]`
**思路**：nums[-1] = nums[n] = -∞，相邻元素不相等。二分法：如果 nums[mid] < nums[mid+1]，峰值在右侧；否则峰值在左侧（含 mid 自身）。不断缩小即可。
**代码**：
```python
def findPeakElement(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] < nums[mid + 1]:
            left = mid + 1
        else:
            right = mid
    return left
```
### 4. 搜索旋转排序数组（⭐⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/search-in-rotated-sorted-array/)
**难度**：中等
**题目**：整数数组 `nums` 按升序排列，数组中的值 **互不相同**。

在传递给函数之前，`nums` 在预先未知的某个下标 `k`（`0
- `1 4 4`

- `nums` 中的每个值都**独一无二**

- 题目数据保证 `nums` 在预先未知的某个下标上进行了旋转

- `-104 4`
**思路**：旋转数组的特点是分两段递增，且左段所有值 > 右段所有值。二分时先判断 mid 落在哪一段（对比 nums[mid] 和 nums[left]）。再判断 target 是否在该段的有序区间内，更新 left/right。
**代码**：
```python
def search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid
        # 左半段有序
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        # 右半段有序
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1
```
### 5. 在排序数组中查找元素的第一个和最后一个位置（⭐⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)
**难度**：中等
**题目**：给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**
```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```
**示例 2：**
```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```
**示例 3：**
```
输入：nums = [], target = 0
输出：[-1,-1]
```
**提示：**

- `0 5`

- `-109 9`

- `nums` 是一个非递减数组

- `-109 9`
**思路**：两次二分。lower_bound 找第一个 >= target 的位置，检查是否等于 target。upper_bound 找第一个 > target 的位置，减 1 即为最后一个位置。
**代码**：
```python
def searchRange(nums, target):
    def lower_bound(arr, t):
        l, r = 0, len(arr)
        while l < r:
            m = l + (r - l) // 2
            if arr[m] >= t:
                r = m
            else:
                l = m + 1
        return l

    def upper_bound(arr, t):
        l, r = 0, len(arr)
        while l < r:
            m = l + (r - l) // 2
            if arr[m] > t:
                r = m
            else:
                l = m + 1
        return l

    first = lower_bound(nums, target)
    if first == len(nums) or nums[first] != target:
        return [-1, -1]
    last = upper_bound(nums, target) - 1
    return [first, last]
```
### 6. 寻找旋转排序数组中的最小值（⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)
**难度**：中等
**题目**：已知一个长度为 `n` 的数组，预先按照升序排列，经由 `1` 到 `n` 次 **旋转**后，得到输入数组。例如，原数组 `nums = [0,1,2,4,5,6,7]` 在变化后可能得到：

- 若旋转 `4` 次，则可以得到 `[4,5,6,7,0,1,2]`

- 若旋转 `7` 次，则可以得到 `[0,1,2,4,5,6,7]`

注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]`**旋转一次**的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]` 。

给你一个元素值**互不相同**的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的**最小元素** 。

你必须设计一个时间复杂度为 `O(log n)` 的算法解决此问题。

**示例 1：**
```
输入：nums = [3,4,5,1,2]
输出：1
解释：原数组为 [1,2,3,4,5] ，旋转 3 次得到输入数组。
```
**示例 2：**
```
输入：nums = [4,5,6,7,0,1,2]
输出：0
解释：原数组为 [0,1,2,4,5,6,7] ，旋转 4 次得到输入数组。
```
**示例 3：**
```
输入：nums = [11,13,15,17]
输出：11
解释：原数组为 [11,13,15,17] ，旋转 4 次得到输入数组。
```
**提示：**

- `n == nums.length`

- `1
**思路**：旋转数组的最小值是唯一一个小于左边元素的值（或左边段与右边段的分界）。二分时比较 nums[mid] 和 nums[right]：如果 nums[mid] > nums[right]，最小值在右侧；否则在左侧（含 mid）。
**代码**：
```python
def findMin(nums):
    left, right = 0, len(nums) - 1
    while left < right:
        mid = left + (right - left) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        else:
            right = mid
    return nums[left]
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

> 💪 **第6周第36天，坚持就是胜利！**

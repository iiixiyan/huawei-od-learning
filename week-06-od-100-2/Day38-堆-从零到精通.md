# 📘 Day 38：堆 — 从零到精通

> 🎯 **学习目标**：掌握堆核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第6周 · 第38天** | [返回目录](../README.md)

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

**堆模板**
```python
import heapq
# 最小堆（Python 默认）
heap = []
heapq.heappush(heap, val)
val = heapq.heappop(heap)
# 最大堆（取负数模拟）
heapq.heappush(heap, -val)
val = -heapq.heappop(heap)
# 前 K 个最小元素
heapq.nsmallest(k, iterable)
heapq.nlargest(k, iterable)
```
**堆的典型应用场景**：
1. **Top K 问题**→ 维护大小为 K 的堆（求最大 K 个用最小堆，求最小 K 个用最大堆）
2.**数据流中位数**→ 一个最大堆 + 一个最小堆，保持平衡
3.**合并 K 个有序链表**→ 所有头节点入堆，每次弹出最小的
4.**多路归并**→ 与合并 K 个有序链表类似
5.**贪心 + 堆** → 每次取最优解，然后加入新的候选
**技巧**：
- 需要自定义优先级时，存元组 `(priority, value)`
- Python 的 heapq 不支持自定义比较器，可以用 `(priority, index, value)` 解决平局问题

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

### 1. 数组中的第K个最大元素（⭐⭐）来源：L75 / T150 / O
**来源**：[L75](https://leetcode.cn/problems/kth-largest-element-in-an-array/)
**难度**：中等
**题目**：给定整数数组 `nums` 和整数 `k`，请返回数组中第 `**k**` 个最大的元素。

请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。

你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。

**示例 1:**
```
输入: [3,2,1,5,6,4], k = 2
输出: 5
```
**示例 2:**
```
输入: [3,2,3,1,2,4,5,5,6], k = 4
输出: 4
**提示： **
- `1 5`
- `-104 4`
```
**思路**：维护大小为 K 的最小堆。遍历数组，当堆大小 < K 时直接入堆；否则若当前元素 > 堆顶，弹出堆顶并入堆新元素。最终堆顶就是第 K 大的元素。
**代码**：
```python
def findKthLargest(nums, k):
    heap = []
    for num in nums:
        if len(heap) < k:
            heapq.heappush(heap, num)
        elif num > heap[0]:
            heapq.heapreplace(heap, num)
    return heap[0]
```
### 2. 无限集中的最小数字（⭐⭐）来源：L75
**来源**：[L75](https://leetcode.cn/problems/smallest-number-in-infinite-set/)
**难度**：中等
**题目**：现有一个包含所有正整数的集合 `[1, 2, 3, 4, 5, ...]` 。

实现 `SmallestInfiniteSet` 类：

- `SmallestInfiniteSet()` 初始化 **SmallestInfiniteSet**对象以包含**所有**正整数。

- `int popSmallest()`**移除** 并返回该无限集中的最小整数。

- `void addBack(int num)` 如果正整数 `num` *不** 存在于无限集中，则将一个 `num` *添加** 到该无限集中。

**示例：**
```
输入
["SmallestInfiniteSet", "addBack", "popSmallest", "popSmallest", "popSmallest", "addBack", "popSmallest", "popSmallest", "popSmallest"]
[[], [2], [], [], [], [1], [], [], []]
输出
[null, null, 1, 2, 3, null, 1, 4, 5]
解释
SmallestInfiniteSet smallestInfiniteSet = new SmallestInfiniteSet();
smallestInfiniteSet.addBack(2);    // 2 已经在集合中，所以不做任何变更。
smallestInfiniteSet.popSmallest(); // 返回 1 ，因为 1 是最小的整数，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 2 ，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 3 ，并将其从集合中移除。
smallestInfiniteSet.addBack(1);    // 将 1 添加到该集合中。
smallestInfiniteSet.popSmallest(); // 返回 1 ，因为 1 在上一步中被添加到集合中，
// 且 1 是最小的整数，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 4 ，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 5 ，并将其从集合中移除。
```
**提示：**

- `1
**思路**：用一个最小堆存储当前集合中的数字，一个 visited 集合去重。初始化时把所有 1~1000 入堆。popSmallest 弹出堆顶；addBack 时如果数字不在 visited 中，入堆并标记。
**代码**：
```python
class SmallestInfiniteSet:
    def __init__(self):
        self.heap = list(range(1, 1001))
        heapq.heapify(self.heap)
        self.removed = set()

    def popSmallest(self):
        val = heapq.heappop(self.heap)
        self.removed.add(val)
        return val

    def addBack(self, num):
        if num in self.removed:
            self.removed.remove(num)
            heapq.heappush(self.heap, num)
```
### 3. 最大子序列的分数（⭐⭐⭐）来源：L75
**来源**：[L75](https://leetcode.cn/problems/maximum-score-from-removing-substrings/)
**难度**：中等
**题目**：给你一个字符串 `s` 和两个整数 `x` 和 `y` 。你可以执行下面两种操作任意次。

- 删除子字符串 `"ab"` 并得到 `x` 分。

- 比方说，从 `"c**ab**xbae"` 删除 `ab` ，得到 `"cxbae"` 。

- 删除子字符串`"ba"` 并得到 `y` 分。

- 比方说，从 `"cabx**ba**e"` 删除 `ba` ，得到 `"cabxe"` 。

请返回对 `s` 字符串执行上面操作若干次能得到的最大得分。

**示例 1：**
```
输入：s = "cdbcbbaaabab", x = 4, y = 5
输出：19
解释：
- 删除 "cdbcbbaaabab" 中加粗的 "ba" ，得到 s = "cdbcbbaaab" ，加 5 分。
- 删除 "cdbcbbaaab" 中加粗的 "ab" ，得到 s = "cdbcbbaa" ，加 4 分。
- 删除 "cdbcbbaa" 中加粗的 "ba" ，得到 s = "cdbcba" ，加 5 分。
- 删除 "cdbcba" 中加粗的 "ba" ，得到 s = "cdbc" ，加 5 分。
总得分为 5 + 4 + 5 + 5 = 19 。
```
**示例 2：**
```
输入：s = "aabbaaxybbaabb", x = 5, y = 4
输出：20
```
**提示：**

- `1 5`

- `1 4`

- `s` 只包含小写英文字母。
**思路**：将两个数组按 nums2 降序配对。遍历时维持一个大小为 k 的最小堆存 nums1 的值，同时维护 sum1。对每个位置，堆满后计算分数 = sum1 * nums2[i]，取最大值。
**代码**：
```python
def maxScore(nums1, nums2, k):
    pairs = sorted(zip(nums1, nums2), key=lambda x: -x[1])
    heap = []
    sum1 = 0
    res = 0
    for n1, n2 in pairs:
        heapq.heappush(heap, n1)
        sum1 += n1
        if len(heap) > k:
            sum1 -= heapq.heappop(heap)
        if len(heap) == k:
            res = max(res, sum1 * n2)
    return res
```
### 4. IPO（⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/ipo/)
**难度**：困难
**题目**：假设 力扣（LeetCode）即将开始 **IPO**。为了以更高的价格将股票卖给风险投资公司，力扣 希望在 IPO 之前开展一些项目以增加其资本。 由于资源有限，它只能在 IPO 之前完成最多 `k` 个不同的项目。帮助 力扣 设计完成最多 `k` 个不同项目后得到最大总资本的方式。

给你 `n` 个项目。对于每个项目 `i` ，它都有一个纯利润 `profits[i]` ，和启动该项目需要的最小资本 `capital[i]` 。

最初，你的资本为 `w` 。当你完成一个项目时，你将获得纯利润，且利润将被添加到你的总资本中。

总而言之，从给定项目中选择**最多**`k` 个不同项目的列表，以**最大化最终资本** ，并输出最终可获得的最多资本。

答案保证在 32 位有符号整数范围内。

**示例 1：**
```
输入：k = 2, w = 0, profits = [1,2,3], capital = [0,1,1]
输出：4
解释：
由于你的初始资本为 0，你仅可以从 0 号项目开始。
在完成后，你将获得 1 的利润，你的总资本将变为 1。
此时你可以选择开始 1 号或 2 号项目。
由于你最多可以选择两个项目，所以你需要完成 2 号项目以获得最大的资本。
因此，输出最后最大化的资本，为 0 + 1 + 3 = 4。
```
**示例 2：**
```
输入：k = 3, w = 0, profits = [1,2,3], capital = [0,1,2]
输出：6
```
**提示：**

- `1 5`

- `0 9`

- `n == profits.length`

- `n == capital.length`

- `1 5`

- `0 4`

- `0 9`
**思路**：贪心 + 堆。先按 capital 排序项目。维护一个最大堆（存利润），把所有 capital ≤ w 的项目利润入堆。每次弹出最大利润的项目，w 增加。重复 k 次。
**代码**：
```python
def findMaximizedCapital(k, w, profits, capital):
    n = len(profits)
    projects = sorted(zip(capital, profits))
    heap = []
    i = 0
    for _ in range(k):
        while i < n and projects[i][0] <= w:
            heapq.heappush(heap, -projects[i][1])
            i += 1
        if not heap:
            break
        w += -heapq.heappop(heap)
    return w
```
### 5. 查找和最小的K对数字（⭐⭐）来源：T150 / O
**来源**：[T150](https://leetcode.cn/problems/find-k-pairs-with-smallest-sums/)
**难度**：中等
**题目**：给定两个以 **非递减顺序排列** 的整数数组 `nums1` 和 `nums2` , 以及一个整数 `k` 。

定义一对值 `(u,v)`，其中第一个元素来自 `nums1`，第二个元素来自 `nums2` 。

请找到和最小的 `k` 个数对 `(u1,v1)`, ` (u2,v2)`  ...  `(uk,vk)` 。

**示例 1:**
```
输入: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
输出: [[1,2],[1,4],[1,6]]
解释: 返回序列中的前 3 对数：
[1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```
**示例 2:**
```
输入: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
输出: [[1,1],[1,1]]
解释: 返回序列中的前 2 对数：
[1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```
**提示:**

- `1 5`

- `-109 9`

- `nums1` 和 `nums2` 均为 **升序排列**

- `1 4`

- `k
**思路**：多路归并。所有组合可以看作 nums1[i] + nums2[0] 的 i 条"链表"。每次取出最小的和，并将对应的 nums1[i] + nums2[j+1] 入堆。注意去重。
**代码**：
```python
def kSmallestPairs(nums1, nums2, k):
    if not nums1 or not nums2:
        return []
    heap = []
    # 初始化：每个 nums1[i] + nums2[0]
    for i in range(min(k, len(nums1))):
        heapq.heappush(heap, (nums1[i] + nums2[0], i, 0))
    res = []
    while heap and len(res) < k:
        s, i, j = heapq.heappop(heap)
        res.append([nums1[i], nums2[j]])
        if j + 1 < len(nums2):
            heapq.heappush(heap, (nums1[i] + nums2[j + 1], i, j + 1))
    return res
```
### 6. 数据流的中位数（⭐⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/find-median-from-data-stream/)
**难度**：困难
**题目**：**中位数**是有序整数列表中的中间值。如果列表的大小是偶数，则没有中间值，中位数是两个中间值的平均值。

- 例如 `arr = [2,3,4]` 的中位数是 `3` 。

- 例如 `arr = [2,3]` 的中位数是 `(2 + 3) / 2 = 2.5` 。

实现 MedianFinder 类:

-
`MedianFinder()` 初始化 `MedianFinder` 对象。

-
`void addNum(int num)` 将数据流中的整数 `num` 添加到数据结构中。

-
`double findMedian()` 返回到目前为止所有元素的中位数。与实际答案相差 `10-5` 以内的答案将被接受。

**示例 1：**
```
输入
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
输出
[null, null, null, 1.5, null, 2.0]
解释
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // 返回 1.5 ((1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```
**提示:**

- `-105 5`

- 在调用 `findMedian` 之前，数据结构中至少有一个元素

- 最多 `5 * 104` 次调用 `addNum` 和 `findMedian` *思路**：维护两个堆：最大堆（存左半部分）和最小堆（存右半部分）。保持两个堆的大小平衡（左堆比右堆多 0 或 1 个元素）。添加元素时先入左堆再调整到右堆，保持所有左堆元素 ≤ 右堆元素。
**代码**：
```python
class MedianFinder:
    def __init__(self):
        self.left = []   # 最大堆（存负数）
        self.right = []  # 最小堆

    def addNum(self, num):
        # 先入左堆（最大堆）
        heapq.heappush(self.left, -num)
        # 平衡：左堆最大 ≤ 右堆最小
        if self.left and self.right and -self.left[0] > self.right[0]:
            val = -heapq.heappop(self.left)
            heapq.heappush(self.right, val)
        # 平衡大小：左堆最多比右堆多 1
        if len(self.left) > len(self.right) + 1:
            val = -heapq.heappop(self.left)
            heapq.heappush(self.right, val)
        elif len(self.right) > len(self.left):
            val = heapq.heappop(self.right)
        

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

> 💪 **第6周第38天，坚持就是胜利！**

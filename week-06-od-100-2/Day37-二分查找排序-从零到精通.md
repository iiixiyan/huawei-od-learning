# 📘 Day 37：二分查找排序 — 从零到精通

> 🎯 **学习目标**：掌握二分查找排序核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第6周 · 第37天** | [返回目录](../README.md)

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

**按值二分（答案二分）模板**
```python
def feasible(x):
    # 判断当前猜测值 x 是否可行
    pass
left, right = min_possible, max_possible
while left < right:
    mid = left + (right - left) // 2
    if feasible(mid):
        right = mid
    else:
        left = mid + 1
return left
```
**排序算法速览**：
- **快速排序**：O(n log n)，不稳定，原地排序
- **归并排序**：O(n log n)，稳定，需要 O(n) 额外空间
- **Python 内置**：`list.sort()` / `sorted()` 是 Timsort（归并+插入），稳定且高效
**链表排序**：归并排序（找中点 + 合并两个有序链表）

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

### 1. 猜数字大小（⭐）来源：L75
**来源**：[L75](https://leetcode.cn/problems/guess-number-higher-or-lower/)
**难度**：简单
**题目**：我们正在玩猜数字游戏。猜数字游戏的规则如下：

我会从 `1` 到 `n` 随机选择一个数字。 请你猜选出的是哪个数字。（我选的数字在整个游戏中保持不变）。

如果你猜错了，我会告诉你，我选出的数字比你猜测的数字大了还是小了。

你可以通过调用一个预先定义好的接口 `int guess(int num)` 来获取猜测结果，返回值一共有三种可能的情况：

- `-1`：你猜的数字比我选出的数字大 （即 `num > pick`）。

- `1`：你猜的数字比我选出的数字小 （即 `num

返回我选出的数字。

**示例 1：**
```
输入：n = 10, pick = 6
输出：6
```
**示例 2：**
```
输入：n = 1, pick = 1
输出：1
```
**示例 3：**
```
输入：n = 2, pick = 1
输出：1
```
**提示：**

- `1 31 - 1`

- `1
**思路**：标准二分。`guess(n)` 返回 -1/0/1，按照二分模板找目标值。注意 mid 计算用 `left + (right - left) // 2` 避免溢出。
**代码**：
```python
def guessNumber(n):
    left, right = 1, n
    while left <= right:
        mid = left + (right - left) // 2
        res = guess(mid)
        if res == 0:
            return mid
        elif res == 1:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```
### 2. 爱吃香蕉的珂珂（⭐⭐）来源：L75 / T150 / O
**来源**：[L75](https://leetcode.cn/problems/koko-eating-bananas/)
**难度**：中等
**题目**：珂珂喜欢吃香蕉。这里有 `n` 堆香蕉，第 `i` 堆中有 `piles[i]` 根香蕉。警卫已经离开了，将在 `h` 小时后回来。

珂珂可以决定她吃香蕉的速度 `k` （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 `k` 根。如果这堆香蕉少于 `k` 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 `h` 小时内吃掉所有香蕉的最小速度 `k`（`k` 为整数）。

**示例 1：**
```
输入：piles = [3,6,7,11], h = 8
输出：4
```
**示例 2：**
```
输入：piles = [30,11,23,4,20], h = 5
输出：30
```
**示例 3：**
```
输入：piles = [30,11,23,4,20], h = 6
输出：23
```
**提示：**

- `1 4`

- `piles.length 9`

- `1 9`
**思路**：按值二分。速度 K 的范围是 [1, max(piles)]。feasible(K) 判断能否在 H 小时内吃完：对每堆香蕉，需要 `(pile + K - 1) // K` 小时。找最小可行 K。
**代码**：
```python
def minEatingSpeed(piles, h):
    def can_finish(k):
        total = 0
        for p in piles:
            total += (p + k - 1) // k
        return total <= h

    left, right = 1, max(piles)
    while left < right:
        mid = left + (right - left) // 2
        if can_finish(mid):
            right = mid
        else:
            left = mid + 1
    return left
```
### 3. 寻找两个正序数组的中位数（⭐⭐⭐）来源：T150
**来源**：[T150](https://leetcode.cn/problems/median-of-two-sorted-arrays/)
**难度**：困难
**题目**：给定两个大小分别为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。请你找出并返回这两个正序数组的 **中位数** 。

算法的时间复杂度应该为 `O(log (m+n))` 。

**示例 1：**
```
输入：nums1 = [1,3], nums2 = [2]
输出：2.00000
解释：合并数组 = [1,2,3] ，中位数 2
```
**示例 2：**
```
输入：nums1 = [1,2], nums2 = [3,4]
输出：2.50000
解释：合并数组 = [1,2,3,4] ，中位数 (2 + 3) / 2 = 2.5
```
**提示：**

- `nums1.length == m`

- `nums2.length == n`

- `0 6 6`
**思路**：核心思路是对较短的数组做二分划分线。保证左半部分的最大值 ≤ 右半部分的最小值。中位数取决于总长度奇偶性。关键是理解划分线 i 和 j 的关系：i + j = (m + n + 1) // 2。
**代码**：
```python
def findMedianSortedArrays(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    m, n = len(nums1), len(nums2)
    left, right = 0, m
    while left <= right:
        i = left + (right - left) // 2
        j = (m + n + 1) // 2 - i
        left1 = nums1[i - 1] if i > 0 else -float('inf')
        right1 = nums1[i] if i < m else float('inf')
        left2 = nums2[j - 1] if j > 0 else -float('inf')
        right2 = nums2[j] if j < n else float('inf')
        if left1 <= right2 and left2 <= right1:
            if (m + n) % 2 == 0:
                return (max(left1, left2) + min(right1, right2)) / 2.0
            else:
                return max(left1, left2)
        elif left1 > right2:
            right = i - 1
        else:
            left = i + 1
    return 0.0
```
### 4. 排序链表（⭐⭐⭐）来源：T150 / O
**来源**：[T150](https://leetcode.cn/problems/sort-list/)
**难度**：中等
**题目**：给你链表的头结点 `head` ，请将其按 **升序**排列并返回**排序后的链表** 。

**示例 1：**
```
*
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```
**示例 2：**
```
*
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```
**示例 3：**
```
输入：head = []
输出：[]
```
**提示：**

- 链表中节点的数目在范围 `[0, 5 * 104]` 内

- `-105 5`

**进阶：**你可以在 `O(n log n)` 时间复杂度和常数级空间复杂度下，对链表进行排序吗？
**思路**：链表归并排序。用快慢指针找中点，递归排序左右两半，然后合并两个有序链表。时间 O(n log n)，空间 O(log n)（递归栈）。
**代码**：
```python
def sortList(head):
    if not head or not head.next:
        return head

    # 快慢指针找中点
    slow, fast = head, head.next
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    mid = slow.next
    slow.next = None  # 断开链表

    left = sortList(head)
    right = sortList(mid)
    return merge(left, right)

def merge(l1, l2):
    dummy = ListNode(0)
    cur = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            cur.next, l1 = l1, l1.next
        else:
            cur.next, l2 = l2, l2.next
        cur = cur.next
    cur.next = l1 or l2
    return dummy.next
```
### 5. 相对排序数组（⭐）来源：O
**来源**：[O](https://leetcode.cn/problems/relative-sort-array/)
**难度**：简单
**题目**：给你两个数组，`arr1` 和 `arr2`，`arr2` 中的元素各不相同，`arr2` 中的每个元素都出现在 `arr1` 中。

对 `arr1` 中的元素进行排序，使 `arr1` 中项的相对顺序和 `arr2` 中的相对顺序相同。未在 `arr2` 中出现过的元素需要按照升序放在 `arr1` 的末尾。

**示例 1：**
```
输入：arr1 = [2,3,1,3,2,4,6,7,9,2,19], arr2 = [2,1,4,3,9,6]
输出：[2,2,2,1,4,3,3,9,6,7,19]
```
**示例  2:**
```
输入：arr1 = [28,6,22,8,44,17], arr2 = [22,28,8,6]
输出：[22,28,8,6,17,44]
```
**提示：**

- `1
**思路**：计数排序。先统计 arr1 中每个数的出现次数。按 arr2 的顺序输出，再按升序输出剩余数字。因为值范围小（0~1000），用数组计数。
**代码**：
```python
def relativeSortArray(arr1, arr2):
    max_val = max(arr1) if arr1 else 0
    count = [0] * (max_val + 1)
    for x in arr1:
        count[x] += 1

    res = []
    for x in arr2:
        res.extend([x] * count[x])
        count[x] = 0

    for i in range(max_val + 1):
        if count[i] > 0:
            res.extend([i] * count[i])
    return res
```
### 6. 山脉数组的峰顶索引（⭐）来源：O
**来源**：[O](https://leetcode.cn/problems/peak-index-in-a-mountain-array/)
**难度**：中等
**题目**：给定一个长度为 `n` 的整数 **山脉 **数组 `arr` ，其中的值递增到一个 **峰值元素** 然后递减。

返回峰值元素的下标。

你必须设计并实现时间复杂度为 `O(log(n))` 的解决方案。

**示例 1：**
```
输入：arr = [0,1,0]
输出：1
```
**示例 2：**
```
输入：arr = [0,2,1,0]
输出：1
```
**示例 3：**
```
输入：arr = [0,10,5,2]
输出：1
```
**提示：**

- `3 5`

- `0 6`

- 题目数据 **保证** `arr` 是一个山脉数组
**思路**：山脉数组先增后减，峰值满足 arr[mid] > arr[mid-1] 且 arr[mid] > arr[mid+1]。二分法：如果 arr[mid] < arr[mid+1]，说明在上升段，left 右移；否则在下降段，right 左移。
**代码**：
```python
def peakIndexInMountainArray(arr):
    left, right = 0, len(arr) - 1
    while left < right:
        mid = left + (right - left) // 2
        if arr[mid] < arr[mid + 1]:
            left = mid + 1
        else:
            right = mid
    return left
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

> 💪 **第6周第37天，坚持就是胜利！**

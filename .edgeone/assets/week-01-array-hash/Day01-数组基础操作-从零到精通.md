# 📘 Day 1：数组基础操作 — 从零到精通

> 华为OD备考 · 第一周 · 第1天
> 适合完全零基础的初学者，从"数组是什么"开始讲起。
> 核心套路：快慢指针（读写指针）、头尾指针（左右夹逼）

---

## 📖 目录

一、数组到底是什么？🤔
二、Day1 的三大核心套路 🎯
　⚡ 套路一：快慢指针（读写指针）
　⚡ 套路二：头尾指针（左右夹逼）
　⚡ 套路三：合并有序数组
三、高频题精讲（5道题）
　📝 第1题：交替合并字符串
　📝 第2题：合并有序数组
　📝 第3题：移除元素
　📝 第4题：删除有序数组重复项
　📝 第5题：多数元素
四、性能优化与常见坑
五、OD机考实战建议
六、课后作业

---

## 一、数组到底是什么？🤔

### 🏠 生活例子：一列邮筒

想象一条街上并排站着 **一排邮筒**，每个邮筒上有一个编号。

```
邮筒编号：  0     1     2     3     4
           ┌─┐   ┌─┐   ┌─┐   ┌─┐   ┌─┐
内容：     │ 5 │   │ 3 │   │ 8 │   │ 1 │   │ 9 │
           └─┘   └─┘   └─┘   └─┘   └─┘
```

**数组**就是计算机里的"一排邮筒"。每个邮筒（叫做**元素**）都有一个编号（叫做**下标**或索引），编号从 **0** 开始。

在 Python 里，用方括号 `[]` 表示数组：

```python
nums = [5, 3, 8, 1, 9]
# 下标：0  1  2  3  4
```

### 🔑 数组最重要的三个特性

1. **连续存放**：邮筒一个挨一个，整整齐齐
2. **下标访问 O(1)**：知道编号，直接找到——`nums[2]` 瞬间拿到 `8`
3. **大小固定**（在大部分语言中）：创建时多大就多大（Python列表自动扩容，但底层逻辑类似）

### ✏️ 基础操作速查

```python
arr = [10, 20, 30, 40, 50]

# 取长度
print(len(arr))          # 5

# 按下标取值
print(arr[0])            # 10
print(arr[-1])           # 50（-1 是最后一个）

# 修改值
arr[2] = 35              # [10, 20, 35, 40, 50]

# 切片（取一段）
print(arr[1:4])          # [20, 35, 40]  （左闭右开：包含1，不包含4）

# 追加
arr.append(60)           # [10, 20, 35, 40, 50, 60]

# 插入
arr.insert(2, 99)        # [10, 20, 99, 35, 40, 50, 60]

# 删除
arr.pop()                # 删最后一个
arr.pop(2)               # 删除下标2的元素
arr.remove(20)           # 删除值为20的元素（只删第一个）

# 是否包含
print(30 in arr)         # True/False
```

### 🚨 一句话记住：数组是"排排坐"，下标从0开始，通过编号秒找元素。

---

## 二、Day1 的三大核心套路 🎯

整个 Day1 讲的 5 道题，总结为 **3 种套路**。学会这 3 种，数组基础操作就入门了。

---

### ⚡ 套路一：快慢指针（读写指针）

#### 生活例子：图书馆占座 📚

想象你在图书馆有一排空座位。你抱着5本书要放：

```
空座位：  [  ] [  ] [  ] [  ] [  ] [  ] [  ]
```

你（**慢指针**）负责放书，你的朋友（**快指针**）负责往前探路。

```
朋友探路 → 发现第3个座位是脏的（要跳过）
你放书 → 跳过脏座位，继续放
```

**快指针**（`fast`）负责"探路"——遍历每个元素
**慢指针**（`slow`）负责"写入"——记录有效元素放哪里

#### 代码模板

```python
def template(nums):
    slow = 0                        # 慢指针：指向"下一个该放哪"
    for fast in range(len(nums)):   # 快指针：遍历每个元素
        if 条件(nums[fast]):        # 如果当前元素满足条件
            nums[slow] = nums[fast] # 把它放到slow位置
            slow += 1               # slow前进一格
    return slow                     # slow就是有效元素的数量
```

#### 手把手推演：移除所有3

```
数组：[2, 3, 3, 1, 4]
目标：移除所有3，返回有效长度

初始：
  slow=0, fast=0
  [2, 3, 3, 1, 4]
   ↑
  fast,slow

第1步：fast=0, nums[0]=2 ≠ 3 → 保留
  nums[slow]=nums[fast] → nums[0]=2
  slow→1
  [2, 3, 3, 1, 4]
      ↑
   slow=1  fast=1

第2步：fast=1, nums[1]=3 == 3 → 跳过
  slow不动（不写入）
  [2, 3, 3, 1, 4]
      ↑   ↑
  slow=1  fast=2

第3步：fast=2, nums[2]=3 == 3 → 跳过
  slow不动
  [2, 3, 3, 1, 4]
      ↑      ↑
  slow=1     fast=3

第4步：fast=3, nums[3]=1 ≠ 3 → 保留
  nums[slow]=nums[fast] → nums[1]=1
  slow→2
  [2, 1, 3, 1, 4]
         ↑      ↑
  slow=2        fast=4

第5步：fast=4, nums[4]=4 ≠ 3 → 保留
  nums[slow]=nums[fast] → nums[2]=4
  slow→3
  [2, 1, 4, 1, 4]
            ↑
  slow=3

结束：slow=3，有效数组是 [2, 1, 4]，后面[1,4]是残留不管
```

**图解快慢指针轨迹**：

```
初始：    [2, 3, 3, 1, 4]
         S,F

跳过3：  [2, 3, 3, 1, 4]
            S  F

跳过3：  [2, 3, 3, 1, 4]
            S     F

写入1：  [2, 1, 3, 1, 4]
               S     F

写入4：  [2, 1, 4, 1, 4]
                  S     F   ← 完成！有效[2,1,4]
```

#### 小白口诀 🎵

> **快指针探路，慢指针记录。符合条件就写入，不符合就跳过。**

---

### ⚡ 套路二：头尾指针（左右夹逼）

#### 生活例子：双人拔河 🏋️

你和朋友站在一条绳子两端，同时往中间走。

```
左你  →                    ← 友右
[1] [2] [3] [4] [5] [6]
```

你们俩从两端往中间走，边走边干一些活（比如交换、检查条件）。这就是**头尾指针**。

#### 适用场景

- 有序数组的两数之和
- 反转数组
- 移除元素（把要删的扔到末尾）
- 接雨水

#### 代码模板

```python
def two_pointer_template(nums):
    left = 0
    right = len(nums) - 1
    while left < right:
        # 根据条件移动指针
        if 条件A:
            left += 1
        elif 条件B:
            right -= 1
        else:
            # 找到答案
            return [left, right]
    return [-1, -1]  # 没找到
```

#### 手把手推演：反转数组

```
数组：[1, 2, 3, 4, 5, 6]
目标：反转成 [6, 5, 4, 3, 2, 1]

初始：
  L=0, R=5
  [1, 2, 3, 4, 5, 6]
   ↑              ↑
   L              R

第1步：交换L和R的值
  交换→ [6, 2, 3, 4, 5, 1]
  L→1, R→4
      [6, 2, 3, 4, 5, 1]
       ↑           ↑
       L           R

第2步：交换
  交换→ [6, 5, 3, 4, 2, 1]
  L→2, R→3
      [6, 5, 3, 4, 2, 1]
          ↑     ↑
          L     R

第3步：交换
  交换→ [6, 5, 4, 3, 2, 1]
  L→3, R→2
  L > R  → 结束！
```

**图解**：

```
[1, 2, 3, 4, 5, 6]
 ↑              ↑
 L              R
 └──交换──→

[6, 2, 3, 4, 5, 1]
    ↑        ↑
    L        R
    └─交换─→

[6, 5, 3, 4, 2, 1]
       ↑  ↑
       L  R
       └交换┘

[6, 5, 4, 3, 2, 1]
```

#### 小白口诀 🎵

> **左指针右指针，两人往中走。碰头就停手，中间来碰头。**

---

### ⚡ 套路三：合并有序数组

#### 生活例子：两摞按大小排好的扑克牌 🃏

你有两摞牌，每摞都按从小到大的顺序排好了：

```
牌堆A：2, 4, 7
牌堆B：1, 3, 5, 8
```

你要把它们合并成一摞，还要保持从小到大。怎么做？

**方法**：每次看两摞最上面的牌，谁小就取谁放进去。

```
A: [2, 4, 7]    B: [1, 3, 5, 8]
              ↓
比较A[0]=2 和 B[0]=1 → 1小，取1
结果：[1]
A: [2, 4, 7]    B: [3, 5, 8]
              ↓
比较2和3 → 2小，取2
结果：[1, 2]
A: [4, 7]       B: [3, 5, 8]
              ↓
比较4和3 → 3小，取3
结果：[1, 2, 3]
...以此类推
```

#### 代码模板（从后往前合并）

```python
def merge(nums1, m, nums2, n):
    """
    nums1: 第一个数组（有足够空间）
    m: nums1有效元素个数
    nums2: 第二个数组
    n: nums2有效元素个数
    """
    i, j, k = m - 1, n - 1, m + n - 1
    # i指向nums1最后一个有效元素
    # j指向nums2最后一个有效元素
    # k指向合并后的最后一个位置

    while j >= 0:  # 只要nums2还有没合并的
        if i >= 0 and nums1[i] > nums2[j]:
            nums1[k] = nums1[i]  # 把大的放后面
            i -= 1
        else:
            nums1[k] = nums2[j]  # 否则放nums2的
            j -= 1
        k -= 1
```

**关键点**：从后往前合并，这样不会覆盖nums1中还没处理的元素。

---

## 三、高频题精讲（5道题）

---

### 📝 第1题：交替合并字符串

**题目**：给定两个字符串 `word1` 和 `word2`，交替合并它们。先放 `word1` 的第一个字符，再放 `word2` 的第一个字符，依此类推。如果某个字符串先走完，直接补上剩余部分。

**难度**：⭐ 简单

**示例**：
```
输入：word1 = "abc", word2 = "pqr"
输出："apbqcr"

输入：word1 = "ab", word2 = "pqrs"
输出："apbqrs"    （word1先走完，补上word2的剩余"rs"）
```

#### 解题思路

这题不涉及快慢指针，而是纯粹的**双指针交替推进**。

用两个指针 `i` 和 `j` 分别指向两个字符串的开头，每次各取一个字符拼接到结果中。当某个指针到头了，就只取另一个字符串的剩余部分。

#### 手把手推演

```
word1 = "a  b  c"
         0  1  2
word2 = "p  q  r  s"
         0  1  2  3

初始：i=0, j=0, result=""

第1轮：取word1[0]='a', 取word2[0]='p'
  result = "ap", i=1, j=1

第2轮：取word1[1]='b', 取word2[1]='q'
  result = "apbq", i=2, j=2

第3轮：取word1[2]='c', 取word2[2]='r'
  result = "apbqcr", i=3, j=3

此时i==len(word1)，word1已走完
补上word2剩余：word2[3]='s'
result = "apbqcr s"
```

#### 代码实现

```python
def mergeAlternately(word1: str, word2: str) -> str:
    i, j = 0, 0
    result = []

    # 交替取字符，直到其中一个走完
    while i < len(word1) and j < len(word2):
        result.append(word1[i])
        result.append(word2[j])
        i += 1
        j += 1

    # 补上剩余部分（两个只有一个会执行）
    if i < len(word1):
        result.append(word1[i:])
    if j < len(word2):
        result.append(word2[j:])

    return "".join(result)
```

#### 复杂度分析

- **时间复杂度**：O(n+m)，n和m分别是两个字符串的长度，每个字符刚好处理一次
- **空间复杂度**：O(n+m)，结果字符串的长度

#### 简化版（Python一行流）

```python
def mergeAlternately(word1: str, word2: str) -> str:
    # zip只取较短长度的配对，然后补上更长的剩余部分
    return "".join(a + b for a, b in zip(word1, word2)) + word1[len(word2):] + word2[len(word1):]
```

#### 面试追问方向

**Q：如果要求空间复杂度O(1)呢？**（不能额外创建字符串）
A：Python字符串不可变，所以必须新建。但在C/C++中可以在原字符串上修改。

**Q：如果要求交替顺序反过来？**
A：先取word2再取word1，把循环里的两行交换即可。

---

### 📝 第2题：合并有序数组

**题目**：给你两个按**非递减顺序**排列的整数数组 `nums1` 和 `nums2`，以及两个整数 `m` 和 `n`，分别表示 `nums1` 和 `nums2` 中的元素数目。请你合并 `nums2` 到 `nums1` 中，使合并后的数组同样按非递减顺序排列。

**注意**：`nums1` 的长度是 `m + n`，前 `m` 个是有效元素，后 `n` 个是0（占位）。

**难度**：⭐⭐ 简单-中等

**示例**：
```
输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]

解释：前3个是[1,2,3]，后3个是0占位。
     合并[2,5,6]进来，结果[1,2,2,3,5,6]
```

#### 解题思路

**核心思想：从后往前合并！**

为什么要从后往前？因为 `nums1` 后面有 `n` 个空位（全是0），从后往前放最大的元素，不会覆盖 `nums1` 还没处理的有效数据。

如果从前往后合并，每次插入都要移动后面的元素，效率很低。

#### 手把手推演

```
nums1 = [1, 2, 3, 0, 0, 0]    m=3
nums2 = [2, 5, 6]             n=3

初始：
  i = m-1 = 2  → nums1[2] = 3
  j = n-1 = 2  → nums2[2] = 6
  k = m+n-1 = 5 → nums1[5] = 0

第1步：比较 nums1[2]=3 和 nums2[2]=6
  6 > 3 → nums1[5] = 6, j=1, k=4
  [1, 2, 3, 0, 0, 6]

第2步：比较 nums1[2]=3 和 nums2[1]=5
  5 > 3 → nums1[4] = 5, j=0, k=3
  [1, 2, 3, 0, 5, 6]

第3步：比较 nums1[2]=3 和 nums2[0]=2
  3 > 2 → nums1[3] = 3, i=1, k=2
  [1, 2, 3, 3, 5, 6]

第4步：比较 nums1[1]=2 和 nums2[0]=2
  2 == 2 → 选nums2的（条件设<=即可），nums2[0]=2
  nums1[2] = 2, j=-1, k=1
  [1, 2, 2, 3, 5, 6]

j=-1 → nums2已全部合并，结束！
结果：[1, 2, 2, 3, 5, 6]
```

#### 图解全过程

```
初始化：
  nums1: [1,  2,  3,  0,  0,  0]
          ↑               ↑
          i=2             k=5
  nums2: [2,  5,  6]
               ↑
              j=2

第1步：3 vs 6 → 6更大 → 放末尾
  nums1: [1,  2,  3,  0,  0,  6]
          ↑           ↑
          i=2         k=4
  nums2: [2,  5,  6]
               ↑
              j=1

第2步：3 vs 5 → 5更大 → 放末尾
  nums1: [1,  2,  3,  0,  5,  6]
          ↑       ↑
          i=2     k=3
  nums2: [2,  5,  6]
          ↑
         j=0

第3步：3 vs 2 → 3更大 → 放末尾
  nums1: [1,  2,  3,  3,  5,  6]
               ↑   ↑
              i=1  k=2
  nums2: [2,  5,  6]
          ↑
         j=0

第4步：2 vs 2 → 相等取nums2 → 放末尾
  nums1: [1,  2,  2,  3,  5,  6]
               ↑
              k=1
  j=-1 → 完成！
```

#### 代码实现

```python
def merge(nums1: list[int], m: int, nums2: list[int], n: int) -> None:
    """
    Do not return anything, modify nums1 in-place instead.
    """
    i = m - 1          # nums1最后一个有效元素
    j = n - 1          # nums2最后一个元素
    k = m + n - 1      # 合并后的最后一个位置

    while j >= 0:      # 只要nums2还有元素
        if i >= 0 and nums1[i] > nums2[j]:
            nums1[k] = nums1[i]  # 把大的放后面
            i -= 1
        else:
            nums1[k] = nums2[j]  # 放nums2的
            j -= 1
        k -= 1
```

#### 复杂度分析

- **时间复杂度**：O(m+n)，每个元素处理一次
- **空间复杂度**：O(1)，原地修改

#### 面试追问方向

**Q：为什么从后往前？**
A：从后往前可以利用 nums1 末尾的空余空间，避免覆盖未处理的元素。

**Q：如果 nums1 没有额外空间呢？**
A：那就创建新数组 `nums1[:m] + nums2` 再排序，但面试官期待的是上面O(1)空间的解法。

**Q：如果是多个有序数组合并呢？**
A：使用**最小堆**（优先队列）——每次从所有数组中取最小的元素。

---

### 📝 第3题：移除元素

**题目**：给你一个数组 `nums` 和一个值 `val`，你需要**原地**移除所有数值等于 `val` 的元素，并返回移除后数组的新长度。不要使用额外的数组空间，必须仅使用 O(1) 额外空间并**原地**修改输入数组。元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**难度**：⭐ 简单

**示例**：
```
输入：nums = [3, 2, 2, 3], val = 3
输出：2, nums = [2, 2, _, _]
解释：移除所有3，剩余[2,2]，长度2。后面是什么不重要。

输入：nums = [0, 1, 2, 2, 3, 0, 4, 2], val = 2
输出：5, nums = [0, 1, 3, 0, 4, _, _, _]
```

#### 解题思路

这就是**快慢指针**的经典应用！

**慢指针 slow**：指向"下一个有效元素应该放的位置"
**快指针 fast**：遍历数组的每个元素

核心逻辑：如果当前元素 `nums[fast] != val`，说明它应该保留，把它放到 `slow` 位置，然后 `slow++`。

#### 手把手推演

```
nums = [0, 1, 2, 2, 3, 0, 4, 2], val = 2

初始：slow=0, fast=0
遍历开始：

fast=0: nums[0]=0 ≠ 2 → 保留
  nums[slow] = nums[fast] → nums[0]=0
  slow=1, fast=1
  [0, 1, 2, 2, 3, 0, 4, 2]

fast=1: nums[1]=1 ≠ 2 → 保留
  nums[slow] = nums[fast] → nums[1]=1
  slow=2, fast=2
  [0, 1, 2, 2, 3, 0, 4, 2]

fast=2: nums[2]=2 == 2 → 跳过
  slow=2, fast=3（slow不动）
  [0, 1, 2, 2, 3, 0, 4, 2]

fast=3: nums[3]=2 == 2 → 跳过
  slow=2, fast=4
  [0, 1, 2, 2, 3, 0, 4, 2]

fast=4: nums[4]=3 ≠ 2 → 保留
  nums[slow]=nums[fast] → nums[2]=3
  slow=3, fast=5
  [0, 1, 3, 2, 3, 0, 4, 2]

fast=5: nums[5]=0 ≠ 2 → 保留
  nums[slow]=nums[fast] → nums[3]=0
  slow=4, fast=6
  [0, 1, 3, 0, 3, 0, 4, 2]

fast=6: nums[6]=4 ≠ 2 → 保留
  nums[slow]=nums[fast] → nums[4]=4
  slow=5, fast=7
  [0, 1, 3, 0, 4, 0, 4, 2]

fast=7: nums[7]=2 == 2 → 跳过
  slow=5, fast=8（结束）

结果：slow=5，有效数组前5个是[0,1,3,0,4]
```

#### 图解

```
数组：[0, 1, 2, 2, 3, 0, 4, 2]  val=2
      S/F（初始）

Step 0: [0, 1, 2, 2, 3, 0, 4, 2]  0≠2，保留
         ↑
        S/F

Step 1: [0, 1, 2, 2, 3, 0, 4, 2]  1≠2，保留
            ↑
           S/F

Step 2: [0, 1, 2, 2, 3, 0, 4, 2]  2==2，跳过
            ↑  ↑
            S  F

Step 3: [0, 1, 2, 2, 3, 0, 4, 2]  2==2，跳过
            ↑     ↑
            S     F

Step 4: [0, 1, 3, 2, 3, 0, 4, 2]  3≠2，3覆盖到S位置
               ↑     ↑
               S     F

最终slow=5：有效部分[0,1,3,0,4]
```

#### 代码实现

```python
def removeElement(nums: list[int], val: int) -> int:
    slow = 0  # 慢指针：下一个有效元素存放位置
    for fast in range(len(nums)):  # 快指针遍历
        if nums[fast] != val:      # 发现要保留的元素
            nums[slow] = nums[fast]  # 放到slow位置
            slow += 1              # slow前进
    return slow  # 有效元素的数量
```

#### 复杂度分析

- **时间复杂度**：O(n)，一次遍历
- **空间复杂度**：O(1)

#### 另一种解法：头尾指针法

当 `val` 出现次数很少时，头尾指针法可以减少赋值次数：

```python
def removeElement(nums: list[int], val: int) -> int:
    left, right = 0, len(nums) - 1
    while left <= right:
        if nums[left] == val:
            nums[left] = nums[right]  # 用右指针的值覆盖
            right -= 1               # 右指针左移
        else:
            left += 1                # 保留，左指针右移
    return left
```

**思路**：左指针找要删除的值，找到了就用右指针的值覆盖它，右指针左移。这样每个要删的元素只赋一次值（把最后一个元素搬过来），而不是把所有后面的元素往前移。

#### 面试追问方向

**Q：如果要求保持元素的相对顺序？**
A：快慢指针法天然保持相对顺序，头尾指针法不保持。

**Q：如果数组有序，有没有更优解？**
A：可以使用二分查找定位要删除的区间，但最坏情况依然是O(n)。

---

### 📝 第4题：删除有序数组重复项

**题目**：给你一个**有序**数组 `nums`，请你**原地**删除重复出现的元素，使每个元素**只出现一次**，返回删除后数组的新长度。不要使用额外的数组空间，必须仅使用 O(1) 额外空间并原地修改。

**难度**：⭐ 简单

**示例**：
```
输入：nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
输出：5, nums = [0, 1, 2, 3, 4, _, _, _, _, _]
```

#### 解题思路

还是**快慢指针**！

关键点：因为数组是有序的，所以重复元素一定相邻。我们只需要比较 `nums[fast]` 和 `nums[slow-1]`（即已保留的最后一个元素），如果不同就说明遇到了新元素。

**慢指针 slow**：指向"下一个非重复元素放哪里"
**快指针 fast**：遍历每个元素

条件：`nums[fast] != nums[slow - 1]` 时，代表遇到新元素

#### 手把手推演

```
nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]

初始：slow=1（第0个元素肯定保留，从第1个位置开始放）
     fast=1

fast=1: nums[1]=0 == nums[0]=0 → 重复，跳过
  slow=1, fast=2

fast=2: nums[2]=1 ≠ nums[0]=0 → 新元素！
  nums[1]=1, slow=2, fast=3
  [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]

fast=3: nums[3]=1 == nums[1]=1 → 重复，跳过
  slow=2, fast=4

fast=4: nums[4]=1 == nums[1]=1 → 重复，跳过
  slow=2, fast=5

fast=5: nums[5]=2 ≠ nums[1]=1 → 新元素！
  nums[2]=2, slow=3, fast=6
  [0, 1, 2, 1, 1, 2, 2, 3, 3, 4]

fast=6: nums[6]=2 == nums[2]=2 → 重复，跳过
  slow=3, fast=7

fast=7: nums[7]=3 ≠ nums[2]=2 → 新元素！
  nums[3]=3, slow=4, fast=8
  [0, 1, 2, 3, 1, 2, 2, 3, 3, 4]

fast=8: nums[8]=3 == nums[3]=3 → 重复，跳过
  slow=4, fast=9

fast=9: nums[9]=4 ≠ nums[3]=3 → 新元素！
  nums[4]=4, slow=5, fast=10
  [0, 1, 2, 3, 4, 2, 2, 3, 3, 4]

结束：slow=5，有效数组[0,1,2,3,4]
```

#### 图解

```
初始：[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
       ↑  ↑
      S-1  F     ← 比较nums[0]和nums[1]，都是0→跳过

     [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
        ↑     ↑
       S-1    F     ← nums[1]=1 ≠ nums[0]=0 → 保留

     [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]
           ↑        ↑
          S-1       F     ← nums[3]=1 == nums[1]=1 → 跳过

     ...继续...

最终：[0, 1, 2, 3, 4, 2, 2, 3, 3, 4]
       ↑        ↑
      S-1       F=9  ← 发现4，保留
      
结果：slow=5 → [0,1,2,3,4]
```

#### 代码实现

```python
def removeDuplicates(nums: list[int]) -> int:
    if not nums:
        return 0

    slow = 1  # 第一个元素必定保留，从下标1开始放
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow - 1]:  # 发现新元素
            nums[slow] = nums[fast]
            slow += 1
    return slow
```

#### 复杂度分析

- **时间复杂度**：O(n)，一次遍历
- **空间复杂度**：O(1)

#### 变形题：最多保留两个重复项

如果题目改为"每个元素最多出现两次"，只需要改一个条件：

```python
def removeDuplicates2(nums: list[int]) -> int:
    if len(nums) <= 2:
        return len(nums)

    slow = 2  # 前两个一定保留
    for fast in range(2, len(nums)):
        if nums[fast] != nums[slow - 2]:  # 和往前数第二个比
            nums[slow] = nums[fast]
            slow += 1
    return slow
```

为什么和 `slow - 2` 比？因为如果 `nums[fast] == nums[slow-2]`，说明 `nums[fast]` 和更前面的相同，会导致重复超过2次。

---

### 📝 第5题：多数元素

**题目**：给定一个大小为 `n` 的数组 `nums`，返回其中的**多数元素**。多数元素是指在数组中出现次数**大于** `⌊n/2⌋` 的元素。你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**难度**：⭐ 简单

**示例**：
```
输入：nums = [3, 2, 3]
输出：3

输入：nums = [2, 2, 1, 1, 1, 2, 2]
输出：2
```

#### 解题思路：摩尔投票法（Boyer-Moore Voting）

这是**最优雅的解法**，也是华为OD的高频考点。

**生活例子**：⚔️ 多方混战，不同阵营互相消耗

想象一个战场上有多支军队，每支军队的士兵数量不同。规则是：**不同阵营的士兵两两同归于尽**。

由于多数元素（我们叫它"主角阵营"）的人数超过总数的一半，所以无论怎么消耗，最后站着的肯定是主角阵营的士兵。

**算法步骤**：
1. 选一个**候选**（candidate），初始为 `nums[0]`
2. 维护一个**票数**（count），初始为1
3. 遍历剩余元素：
   - 如果 `count == 0`：重新选候选
   - 如果当前元素 == 候选：`count++`
   - 否则：`count--`

#### 手把手推演

```
nums = [2, 2, 1, 1, 1, 2, 2]

初始：candidate = 2, count = 1
      开始从 i=1 遍历

i=1: nums[1]=2 == candidate → count=2
i=2: nums[2]=1 ≠ candidate → count=1
i=3: nums[3]=1 ≠ candidate → count=0  → 重新选：candidate=1, count=1
i=4: nums[4]=1 == candidate → count=2
i=5: nums[5]=2 ≠ candidate → count=1
i=6: nums[6]=2 ≠ candidate → count=0  → 重新选：candidate=2, count=1

结束：candidate=2
```

**图解票数变化**：

```
元素:    2    2    1    1    1    2    2
count:  1 →  2 →  1 →  0 →  1 →  2 →  1 →  0 →  1
                         ↑                   ↑
                      重新选1              重新选2
cand:   2    2    2    2    1    1    1    1    2
```

最终 `candidate=2` 就是答案。

为什么能这样算？

```
总数 n=7，多数元素出现 ≥4 次
每次count减1代表一对不同阵营的同归于尽
就算所有其他元素都联合起来消灭主角阵营
主角阵营有4个人，其他阵营总共3个人
4 vs 3 → 同归于尽3对后，主角还剩1人
所以答案一定站到最后
```

#### 代码实现

```python
def majorityElement(nums: list[int]) -> int:
    candidate = nums[0]
    count = 1

    for i in range(1, len(nums)):
        if count == 0:           # 当前候选被消耗完了
            candidate = nums[i]  # 换一个候选
            count = 1
        elif nums[i] == candidate:
            count += 1           # 同阵营，票数+1
        else:
            count -= 1           # 不同阵营，同归于尽

    return candidate
```

#### 其他解法

**解法1：哈希表统计**
```python
def majorityElement(nums: list[int]) -> int:
    from collections import Counter
    counts = Counter(nums)
    return max(counts.keys(), key=counts.get)
```
时间复杂度O(n)，空间复杂度O(n)

**解法2：排序取中位数**
```python
def majorityElement(nums: list[int]) -> int:
    nums.sort()
    return nums[len(nums) // 2]  # 多数元素一定在中间位置
```
因为多数元素出现次数超过一半，排序后数组中间位置就是它。
时间复杂度O(n log n)

#### 复杂度分析

- **时间复杂度**：O(n)（摩尔投票法）
- **空间复杂度**：O(1)

#### 面试追问方向

**Q：如果多数元素不一定存在呢？**
A：需要再遍历一次验证候选是否真的出现次数 > n/2。

```python
def majorityElement(nums):
    # 第一步：找到候选
    candidate, count = nums[0], 1
    for num in nums[1:]:
        if count == 0:
            candidate, count = num, 1
        elif num == candidate:
            count += 1
        else:
            count -= 1

    # 第二步：验证
    count = sum(1 for num in nums if num == candidate)
    return candidate if count > len(nums) // 2 else -1
```

**Q：如果要求超过 n/3 的元素呢？**
A：最多有两个候选，用两个变量维护即可（摩尔投票法扩展版）。

```python
def majorityElement(nums):
    # 找超过 n/3 的元素（最多两个）
    cand1 = cand2 = None
    cnt1 = cnt2 = 0

    for num in nums:
        if num == cand1:
            cnt1 += 1
        elif num == cand2:
            cnt2 += 1
        elif cnt1 == 0:
            cand1, cnt1 = num, 1
        elif cnt2 == 0:
            cand2, cnt2 = num, 1
        else:
            cnt1 -= 1
            cnt2 -= 1

    # 验证
    n = len(nums)
    result = []
    for c in [cand1, cand2]:
        if c is not None and sum(1 for x in nums if x == c) > n // 3:
            result.append(c)
    return result
```

---

## 四、性能优化与常见坑 🚨

### 常见坑

**坑1：Python 列表不是数组？**
> Python的 `list` 其实是动态数组（类似于C++的 `vector`），不是传统固定大小的数组。它在底层依然是连续存储的，支持O(1)下标访问，但 `insert(0, x)` 是O(n)的。

**坑2：切片会创建新列表**
```python
nums = [1, 2, 3, 4, 5]
sub = nums[1:3]    # 这是新列表 [2, 3]，不是原列表的视图！
sub[0] = 99
print(nums)        # [1, 2, 3, 4, 5] ← 不变
```
在需要原地操作时注意不要用切片赋值，要用 `nums[:] = ...`

**坑3：遍历数组时删除元素**
```python
# 错误示范
nums = [1, 2, 3, 4, 5]
for x in nums:
    if x % 2 == 0:
        nums.remove(x)  # 边遍历边删除 → 结果不对！
```

### 性能优化技巧

1. **用局部变量减少属性访问**
```python
# 慢：每次循环都要查 len
for i in range(len(nums)):
    ...

# 快：把 len 赋值给局部变量
n = len(nums)
for i in range(n):
    ...
```

2. **避免频繁 insert(0, x)**
   - `insert(0, x)` 是O(n)操作
   - 如果需要在头部频繁插入，用 `collections.deque`

3. **用列表推导式替代循环**
```python
# 循环
result = []
for x in nums:
    if x > 0:
        result.append(x)

# 推导式（更快，更Pythonic）
result = [x for x in nums if x > 0]
```

---

## 五、OD机考实战建议 🎯

### OD机考数组题的命题规律

1. **频率极高**：数组操作题在OD机考中占比约 30-40%，是第一高频题型
2. **难度分布**：简单题约70%，中等题约25%，难题约5%
3. **Day1的五道题**都是OD机考中的"必考题"或"高频题"

### 各题在OD中的定位

| 题号 | 题目 | OD中出现概率 | 建议用时 | 优先级 |
|------|------|————|----------|--------|
| 1 | 交替合并字符串 | ⭐⭐⭐⭐ | 3-5min | ✅ 热身必会 |
| 2 | 合并有序数组 | ⭐⭐⭐⭐⭐ | 5-8min | ✅ 必考变形 |
| 3 | 移除元素 | ⭐⭐⭐⭐⭐ | 3-5min | ✅ 快慢指针基础 |
| 4 | 删除有序数组重复项 | ⭐⭐⭐⭐ | 3-5min | ✅ 高频变形 |
| 5 | 多数元素 | ⭐⭐⭐⭐⭐ | 5-8min | ✅ 摩尔投票常考 |

### OD机考小贴士

1. **优先掌握快慢指针**：OD中70%的数组题都能用快慢指针解决
2. **注意边界条件**：数组为空、长度为1、全删、全留
3. **Python输入技巧**：
```python
# 读取整型数组
nums = list(map(int, input().split()))

# 读取 n 和数组
n = int(input())
nums = list(map(int, input().split()))

# 读取二维数组
matrix = [list(map(int, input().split())) for _ in range(n)]
```
4. **调试大法**：把中间结果打印出来
```python
print(f"slow={slow}, fast={fast}, nums={nums}")  # 放在循环里观察
```
5. **如果卡住了**：先用暴力法拿部分分数（一般能过50-60%测试用例）
6. **时间分配**：前20分钟做简单题，中等题最多30分钟，超过就跳

### 必背代码模板清单

```python
# 1. 快慢指针模板
def template(nums):
    slow = 0
    for fast in range(len(nums)):
        if condition(nums[fast]):
            nums[slow] = nums[fast]
            slow += 1
    return slow

# 2. 头尾指针模板
def template(nums, target):
    left, right = 0, len(nums) - 1
    while left < right:
        if nums[left] + nums[right] == target:
            return [left, right]
        elif nums[left] + nums[right] < target:
            left += 1
        else:
            right -= 1
    return [-1, -1]

# 3. 摩尔投票模板
def majorityElement(nums):
    candidate, count = nums[0], 1
    for num in nums[1:]:
        if count == 0:
            candidate, count = num, 1
        elif num == candidate:
            count += 1
        else:
            count -= 1
    return candidate
```

---

## 六、课后作业 📝

### 基础题（必做）

1. **交替合并字符串**（LeetCode 1768）
   - 用两种方法实现：基础版和一行版
   - 思考：如果两个字符串都很长（10万字符），用 `+=` 拼接字符串会有什么问题？

2. **合并有序数组**（LeetCode 88）
   - 用从后往前合并的方法实现
   - 思考：如果题目要求合并到新的数组而不是原地，代码怎么变？

3. **移除元素**（LeetCode 27）
   - 分别用快慢指针和头尾指针实现
   - 对比两种方法的赋值次数差异

4. **删除有序数组重复项**（LeetCode 26）
   - 实现并做变形：如果每个元素最多保留2次
   - 思考：如果每个元素最多保留k次，怎么写通用函数？

5. **多数元素**（LeetCode 169）
   - 用摩尔投票法实现
   - 用哈希表和排序法实现
   - 变形：找出现次数超过 n/3 的元素（LeetCode 229）

### 进阶题（选做）

- **颜色分类**（LeetCode 75）：荷兰国旗问题，三指针
- **移动零**（LeetCode 283）：快慢指针变形
- **按奇偶排序数组**（LeetCode 905）：头尾指针

### 自我检测

完成作业后，问自己：
- 快慢指针的 slow 指向什么？fast 指向什么？
- 头尾指针什么时候用 left <= right 而不是 left < right？
- 摩尔投票法的核心原理是什么？为什么它有效？
- 从后往前合并解决了什么问题？

---

> **Day1 核心总结**：
> **数组基础 = 下标访问 + 快慢指针 + 头尾指针 + 摩尔投票**
>
> 初学者先攻克 **移除元素**（第3题）和 **交替合并字符串**（第1题），建立信心。
> 然后掌握 **合并有序数组**（从后往前，面试高频）。
> 最后学习 **摩尔投票法**（多数元素，OD必考）。
>
> 记住一天学会3个套路，56天足够通关OD！💪

# 📘 Day 34：Trie字典树 — 从零到精通

> 🎯 **学习目标**：掌握Trie字典树核心知识与算法模板，能独立完成华为OD机考同类题目
> 📅 **华为OD备考 · 第5周 · 第34天** | [返回目录](../README.md)

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

**Trie(前缀树)** — 高效存储和检索字符串集合的树形数据结构.
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False
```
**核心操作**:
- 插入: 沿着路径逐字符创建节点.
- 搜索: 沿着路径逐字符查找.
- 前缀匹配: 判断是否存在以给定前缀开头的单词.
- 通配符搜索: DFS匹配'.'(任意字符).

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

### 1. Implement Trie (Prefix Tree)（⭐⭐）
**来源**：[L75/T150/O](https://leetcode.cn/problems/implement-trie-prefix-tree/)
**难度**：中等
**题目**：**Trie**（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

请你实现 Trie 类：

- `Trie()` 初始化前缀树对象。

- `void insert(String word)` 向前缀树中插入字符串 `word` 。

- `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即，在检索之前已经插入）；否则，返回 `false` 。

- `boolean startsWith(String prefix)` 如果之前已经插入的字符串 `word` 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。

**示例：**
```
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]
解释
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```
**提示：**

- `1 4` 次
**思路**：实现Trie类的insert,search,startsWith三个方法.用字典存储子节点.
**代码**：
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.is_end = True

    def search(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                return False
            node = node.children[c]
        return node.is_end

    def startsWith(self, prefix):
        node = self.root
        for c in prefix:
            if c not in node.children:
                return False
            node = node.children[c]
        return True
```
### 2. Search Suggestions System（⭐⭐）
**来源**：[L75](https://leetcode.cn/problems/search-suggestions-system/)
**难度**：中等
**题目**：给你一个产品数组 `products` 和一个字符串 `searchWord` ，`products`  数组中每个产品都是一个字符串。

请你设计一个推荐系统，在依次输入单词 `searchWord` 的每一个字母后，推荐 `products` 数组中前缀与 `searchWord` 相同的最多三个产品。如果前缀相同的可推荐产品超过三个，请按字典序返回最小的三个。

请你以二维列表的形式，返回在输入 `searchWord` 每个字母后相应的推荐产品的列表。

**示例 1：**
```
输入：products = ["mobile","mouse","moneypot","monitor","mousepad"], searchWord = "mouse"
输出：[
["mobile","moneypot","monitor"],
["mobile","moneypot","monitor"],
["mouse","mousepad"],
["mouse","mousepad"],
["mouse","mousepad"]
]
解释：按字典序排序后的产品列表是 ["mobile","moneypot","monitor","mouse","mousepad"]
输入 m 和 mo，由于所有产品的前缀都相同，所以系统返回字典序最小的三个产品 ["mobile","moneypot","monitor"]
输入 mou， mous 和 mouse 后系统都返回 ["mouse","mousepad"]
```
**示例 2：**
```
输入：products = ["havana"], searchWord = "havana"
输出：[["havana"],["havana"],["havana"],["havana"],["havana"],["havana"]]
```
**示例 3：**
```
输入：products = ["bags","baggage","banner","box","cloths"], searchWord = "bags"
输出：[["baggage","bags","banner"],["baggage","bags","banner"],["baggage","bags"],["bags"]]
```
**示例 4：**
```
输入：products = ["havana"], searchWord = "tatiana"
输出：[[],[],[],[],[],[],[]]
```
**提示：**

- `1
**思路**：对products排序后构建Trie(或直接排序+二分).每个节点存储该前缀下的前3个推荐(按字典序).也可以在Trie节点存推荐列表.
**代码**：
```python
def suggestedProducts(self, products, searchWord):
    products.sort()
    root = {}
    # 构建Trie,每个节点存字典序前三的推荐
    for p in products:
        node = root
        for c in p:
            if c not in node:
                node[c] = {}
            node = node[c]
            if 'suggest' not in node:
                node['suggest'] = []
            if len(node['suggest']) < 3:
                node['suggest'].append(p)

    res = []
    node = root
    for c in searchWord:
        if c in node:
            node = node[c]
            res.append(node.get('suggest', []))
        else:
            # 后续都为空
            node = {}
            res.append([])
    return res
```
### 3. Add and Search Word（⭐⭐）
**来源**：[T150](https://leetcode.cn/problems/design-add-and-search-words-data-structure/)
**难度**：中等
**题目**：请你设计一个数据结构，支持 添加新单词 和 查找字符串是否与任何先前添加的字符串匹配 。

实现词典类 `WordDictionary` ：

- `WordDictionary()` 初始化词典对象

- `void addWord(word)` 将 `word` 添加到数据结构中，之后可以对它进行匹配

- `bool search(word)` 如果数据结构中存在字符串与 `word` 匹配，则返回 `true` ；否则，返回  `false` 。`word` 中可能包含一些 `'.'` ，每个 `.` 都可以表示任何一个字母。

**示例：**
```
输入：
["WordDictionary","addWord","addWord","addWord","search","search","search","search"]
[[],["bad"],["dad"],["mad"],["pad"],["bad"],[".ad"],["b.."]]
输出：
[null,null,null,null,false,true,true,true]
解释：
WordDictionary wordDictionary = new WordDictionary();
wordDictionary.addWord("bad");
wordDictionary.addWord("dad");
wordDictionary.addWord("mad");
wordDictionary.search("pad"); // 返回 False
wordDictionary.search("bad"); // 返回 True
wordDictionary.search(".ad"); // 返回 True
wordDictionary.search("b.."); // 返回 True
```
**提示：**

- `1 4` 次 `addWord` 和 `search` *思路**：支持通配符'.'(匹配任意字符)的Trie.search时遇到'.'需DFS遍历所有子节点.
**代码**：
```python
class WordDictionary:
    def __init__(self):
        self.root = TrieNode()

    def addWord(self, word):
        node = self.root
        for c in word:
            if c not in node.children:
                node.children[c] = TrieNode()
            node = node.children[c]
        node.is_end = True

    def search(self, word):
        def dfs(node, idx):
            if idx == len(word):
                return node.is_end
            c = word[idx]
            if c == '.':
                for child in node.children.values():
                    if dfs(child, idx + 1):
                        return True
                return False
            if c not in node.children:
                return False
            return dfs(node.children[c], idx + 1)

        return dfs(self.root, 0)
```
### 4. Word Search II（⭐⭐⭐）
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
**思路**：Trie + 回溯.先将words构建Trie,然后在board上DFS搜索,同时沿Trie节点移动,匹配到完整单词则加入结果.用'#'标记已访问单元格.
**代码**：
```python
def findWords(self, board, words):
    # 构建Trie
    root = {}
    for w in words:
        node = root
        for c in w:
            if c not in node:
                node[c] = {}
            node = node[c]
        node['#'] = w  # 用'#'存储完整单词

    m, n = len(board), len(board[0])
    res = []

    def dfs(i, j, node):
        if '#' in node:
            res.append(node.pop('#'))  # 去重
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] not in node:
            return
        c, board[i][j] = board[i][j], '*'
        for di, dj in [(1,0),(-1,0),(0,1),(0,-1)]:
            dfs(i+di, j+dj, node[c])
        board[i][j] = c

    for i in range(m):
        for j in range(n):
            if board[i][j] in root:
                dfs(i, j, root)
    return res
```
### 5. Replace Words（⭐⭐）
**来源**：[O](https://leetcode.cn/problems/replace-words/)
**难度**：中等
**题目**：在英语中，我们有一个叫做 **词根**(root) 的概念，可以词根 **后面 **添加其他一些词组成另一个较长的单词——我们称这个词为 **衍生词** (**derivative**)。例如，词根 `help`，跟随着 **继承**词 `"ful"`，可以形成新的单词 `"helpful"`。

现在，给定一个由许多 **词根 **组成的词典 `dictionary` 和一个用空格分隔单词形成的句子 `sentence`。你需要将句子中的所有 **衍生词 **用 **词根 **替换掉。如果 **衍生词 **有许多可以形成它的 **词根**，则用 **最短 **的 **词根** 替换它。

你需要输出替换之后的句子。

**示例 1：**
```
输入：dictionary = ["cat","bat","rat"], sentence = "the cattle was rattled by the battery"
输出："the cat was rat by the bat"
```
**示例 2：**
```
输入：dictionary = ["a","b","c"], sentence = "aadsfasf absbs bbab cadsfafs"
输出："a a b c"
```
**提示：**

- `1 6`

- `sentence` 仅由小写字母和空格组成。

- `sentence` 中单词的总量在范围 `[1, 1000]` 内。

- `sentence` 中每个单词的长度

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

> 💪 **第5周第34天，坚持就是胜利！**

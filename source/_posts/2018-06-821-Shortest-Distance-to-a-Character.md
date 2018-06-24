---
title: 821.Shortest Distance to a Character
description: >-
  给定一个字符串 `S` 和一个字符 `C`，返回一组整数，表示字符串中每个字符到字符 `C`
  的最短距离。解答：分别从左往右和从右往左遍历2次，用`prev`记录上次字符`C`出现的位置，取`min(i-prev,
  prev-i)`，就是我们想要的结果。
categories: LeetCode
tags:
  - LeetCode
  - 字符串
date: 2018-06-24 21:18:41
updated: 2018-06-24 21:18:41
---


[LeetCode链接：821.Shortest Distance to a Character](https://leetcode.com/problems/shortest-distance-to-a-character)
# 题目
给定一个字符串 `S` 和一个字符 `C`，返回一组整数，表示字符串中每个字符到字符 `C` 的最短距离。

# 样例
> Input: S = "loveleetcode", C = 'e'
> Output: [3, 2, 1, 0, 1, 0, 0, 1, 2, 2, 1, 0]

# 注意：
* S 的长度在 [1, 10000] 之间.
* C 是一个单字符, 并且一定在 `S` 中出现.
* 所有的字符都是小写字符.

# 分析
用一个数组`index`记录字符`C`在`S`中出现的位置。
> index = [3, 5, 6, 11]

遍历`i in [0, len-1]`，求出`i`与`index`中任意一个值的『最小差值』.
> 遍历`[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]`，求`i`与`[3, 5, 6, 11]`中任意一个值的『最小差值』。
> 两重遍历的话，时间复杂度为$O(n^2)$.

恰巧的是，数组`index`是有序的，所以只需要用两个角标[left, right]，来分别记录『恰好比`i`大』和『恰好比`i`小』的值的索引。时间复杂度为$O(n)$.

# 代码
Python代码如下：
```python
def shortestToChar(self, S, C):
    """
    :type S: str
    :type C: str
    :rtype: List[int]
    """
    # 字符C出现的index
    index = [idx for idx, c in enumerate(S) if c == C]
    
    # S中任意一个字符分别到左右两边最近的C的index
    # 遍历S的过程中，保证：index[left] <= i < index[right]
    left = right = 0
    res = []
    for i in range(len(S)):  
        # print (i, left, right)
        res.append(min(abs(i - index[left]), abs(i - index[right])))
        
        # 保证：index[right] > i
        if i >= index[right] and right +1 < len(index):
            right += 1
        # 保证：i >= index[left]
        if left + 1 < len(index) and i >= index[left+1]:
            left += 1
    return res
```

# 官方答案
1. 对于每一个索引`i`，我们从左往右遍历一遍，记录上次`C`出现的位置`prev`，`i-prev`就是字符`S[i]`到左边最近的`C`的距离。
2. 同样，再从右往左遍历一遍，`prev-i`就是字符`S[i]`到右边最近的`C`的距离。
3. 取`min(i-prev, prev-i)`，就是我们想要的结果。

```python
def shortestToChar(self, S, C):
    prev = float('-inf')
    ans = []
    for i, x in enumerate(S):
        if x == C: prev = i
        ans.append(i - prev)

    prev = float('inf')
    for i in xrange(len(S) - 1, -1, -1):
        if S[i] == C: prev = i
        ans[i] = min(ans[i], prev - i)

    return ans
```
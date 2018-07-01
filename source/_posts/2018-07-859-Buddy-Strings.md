---
title: 859. Buddy Strings
description: 给定2个数组`A`和`B`，判断是否能通过交换（必须交换一次）任意两个不同位置的字母使`A=B`。
categories: LeetCode
tags:
  - LeetCode
date: 2018-07-01 17:21:03
updated: 2018-07-01 17:21:03
---

[LeetCode: 859. Buddy Strings](https://leetcode.com/problems/buddy-strings)

# 题目
给定2个数组`A`和`B`，如果我们能通过交换（必须交换一次）任意两个不同位置的字母使`A=B`，就返回`true`；否则返回`false`。

# 举例
1. Example 1:
> Input: A = "ab", B = "ba"
> Output: true

2. Example 2:
> Input: A = "ab", B = "ab"
> Output: false

3. Example 3:
> Input: A = "aa", B = "aa"
> Output: true

4. Example 4:
> Input: A = "aaaaaaabc", B = "aaaaaaacb"
> Output: true

5. Example 5:
> Input: A = "", B = "aa"
> Output: false

# 分析
先找出两个数组存在字母不同的各个索引位置。
对于不同字母的个数`num`：
1. num == 2，判断交换之后是否一样；
2. num == 1 or num > 2，则返回false；
3. num == 0，也就是`A`与`B`两个字符串已经是相等的。那么就要判断`A`或`B`中是否存在相同的字母。

# 代码
```python
def buddyStrings(self, A, B):
    """
    :type A: str
    :type B: str
    :rtype: bool
    """
    if len(A) != len(B):
        return False
    index = [idx for idx, (a, b) in enumerate(zip(A, B)) if a != b]
    #print (index)
    if len(index) == 0:
        count = [0] * 26
        for a in A:
            count[ord(a) - ord('a')] += 1
            if count[ord(a) - ord('a')] == 2:
                return True
        return False
    if len(index) == 2:
        return A[index[0]] == B[index[1]] and A[index[1]] == B[index[0]]              
    if len(index) == 1 or len(index) > 2:
        return False
```
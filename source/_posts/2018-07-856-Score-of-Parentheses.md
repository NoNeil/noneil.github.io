---
title: 856. Score of Parentheses
description: 给定一个由『平衡圆括号』组成的数组，根据规则计算字符串的分数。
categories: LeetCode
tags:
  - LeetCode
  - 栈
date: 2018-07-01 17:33:34
updated: 2018-07-01 17:33:34
---


[LeetCode: 856. Score of Parentheses](https://leetcode.com/problems/score-of-parentheses/description/)

# 题目：
给定一个由『平衡圆括号』组成的数组，根据如下规则计算字符串的分数：
1. `()` 的分数为: `1`
2. `AB` 的分数为: `A + B`, `A`和`B`为数字(where A and B are balanced parentheses strings).
3. `(A)` 的分数为: `2 * A`, `A`是数字(where A is a balanced parentheses string).
 
# 举例
1. Example 1:
> Input: "()"
> Output: 1

2. Example 2:
> Input: "(())"
> Output: 2

3. Example 3:
> Input: "()()"
> Output: 2

4. Example 4:
> Input: "(()(()))"
> Output: 6

# 分析
使用『栈』进行计算。因为需要计算的数字都是正数，可以用`0`来代替左括号`(`。
1. 当遇到左括号`(`时（也就是`0`），压栈.
2. 当遇到右括号`)`是，开始计算：
2.1 如果栈顶是左括号`(`，则满足`()`，那么弹出栈顶元素，进栈`1`.
2.2 如果栈顶是正数，则满足`(A)`，那么弹出`2`个元素，进栈`2*A`.
计算完后，判断栈顶是否存在`AB`，如果存在，那么弹出`2`个元素，进栈`A+B`.

# 代码
python代码如下：
```python
def scoreOfParentheses(self, S):
    """
    :type S: str
    :rtype: int
    """
    stack = []
    for s in S:
        if s == '(':    # 当遇到左括号时，往栈内压入一个0，作为标记
            stack.append(0)
        elif s == ')':  # 当遇到右括号时，进行运算
            if stack[-1] == 0: # ()    如果栈顶是左括号
                stack[-1] = 1   # () -> 1                                        
            else:   # (A)   如果栈顶数字
                stack[-2] = stack[-1] * 2   # (A) -> A * 2
                stack = stack[:-1]
            
            # 上面经过运算之后，栈顶新产生了一个数字
            # 如果stack[-2]也是数字，也就是遇到了『AB』，需要将其消掉
            if len(stack) >= 2 and stack[-2] > 0:   # AB
                stack[-2] = stack[-2] + stack[-1]   # AB -> A + B
                stack = stack[ :-1]
            else: # (B  如果stack[-2]不是数字，那么必是左括号，不做任何处理
                pass
        # print (stack)
    return stack[0]
```
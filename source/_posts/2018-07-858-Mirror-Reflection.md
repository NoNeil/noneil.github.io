---
title: 858. Mirror Reflection
description: 镜面反射问题
categories: LeetCode
tags:
  - LeetCode
date: 2018-07-01 17:33:11
updated: 2018-07-01 17:33:11
---


[LeetCode: 858. Mirror Reflection](https://leetcode.com/problems/mirror-reflection)

# 题目

有一个四面墙上都是镜子的正方形房间，除了西南角，其他三个角上都有一个接收激光信号的装置，分别标号`0`,`1`,`2`.

正方形房间的宽为`p`，在西南角有个激光发射器，它首先射向东面墙，距离`0`号激光装置的距离为`q`。

返回：第一次接收到激光信号的装置的编号，(保证激光能遇到其中一个接收装置)

注意：
1. 1 <= p <= 1000
2. 0 <= q <= p
 
# 样例
> Input: p = 2, q = 1
> Output: 2
> Explanation: The ray meets receptor 2 the first time it gets reflected back to the left wall.
> <img src = "reflection.png" width="200px">

# 分析
只有当p是q的整数倍时，激光才命中接收装置；否则在南北两面镜子的作用下继续进行反射。
1. 如果`p`是`q`的**偶数**倍，则命中`2`号装置。
2. 如果`p`是`q`的**奇数**倍，则命中`0`或`1`号装置：
2.1 如果被南北两面镜子反射的次数为**偶数**，命中`1`号装置
2.2 如果被南北两面镜子反射的次数为**奇数**，命中`0`号装置

# 代码
python代码：
```python
def mirrorReflection(self, p, q):
    """
    :type p: int
    :type q: int
    :rtype: int
    """
    folder = 0
    piece = p
    while (True):
        remain = p % q
        times = p / q
        if remain == 0:
            if times % 2 == 0:
                return 2
            else:
                if folder % 2 == 0:
                    return 1
                else:
                    return 0
        p += piece
        folder += 1
```

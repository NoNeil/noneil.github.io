---
title: '李宏毅机器学习2017学习笔记:8.Backpropagation'
date: 2018-04-21 22:29:28
updated: 2018-04-21 22:29:28
description: （1）链式求导法则；（2）向前传播求解$\partial z / \partial w$，向后传播求解$\partial C / \partial z$，两者相乘就是参数w的微分；（3）向后传播可以看做一个反向的神经网络，能从右向左依次求出$\partial C / \partial z$
categories: Machine Learning
tags:
- Machine Learning
- 李宏毅
- BP
---


# 链式求导法则
Case 2: 当改变`s`时，会通过函数`g(s)`和`h(s)`改变`x`和`y`，进而通过`k(x,y)`改变`z`。
<img src="chain_rule.jpg" width="500px">


# 反向传播（Backpropagation）
假设损失函数为$L(\theta)$：
<img src="bp_1.jpg" width="500px">

* 计算**参数w**的偏导：$\partial z / \partial w$，称之为向前传播。
* 计算**激活值z**的偏导：$\partial C / \partial z$，称之为向后传播。

## 向前传播
$z = w*x$，因此$\partial z / \partial w = x$
<img src="bp_2.jpg" width="500px">

## 向后传播
假设：
* 激活函数是sigmoid函数；
* 只有一个隐含层；
* 每一层只有2个神经元。
<img src="bp_3.jpg" width="500px">

那么：
<img src="bp_4.jpg" width="500px">
1. $\partial a / \partial z$，也就是对激活函数求偏导；
2. $\partial z / \partial a = w$，这个很直观；
3. 要求左边的$\partial C / \partial z$，必须先求右边的$\partial C / \partial z'$和$\partial C / \partial z''$。
因此，可以从右往左，依次求解$\partial C / \partial z$。

整理一下，可以得到：
<img src="bp_5.jpg" width="500px">
其中，$\sigma'(z)=\sigma(z)(1-\sigma(z))$可以轻松求解。

可以把『反向传播』想象成另一个**神经元**：
* 输入是：后面的$\partial C / \partial z$
* 激活函数是：乘上一个已知的数$\delta'(z)$，类似『放大器』的功能
<img src="bp_6.jpg" width="500px">

如果是最后一层:直接求解$\partial C / \partial z$
<img src="bp_7.jpg" width="500px">

如果不是最后一层:依次递归求解，直到最后一层。
<img src="bp_8.jpg" width="500px">
『向后传播』时，类似一个反向的神经网络。从右往左计算，计算量跟『向前传播』的计算量一样。
<img src="bp_9.jpg" width="500px">

## 小结
* 在向前传播中，我们求得了$\partial z / \partial w$
* 在向后传播中，我们求得了$\partial C / \partial z$
* 将上述两者相乘，就得到了参数的微分：$\partial C / \partial w$
<img src="bp_10.jpg" width="500px">

# 总结
1. 链式求导法则
2. 向前传播求解$\partial z / \partial w$，向后传播求解$\partial C / \partial z$，两者相乘就是参数`w`的微分
3. 向后传播可以看做一个反向的神经网络，能从右向左依次求出$\partial C / \partial z$
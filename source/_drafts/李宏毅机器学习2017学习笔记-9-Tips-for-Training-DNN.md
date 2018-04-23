---
title: '李宏毅机器学习2017学习笔记:9.Tips for Training DNN'
date: 2018-04-22 20:50:05
updated: 2018-04-22 20:50:05
description:
categories: Machine Learning
tags:
- 李宏毅
- Machine Learning
- DNN
---

# 引言

深度学习的步骤跟机器学习一样，也是分三步。
当训练好一个模型之后：
* 如果在训练数据上表现不好，那么是欠拟合
* 如果在训练数据表现很好，在测试数据上表现不好，那么是过拟合
* 如果在训练数据、测试数据上表现都很好，那么这是个好的模型

<img src="recipe_of_dl.jpg" width="500px">

# 新的激活函数
在手写字符识别中，如果使用sigmoid函数作为激活函数，那么随着网络深度的增加，正确率反而会下降。
<img src="handwriting_digit_classification.jpg" width="500px">

那是因为当网络深度太大时，会存在『梯度消失』的问题。

当固定learnng rate时，后面的参数变化很快，前面的的参数变化很慢。
后面的参数已经收敛时，前面的参数几乎没有变化，还是初始时的随机数。
<img src="vanishing_gradient_problem_0.jpg" width="500px">

因为sigmoid函数是将输入值$(-\infty, +\infty)$映射到$(-1,+1)$区间，相当于把输入值压扁了，那么在串联多个sigmoid函数的情况下，当改变输入时，没通过一个sigmoid函数，输出就会衰减一次，对最终的输出影响就会很小。
<img src="vanishing_gradient_problem.jpg" width="500px">

解决办法：
1. 逐层训练
2. 自适应的learning rate

## ReLU
Rectified Linear Unit, 修正的线性单元
特点：
* `z > 0`时，输出`z`
* `z < 0`时，输出`0`

优势：
1. 计算起来很快
2. 有一定的生物学上原因
3. 无穷多个不同bias的sigmoid函数叠加的结果
4. 能解决『梯度消失』的问题
<img src="relu_1.jpg" width="500px">

有些激活函数的输出为0，另一些激活函数的输出等于输入。
* 去掉输出为0的激活函数，能简化网络结构。
* 剩下的输出等于输入的节点，能避免『梯度消失』的问题
<img src="relu_2.jpg" width="500px">


## Maxout



# 自适应的学习率


# 早点停止训练


# 正则化


# Dropout



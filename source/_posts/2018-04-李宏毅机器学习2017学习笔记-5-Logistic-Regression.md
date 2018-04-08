---
title: '李宏毅机器学习2017学习笔记:5.Logistics Regression'
description: >-
  本文介绍了如何求解Logistic
  Regression，比较交叉熵和平方误差的区别，比较判别式模型与产生式模型的区别，以及各自的优势，最后介绍了解决Logistic回归线性不可分的问题。
categories: Machine Learning
tags:
  - 李宏毅
  - Logistic Regression
  - Neural Network
date: 2018-04-06 22:30:26
updated: 2018-04-06 22:30:26
---


# Logistic 回归 V.S. Linear 回归

## Logistic 回归
* 模型：$f_{w.b}(x) = \delta \left( w \cdot x+b \right)$，输出值的范围是：$(0, 1)$
* 损失函数：$ L(f) = \sum_{j=1}^n C(f(x_j), \hat y^j) $， 其中$ C(f(x_j), \hat y^j) = -\left[ \hat y^j ln f(x^j) + (1-y^j) ln (1-f(x^j))\right]$
* 优化方法：$ w_i = w_i - \eta \sum_{j=1}^n -(\hat y^j - f(x^j)) \cdot x_i $

## Linear 回归
* 模型：$f_{w.b}(x) = w \cdot x+b $，输出值的范围是：$(-\infty, \infty)$
* 损失函数：$ L(f) = \frac 1 2 \sum_i (f(x_i) - \hat y^i)^2 $
* 优化方法：$ w_i = w_i - \eta \sum_{j=1}^n -(\hat y^j - f(x^j)) \cdot x_i $

如下图，Logistic 回归与Linear 回归的区别：
1. 模型不一样：前者是sigmoid函数，后者是线性函数。
2. 损失函数不一样，前者一般采用交叉熵，后者使用平方误差。
3. 参数更新方式的数学表达式的形式上一样。
<img src="logistic vs linear regression.jpg" width="500px">

Logistic 回归的损失函数是两个伯努利的**交叉熵**。
交叉熵代表的是$p(x)$与$q(x)$两个分布有多接近（也就是说模型的输出值与label一致）。
如果两个分布一样的话，交叉熵的值为0.
* 如果$\hat y^i = 1$，且$f(x^i) = 1$，那么$H(p,q)=0$.
* 如果$\hat y^i = 0$，且$f(x^i) = 0$，那么$H(p,q)=0$.
<img src="cross_entropy.jpg" width="500px">

## Logistic 回归的优化方法
记住，$\sigma(z)$对$z$的导数为：
$$ \frac {\partial \sigma(z)} {\partial z} = \sigma(z) (1-\sigma(z))$$

那么，$ln \sigma(z)$对$z$的导数为：
$$ \frac {\partial ln\sigma(z)} {\partial z} = \frac 1 {\sigma(z)} \sigma(z) (1-\sigma(z)) = (1-\sigma(z))$$

那么，$1 - ln \sigma(z)$对$z$的导数为：
$$ \frac {\partial (1-ln\sigma(z))} {\partial z} = - \frac 1 {1 - \sigma(z)} \sigma(z) (1-\sigma(z)) = -\sigma(z)$$

其中，$z=wx+b$，所以$z$对$w$的偏导为$x$：$ \frac {\partial z} {\partial w} = x$

可以很容易的得到$-lnL(w,b)$对$w$的偏导：
<img src="partial_w.jpg" width="500px">
$\hat y$与$f_{w,b}(x)$的差异越大（预测结果与目标越大），梯度越大。

## Logistic 回归 + Square Error
如果使用平方误差作为Logistic回归的损失函数.
损失函数和梯度如下：
<img src="LR+SE.jpg" width="500px">
这个梯度有个问题：当$\hat y = 1$时，无论$f_{w,b}(x)$为1还是0，梯度都为0.
1. 当$f_{w,b}(x)=1$时，$\partial L / \partial w = 0$
2. 当$f_{w,b}(x)=0$时，$\partial L / \partial w = 0$

# 交叉熵 V.S. 平方误差
分别使用交叉熵和平方误差作为损失函数，当参数变化时，损失函数的变化如下图所示：
1. 交叉熵比较陡峭，随着参数变化而变化很大；平方误差则比较平坦。
2. 平方误差的微分总是很小，不好优化。当微分值很小时，可能离目标很远，我们需要调大步长；但是实际上可能离目标很近，应该调小步长。
<img src="LR+SE_2.jpg" width="500px">

# 判别式模型 V.S. 产生式模型
## 两种模型的比较
两种模型都是sigmoid函数，不同的是参数优化方法不同，对数据分布的假设不同。
1. 判别式模型：Logistic 回归
* 直接用梯度下降法，迭代求出$w$和$b$。
* 对数据的分布**没有任何假设**。
2. 产生式模型：概率高斯模型
* 先求出两类数据的均值，和共同的方差，然后求出的$w$和$b$。
* 假设数据的分布服从**高斯分布**、假设服从伯努利分布、假设属性之间不相关。

这两种方法的模型一样，求解参数的方法不一样，求出的$w$和$b$也不一样。因为两种方法对数据分布的假设不一样。
<img src="discriminative_generative_1.jpg" width="500px">
两种方法的正确率也不一样。LR对数据分布假设的依赖很小，它的效果更好。当我们不知道数据的分布情况时，可以试试LR，一般能得到不错的效果。
<img src="discriminative_generative_2.jpg" width="500px">

一般而言，产生式模型的分类效果比产生式模型的更好。
举个例子：
* 类1：有1个样本，x1和x2的取值都为1；
* 类2：有12个样本，其中，4个样本x1和x2的取值分别为1和0，4个样本x1和x2的取值分别为0和1，4个样本x1和x2的取值都为0.
* 问：一个x1和x2的取值都为1的样本为哪类？
<img src="discriminative_generative_3.jpg" width="500px">

求解出来的$P(C_1|x) < 0.5$，属于类2.
<img src="discriminative_generative_4.jpg" width="500px">
因为朴素贝叶斯的假设是：x1和x2是不相关的。

所以,
在类2中，虽然没有x1和x2都为1的样本，但是x1和x2都为1的概率为`1/9`

而在类1中，x1和x2都为1的概率为`1`，远远高于类2中出现x1和x2都为1的概率。

但是，朴素贝叶斯还考虑$P(C_1)$和$P(C_2)$的大小，分别为`1/13`和`12/13`，综合考虑的话，这个样本属于类2的概率比类1的概率还高。

## 有时候，产生式模型的效果更好：
1. 训练数据很少的情况。对训练数据有假设的话，模型能对数据起到补充的效果。
2. 对噪声比较鲁棒。比如上面举的一个例子，类1中的那个样本可能是个噪声。产生式模型能对这个噪声分为类2，但是LR坚持将它分为类1.
3. 先验概率和类条件概率可以通过不同的数据源计算得来。

# 多分类的问题
对于多个类别($K>2$)的情况，我们有
$$ P(C_k|x) = \frac {P(x|C_k)P(C_k)} {\sum_i P(x|C_i) P(C_i)} = \frac {exp(z_k)} {\sum_iexp(z_i)} $$
它被称为归一化指数（normalized exponential），可以被当做logistic sigmoid函数对于多类情况的推广。
其中，$z_k = lnP(x|C_k)P(C_k) $
归一化指数也被称为softmax函数，因为它表示`max`函数的一个平滑版本。
对于所有的$i \neq k$都有$z_k >> z_i$，那么$P(C_k|x) \simeq 1$且$P(C_k|i) \simeq 0$。


# Logistic回归的局限性
LR是线性的，无法解决线性不可分的问题。
<img src="limitation_LR.jpg" width="500px">

## 特征变换
一种解决办法是，进行**特征变换**。变换后的点，能线性可分。但是实际情况下，很难找到一种合适的变换。
> 新的特征第一个维度$x_1^{'}$表示为：点到(0, 0)的距离；
> 新的特征第一个维度$x_2^{'}$表示为：点到(1, 1)的距离；

<img src="limitation_LR_2.jpg" width="500px">

## 级联Logistic 回归 模型
级联2层LR模型，第一个LR的作用是“特征变换”，第二个LR的作用是“分类”。
<img src="cascading_LR_1.jpg" width="500px">
第一层的输出值$(x_1^{'}, x_2^{'})$线性可分。（四个点分别为(0.73,0.05),(0.27,0.27),(0.27,0.27),(0.05,0.73)）.
<img src="cascading_LR_2.jpg" width="500px">

## Deep Learning
级联多层LR模型，得到的就是深度神经网络。前面几层的作用是“特征变换”，最后一层的作用是分类。其中一个LR模型，就是一个“神经元”。
<img src="dl.jpg" width="500px">

# 总结
1. 对比了一下Logistic回归与线性回归的相同点与不同点。不同点是模型函数不同，输出值的范围不同；相同点是参数的梯度的形式一样。
2. 交叉熵衡量的是两个分布的差异，如果两个分布相同，那么他们的交叉熵的值最小，为0。如果Logistic回归用平方误差作为损失函数的话，损失函数随着参数的变化而变化比较缓慢，不好优化，很难得到理想的结果。
3. Logistic回归与线性回归两种模型都是sigmoid函数，不同的是参数优化方法不同，对数据分布的假设不同。
4. 分类问题中，一般判别式模型比产生式模型的效果更好。产生式模型能生成样本。
4. Logistic 回归无法解决线性无可分的问题。可通过“特征变换”和“级联LR"解决线性不可分的问题。

---
title: '李宏毅机器学习2017学习笔记:9.Tips for Training DNN'
description: >-
  介绍了使用DNN时的5个要点，如果在训练数据上表现不好，那么是欠拟合。使用『新的激活函数』、『自适应的学习率』等方法。如果在训练数据表现很好，在测试数据上表现不好，那么是过拟合。使用『早点停止训练』、『正则化』、『Dropout』等方法。
categories: Machine Learning
tags:
  - 李宏毅
  - Machine Learning
  - DNN
date: 2018-04-22 20:50:05
updated: 2018-04-22 20:50:05
---


# 引言

深度学习的步骤跟机器学习一样，也是分三步。
当训练好一个模型之后：
* 如果在训练数据上表现不好，那么是欠拟合。使用『新的激活函数』、『自适应的学习率』等方法。
* 如果在训练数据表现很好，在测试数据上表现不好，那么是过拟合。使用『早点停止训练』、『正则化』、『Dropout』等方法。
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
* 剩下的『输出等于输入』的节点，能避免『梯度消失』的问题
<img src="relu_2.jpg" width="500px">

ReLU是线性的，由ReLU组成的网络是线性的吗？

ReLU的变体
* Leaky ReLU($\alpha = 0.01$)
* Parametric ReLU($\alpha$也是学习的参数)
<img src="relu_variant.jpg" width="500px">

## Maxout

就是以n个节点为一组，每组做**MaxPooling**，选择最大的一个进到下一层，其他的丢弃。
<img src="maxout_1.jpg" width="500px">

ReLU是Maxout的特殊形式。
当每组2个节点，并且其中一个节点的参数都为0，输出$z2=0$时，就是ReLU了。
<img src="maxout_2.jpg" width="500px">
如果其中一个节点的参数不为0，输出$z2 \neq 0$，那么得到了一个变体的ReLU，就得到了一个可学习的激活函数。
<img src="maxout_3.jpg" width="500px">

可学习的激活函数：
* maxout中的激活函数可以是任意的分段线性凸函数
* 多少段取决于每组中节点的个数
<img src="maxout_4.jpg" width="500px">

训练的过程中，随着参数的变化，有时候$z1>z2$，有时候$z1<z2$，那么所有的参数都能学习到。

# 自适应的学习率
每个参数的learning rate都不一样。

## Adagrad
* 用固定的learning rate除以『过去所有梯度平方和的平方根』
* 如果梯度较大，那么learning rate较小；如果梯度较小，那么learning rate较大。
<img src="adagrad.jpg" width="500px">

## RMSProp
训练神经网络的时候，Error的等高线图可能会很复杂，如下图中的月牙形。
我们需要，在同一个方向上，不同的地点的learning rate也不一样。
<img src="RMSProp_1.jpg" width="500px">

需要更加动态地调整learning rate。
RMSProp相对AdaGrad而言，加了个参数，来调整『过去所有梯度』与『当前梯度』的权重。
<img src="RMSProp_2.jpg" width="500px">

> Yann LeCun说，我们没必要太担心『局部最小值』的问题，因为假设一维参数在某个点存在局部最小值的概率为P，对于一个神经网络而言，有成千上万个参数，在高维空间中，必须是所有参数在这个点都取得极小值，那么这个概率就很小了。
> 
> 听起来似乎很有道理。但是从现实生活中来看，现实生活中的地面就有很多坑坑洼洼，每个坑出现的概率又不是很低呀。

## Momentum(惯性)
* 一般的梯度下降法的移动方向：梯度方向的反方向
* 每次移动的方向：上次移动的方向+本次的梯度方向的反方向

如下图，$\lambda$越大，上次移动的方向的权重越大。
第i次的移动方向，考虑了前面i-1次的梯度方向，是i-1次梯度的加权和。
<img src="momentum.jpg" width="500px">

## Adam
RMSPros+Momentum
<img src="adam.jpg" width="500px">

# 早点停止训练
使用`Validation Set`来验证，找出什么时候停止训练。
<img src="early_stopping.jpg" width="500px">

Keras中可以调用`EarlyStopping`，来实现：
```python
from keras.callbacks import EarlyStopping
early_stopping = EarlyStopping(monitor='val_loss', patience=2)
model.fit(x, y, validation_split=0.2, callbacks=[early_stopping])
```
[Keras文档链接](https://keras.io/getting-started/faq/#how-can-i-interrupt-training-when-the-validation-loss-isnt-decreasing-anymore)

# 正则化
正则化就是在损失函数上添加参数的约束项，使参数的值尽可能得趋近于`0`.

> 在神经网络的训练中，正则化的效果往往不是很明显。
> 梯度下降的作用是不断迭代使参数离`0`越来越远，正则化的效果是让参数不要离`0`太远，实际上可能减少迭代次数也能达到同样的效果。

## L2正则
<img src="L2_1.jpg" width="500px">
**正则化会是函数变得平滑，所以用正则化并不会改变函数的bias**
求导，合并同类项。
$\lambda$一般取很小的值，如0.01。那么$\eta \cdot \lambda$的值趋近于0，$(1- \eta \cdot \lambda)$的值趋近于0.99。
也就是说，每次更新参数`w`时，都让它先乘以0.99，经过多次迭代，就越来越趋近于0.有对参数『惩罚』的效果。
再加上后面的微分项，保证参数`w`不会全部为0.
<img src="L2_2.jpg" width="500px">

## L1正则
1. 当 $w>0$ 时，减掉一个正数，`w`往0逼近；
2. 当 $w>0$ 时，减掉一个负数，`w`还是往0逼近；
<img src="L1.jpg" width="500px">

## L1 V.S. L2
<img src="L1vsL2.jpg" width="500px">
1. L2每次更新参数时，都会乘上0.99；L1每次更新参数时，都会加上/减掉一个固定的值。
2. 假设参数为10000，使用两种正则化方法。使用L2时，乘上0.99，那么一次性减掉了100；使用L1时，减掉一个固定的值0.99。也就是说，L2更能避免参数过大的情况。
3. 假设参数为1，使用两种正则化方法。使用L2时，乘上0.99，那么一次性减掉了0.01；使用L1时，减掉一个固定的值0.99。也就是说，L1更能使参数为0.
4. 所以，使用L2时，能使参数不至于过大，使大部分参数都接近0，但等于0的个数不会太多；使用L1时，会出现比较多的参数比较大的情况，也会出现比较多的参数为0的情况。

## 生物学解释
生物学上，刚出生的人脑神经元的链接不较少；6岁的时候出现大量的神经元链接；14岁的时候链接变少，就像『稀疏参数』一样。
<img src="brain.jpg" width="500px">

# Dropout
只对『输入层』和『隐含层』进行Dropout操作。

## 操作方法

* 训练阶段：对于每个mini-batch，更新参数时，先对所有神经元进行采样，每个神经元以`p`的概率被丢弃掉。

* 测试阶段：使用所有的参数，但是每个参数都要乘以`1-p`。
因为训练时，只保留了`1-p`部分的神经元；但是测试时，使用的是所有神经元，所以直观地需要将参数乘以`1-p`（如果激活函数是线性函数）。

就像训练的时候，只用一部分神经元来训练，得到的是多个比较弱的分类器。测试的时候，用所有神经元，就能得到更好的效果。
<img src="dropout_1.jpg" width="500px">

## Dropout是一种ensamble方法

Ensemble方法（Bagging）：
从一个很大的训练集中采样出多个子集（一部分样本），分别训练出多个模型。
<img src="dropout_2.jpg" width="500px">

如果每个模型都足够复杂，那么这些模型的bias很小，variance很大。多个模型平均之后，variance就很小了。

Dropout方法是：
* 用每个mini-batch去训练一个神经网络；
* 有些参数在网络之间是共享的；
<img src="dropout_3.jpg" width="500px">

当参数个数为`M`是，可能存在 $2^M$ 个。如果穷举 $2^M$ 种情况，计算量太大了。

在测试阶段：
* 『Ensemble』是直接对多个模型的输出求平均
* 『Dropout』是将参数乘以`1-p`

如果激活函数是线性函数的话，这种将参数乘以`1-p`的方法是完全可行的。如下图：
<img src="dropout_4.jpg" width="500px">

神奇的是，即使激活函数不是线性的，也是work的。

所以，对于激活函数比较接近**线性**的情况，如ReLU、MaxOut，使用Dropout方法时能得到更好从效果。

也就是说，如果你已经决定了使用了Dropout，那么在选择激活函数时，尽量选择比较接近**线性**的，如ReLU、MaxOut，而不是sigmoid。

# 总结

1. 如果在训练数据上表现不好，那么是欠拟合。使用『新的激活函数』、『自适应的学习率』等方法。如果在训练数据表现很好，在测试数据上表现不好，那么是过拟合。使用『早点停止训练』、『正则化』、『Dropout』等方法。
2. 新的激活函数：ReLU，Maxout
3. 自适应的学习率：Adagram、RMSProp、Momentum、Adam
4. 早点停止训练：交叉验证
5. 正则化：L1能使更多的参数为0，L2能使更多的参数接近0
6. Dropout：一种Enseble方法，训练时参数要乘以`1-p`
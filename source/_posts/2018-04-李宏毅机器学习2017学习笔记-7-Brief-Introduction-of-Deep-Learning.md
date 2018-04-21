---
title: '李宏毅机器学习2017学习笔记:7.Brief Introduction of Deep Learning'
description: 深度学习的3个步骤，与机器学习的三个步骤一样。以全连接的前馈神经网络为例阐述着三个步骤：建模（定义网络结构）、定义损失函数、求解最优参数。
categories: Machine Learning
tags:
  - Machine Learning
  - 李宏毅
  - Deep Learning
date: 2018-04-21 11:42:31
updated: 2018-04-21 11:42:31
---



# 深度学习的发展史
<img src = 'history_of_dl.jpg' width="500px">

# 深度学习方法

## Deep Learning 的三个步骤跟机器学习的三个步骤一样：
1. 定义一个模型
2. 评价模型的好坏（损失函数）
3. 求解模型的最优参数
<img src = 'three_steps_for_dl.jpg' width="500px">
备注：function set是指一个模型；一个function是指参数已经确定的模型，给定一个输入，就会有个输出。

## 定义一个模型
最简单的深度学习模型就是『全连接前馈网络』，由一个输入层、多个隐含层、一个输出层组成。
<img src = 'fully_connect_feedforward_network_2.jpg' width="500px">

各种DL模型的层数对比：
* 2012，AlexNet，8层，16.4%的错误率；
* 2014，VGG，19层，7.3%的错误率；
* 2014，GoogleNet，6.7%的错误率；
* 2015，Residual Net，3.57%的错误率。

我们可以把中间隐含层部分看作一个特征提取的模块。
最后一个输出层用Softmax。
<img src = 'fully_connect_feedforward_network_3.jpg' width="500px">

举个例子：手写数字识别
需要设计每层的神经元的个数。
<img src = 'digit_recognition.jpg' width="500px">

FAQ：
1. Q:选择多少层？每层多少个神经元？
A: 根据训练处的误差，凭直觉进行调整
2. Q:网络结构能自动设置吗？
A: 能，例如：Evolutionary Artificial Neural Network
3. Q: 能否设计其他的网络结构？
A: 能，例如：CNN

## 评价模型的好坏（损失函数）

『手写字符识别』的例子中，用Cross Entropy作损失函数。
<img src = 'loss_for_the_example.jpg' width="500px">

## 求解模型的最优参数
使用Gradient Descent求解最优的参数。

有很多深度学习框架来帮你计算微分：TensorFlow、torch、theano、Caffe、Microsoft CNTK、chainer、DSSTNE、mxnet、libdnn

# 为什么要用深度学习？
1. 有些实验表明，网络越深，效果越好。
<img src = 'deep_is_better.jpg' width="500px">

2. 理论证明，一个隐含层的网络能表示任意函数。理论上，不一定要『深』，也可以『宽』。
<img src = 'universality_theorem.jpg' width="500px">

总结：
1. 介绍了深度学习的发展史
2. 深度学习的3个步骤，与机器学习的三个步骤一样。以全连接的前馈神经网络为例阐述着三个步骤：建模（定义网络结构）、定义损失函数、求解最优参数。


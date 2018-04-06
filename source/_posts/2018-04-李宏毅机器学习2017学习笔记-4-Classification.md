---
title: 李宏毅机器学习2017学习笔记-4.Classification
description: 生成模型、贝叶斯分类器、推导出Logistics Regression模型
categories: Machine Learning
tags:
  - 李宏毅
  - 高斯分布
  - 贝叶斯
  - Logistics Regression
date: 2018-04-04 10:18:33
updated: 2018-04-04 10:18:33
---


# 引言

如何做分类呢？一个理想的方法是：
* 函数（模型）：找到一个函数$f(x)$，其中内建一个函数$g(x)$，如果$g(x)>0$则为类`1`，否则为类`0`；
* 损失函数：$L(f) = \sum_n \delta(f(x^n) \neq \hat y^n)$，分类正确的个数。
* 优化方法：感知机、SVM等


# 生成模型

## 举个例子
有2个盒子，盒子1中有4个蓝1球、1个绿球，盒子2中有2个蓝球、3个绿球。选择盒子1的概率为2/3，选择盒子2的概率为1/3。已知抽到了一个蓝球，问这个蓝球是从哪个盒子里抽出来的。
<img src="two_boxes.png" width="500px">

> 贝叶斯公式：$$P(A|B) = \frac {P(B|A) * P(A)} {P(B)}$$

我们可以利用贝叶斯公式，分别算出$P(B_1|Blue)$ 和 $P(B_2|Blue)$的大小，哪个大，蓝球就来自哪个盒子。
<img src="two_boxes_1.jpg" width="500px">

> $ P(Blue|B_1)P(B_1) = 4/5 * 2/3 = 8 / 15 $
>
> $ P(Blue|B_2)P(B_2) = 2/5 * 1/3 = 2 / 15 $
>
> $ P(B_1|Blue) = 8/15 / (8/15+2/15) = 4 / 5$

更加普适一点：我们设一个样本来自于类`i`的概率为$P(C_i)$，在类`i`中，抽中某个样本的概率为$P(x|C_i)$.
<img src="two_boxes_2.jpg" width="500px">
给定`x`，它来自哪一类的概率为：
$$ P(C_i|x) = \frac {P(x|C_i)P(C_i)} {\sum_{i=1}^n P(x|C_i) P(C_i)}$$
那么要求`x`来自哪一类，就看哪个$P(C_i|x)$最大。

**生成模型**：我们从训练数据中能得到上图4个红框框中的表达式，用这4个东西就能求出`x`出现的几率，就能生成`x`:
$$ P(x) = P(x|C_1)P(C_1) + P(x|C_2)P(C_2) $$ 


## 求解一下宝可梦属于水系还是一般系
训练数据中有79只『水系』的和61只『一般系』宝可梦，其中$ P(C_1) $ 和 $ P(C_2)$很好求出：
$ P(C_1) = 79 / (79 + 61) = 0.56 $
$ P(C_2) = 61 / (79 + 61) = 0.44 $

但是，**如何求$ P(x|C_1) $ 呢？**

我们选择『防御力』和『特殊防御力』两个属性来描述宝可梦，那么特征的维度为2维。

把79只『水系』的宝可梦画在坐标系中，如下图所示：
<img src="feature.jpg" width="500px">
如上图红色圆圈所示，假设宝可梦的特征值服从高斯分布。求出均值和方差，就能根据$f_{\mu,\Sigma}(x)$ 求出 $P(x|C_i)$。
<img src="gaussian_distribution.jpg" width="500px">

使用**最大似然估计**，求$\mu$和$\Sigma$呢？
从一个均值为$\mu$、方差为$\Sigma$的高斯分布中取79个样本的概率为：
$$ L(\mu, \Sigma) = f_{\mu,\Sigma}(x^1) \cdot f_{\mu,\Sigma}(x^2) \cdot f_{\mu,\Sigma}(x^3)...f_{\mu,\Sigma}(x^{79})$$

使似然函数取得最大值的解，就是最优解：$ \mu^\ast, \Sigma^\ast = arg \max_{\mu, \Sigma} L(\mu, \Sigma) $
最优解就是训练数据的均值和方差：
$\mu^\ast = \frac 1 {79} \sum_{n=1}^{79} x^n $
$\Sigma^\ast = \frac 1 {79} \sum_{n=1}^{79} (x^n - \mu\ast)(x^n - \mu^\ast)^T $

『水系』和『一般系』两类样本的分布如下：
<img src="gaussian_distribution_2.jpg" width="500px">

根据$P(x|C_1) = f_{\mu^1, \Sigma^1}(x)$，求出$P(C_1|x)$:
<img src="gaussian_distribution_3.jpg" width="500px">

如果$P(C_1|x) > 0.5 $，那么`x`属于第一类『水系』。这样，在测试集上的正确率为47%。
如下图，蓝色区域的点会被分为『水系』，红色区域的点会被分为『一般系』。
<img src="gaussian_distribution_4.jpg" width="500px">

改进方法：
* 增加特征，将全部7种属性都作为宝可梦的特征，正确率为54%。结果还是不理想。
* 假设『水系』和『一般系』两类样本的 $\Sigma$ 一样，$\Sigma = (79/140)\Sigma^1 + (61/140)\Sigma^2 $，正确率提升到73%。

比较：
* 两类不共用同一个$\Sigma$的话，分界面是曲线的。
* 两类共用同一个$\Sigma$的话，分界面变成了**线性**的。（这里可引出Logistics Regression）
<img src="gaussian_distribution_5.jpg" width="500px">

## 解决分类问题需要3步
使用概率生成模型进行分类，共3步：
1. 建模；
2. 用似然函数定义参数的好坏；
3. 用极大似然估计来计算最优参数。
<img src="three_steps.jpg" width="500px">

# 朴素贝叶斯
假设产生x的每一维特征都是不相关的，那么$P(x|C_1) = P(x_1|C_1) \cdot P(x_2|C_1) ... P(x_k|C_1)$. (实际上，特征的各个维度之间是相关的。比如『防御力』比较大的，一般『攻击力』比较小)。

不是所有的情况都使用高斯分布，**如果某个特征是二值的，用伯努利分布比较好！**

# 后验概率
令$ z = ln \frac {P(x|C_1) P(C_1)} {P(x|C_2) P(C_2)}$，
用$exp(-z)$ 表示 $\frac {P(x|C_1) P(C_1)} {P(x|C_2) P(C_2)}$，因为$p/(1-p)$和epx(-z)的取值范围都是$0 \to \infty$.
<img src="posterior_prob.jpg" width="500px">

## 求解z

过程如下：
<img src="z.png" width="500px">

## `z`与`x`线性相关
得到`z`与`x`是线性相关的：$z=w^T \cdot x + b$
进而得到：$P(C_1|x) = \delta(w \cdot x + b)$，其中$\delta$是sigmoid函数。
也就是说，**Logistics Regression方法的假设可以是各个类的数据分布为高斯分布**。
<img src="z_2.jpg" width="500px">

总结：
1. 通过一个从两个盒子中取篮球和绿球的例子，引出生成模型。
2. 生成模型能求出$P(x)$，求$P(x)$依赖于求解各个类的$P(C_i)和$P(x|C_i)$。
3. 朴素贝叶斯分类器的假设是，`x`的特征的各个维度是不相关的。
4. 后验概率能表示为一个sigmoid函数：$P(C_i|x)=\delta(z)$，在数据服从高斯分布的前提假设下，这个`z`又与`x`线性相关。所以，$P(C_i|x) = \delta(w \cdot x + b)$
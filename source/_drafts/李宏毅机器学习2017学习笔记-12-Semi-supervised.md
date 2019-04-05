---
title: '李宏毅机器学习2017学习笔记:12.Semi-supervised'
date: 2018-07-16 23:31:42
updated: 2018-07-16 23:31:42
description:
categories:
tags:
- 李宏毅
- Machine Learning
- DL
- 半监督学习
---

半监督学习就是unlabeled 的样本数远大于 labeled 的样本数。

做半监督学习的动机有两个：一个是由于获取labeled 的样本比较困难；另一个是由于我们在日常生活中就一直在做半监督学习。

下面这个例子解释了unlabeled 的数据分布可能给我们一些信息。
(1) 蓝色和橙色两个点为 labeled 样本，如果只看这两个样本，我们会认为这条红色的线可能是分界面。
<img src="why_semi-supervised_1.jpg" width="350px">
(2) 如果加上灰色的这些 unlabeled 样本，分界面更可能是这条斜着的线。
<img src="why_semi-supervised_2.jpg" width="350px">

# 在产生式模型中使用半监督学习


# (Low-density Separation Assumption)


# Smoothness Assumption



# Better Representation


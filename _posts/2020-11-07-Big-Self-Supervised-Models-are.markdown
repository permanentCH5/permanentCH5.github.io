---
title: "Big self-supervised models are strong semi-supervised learners."
layout: post
date: 2020-11-07 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---
# Big self-supervised models are strong semi-supervised learners. 

[ARXIV2020] Chen, T., Kornblith, S., Swersky, K., Norouzi, M., & Hinton, G. (Google Research, Brain Team). Big self-supervised models are strong semi-supervised learners. *arXiv preprint arXiv:2006.10029*. 

## 简介
这篇文章介绍的大型的自监督模型是强的半监督学习器，介绍的是一些自监督研究中的发现。  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107233051.png)
图1 ImageNet的Top1准确率的进步随模型大小的变化  


## 相关工作
无标签数据，如何设计特殊任务（56-58）
Olivier Chapelle, Bernhard Scholkopf, and Alexander Zien. Semi-supervised learning. MIT Press, 2006.  
Xiaojin Zhu and Andrew B Goldberg. Introduction to semi-supervised learning. Synthesis lectures on artificial intelligence and machine learning, 3(1):1–130, 2009.  
Avital Oliver, Augustus Odena, Colin A Raffel, Ekin Dogus Cubuk, and Ian Goodfellow. Realistic evaluation of deep semi-supervised learning algorithms. In Advances in Neural Information Processing Systems, pages 3235–3246, 2018..  

## 方法
本文介绍的半监督学习方法示意图如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107233609.png)  
图2 方法示意图  
包括无监督的预训练，以及自训练/知识蒸馏等。

## 实验和结果
实验结果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107234113.png)
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107234215.png)
可见，用于自监督训练的网络参数量越大越好
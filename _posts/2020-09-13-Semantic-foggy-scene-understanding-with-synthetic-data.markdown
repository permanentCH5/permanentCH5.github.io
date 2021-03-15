---
title: "Semantic foggy scene understanding with synthetic data"
layout: post
date: 2020-09-13 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---

# Semantic foggy scene understanding with synthetic data. 

[IJCV2018]Sakaridis, C., Dai, D., & Van Gool, L. (ETH Zurich, Zurich, Switzerland and KU Leuven, Leuven, Belgium). Semantic foggy scene understanding with synthetic data. International Journal of Computer Vision, 126(9), 973-992.

## Introduction
本篇文章是讲怎么去生成含有雾的影像，以及相关的场景理解。大体的示意图如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200908112730.png)
图1 示意图。包括雾的仿真，含有雾影像的训练，含有雾的场景理解。

对雾的仿真是遵循了标准的光学模型（白天），这种方法已经在去雾领域里广泛使用。总的来说，本篇文章贡献有以下几点：

1. 自动地在晴朗天气下的图像生成带有雾的影像。
2. 两个新的数据集，一个是合成的，另一个是真实的，用于含有雾的影像的场景理解(Semantic Foggy Scene Understanding, SFSU)。
3. 一个新的半监督学习的方法用于含有雾的场景理解。
4. 含有雾的场景理解和人类认知对含有雾的影像。

## 在真实的户外场景中对雾的生成进行模拟
雾的生成需要遵循一定的模型。这里，首先介绍光学模型，然后介绍生成的流程，最后介绍一些选择高质量仿真图的一些标准。
### 雾生成的光学模型
这个光学模型在去雾文章中用得较多，其核心如下：
$$
I(x) = R(x)t(x)+L(1-t(x))
$$
其中$I(x)$是含有雾的影像的像素$x$，$R(x)$是没有雾的图，$L$是光照（白天有效）；$t(x)$是透射的系数，表示环境中的反射光线到达相机的量。这里，$t(x)$可以由深度$l(x)$计算，
$$
t(x) = exp(-\beta l(x))
$$
其中$\beta$是透射系数，遵循如下规律：
$$
\beta \ge 2.996 \times m^{-1}
$$
有分析指出，这个模型算是一个相当强的模型，所以只要能得到光照图$L$以及透射率图$t$，则生成的含有雾的影像是比较好的。
总之任务分为两个部分，一个是对$t$的估计，另一个是对$L$的估计。其中对$L$的估计已经有文献(a,b)做得较好了，这篇文章直接引用。文章重点在于对透射图$t$的估计。

(a)He, K., Sun, J., & Tang, X. (2010). Single image haze removal using dark channel prior. IEEE transactions on pattern analysis and machine intelligence, 33(12), 2341-2353.
(b)Tang, K., Yang, J., & Wang, J. (2014). Investigating haze-relevant features in a learning framework for image dehazing. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 2995-3000).

### 生成流程
这里首先整理生成准确的透射图$t$的输入为：
- 晴朗天气下的图像$R$，这个图像是相互标定的图像对中左边的图像。
- 对应的右边的图像。
- 相机的标定系数
- 密集的视差图对于图像$R$（具备相同的分辨率）
- 当$D$中的像素值缺失时，用作平衡的集合$M$

所以，这里需要借助相机标定的一些知识。借助文献格式(c),可以得到上述的要求。
(c)Hirschmuller, H. (2007). Stereo processing by semiglobal matching and mutual information. IEEE Transactions on pattern analysis and machine intelligence, 30(2), 328-341.

所以流程主要如下所述：
1.计算深度图$d$(米级)。
2.对深度图$d$进行去噪，得到细化的深度图$d'$。
3.由细化的深度图$d'$计算场景距离图$l$。
4.由$t(x) = exp(-\beta l(x))$计算透射图。
5.采用引导滤波对深度图$t$作一个引导，引导图为晴朗的含有色彩的原图$R$。



### 标准
有可能生成的图像存在一定的问题，一个问题就是在于图像本身可能存在高对比度，如下图所示：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920163555.png)
所以这里需要进行一次筛选。
另外还有可能成像的位置是天空，那么这里生成雾的模型$I(x) = R(x)t(x)+L(1-t(x))$就不对了，所以还需要筛查像素的标签是不是天空。

## 生成结果
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200913224937.png)
图2 不同$\beta$下的含有雾的影像（Foggy Cityscapes），可见程度不同
有了这些含有雾的影像后，可以进行去雾的研究以及通过数据扩充和微调，增强含有雾的场景分割的研究。
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200913225209.png)
图3 含雾影像的去雾算法对比
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200913225334.png)
图4 含雾影像场景分割研究，最后一列为在[44]的基础上，通过数据扩充并微调后的结果


---
title: "Dual Adversarial Network for Deep Active Learning."
layout: post
date: 2020-09-20 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---

# Dual Adversarial Network for Deep Active Learning. 

[ECCV2020]Wang, S., Li, Y., Ma, K., Ma, R., Guan, H., & Zheng, Y.(School of Electronic Information and Electrical Engineering, Shanghai Jiao Tong University, Shanghai 200240, China and Tencent Jarvis Lab, Shenzhen, China) Dual Adversarial Network for Deep Active Learning.

## 简介
当数据集较小的时候，如何更好地利用数据找到最适合网络的样本进行学习呢？这篇文章介绍的是双路对抗网络用在深度主动学习上（DAAL）。
其思路示意图如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920172041.png)
图1 思路示意图
其中，在主动学习框架中（a），图像选择器用来挑选和已经有标签的图像有最大距离（不确定性）的图像，同时使未作标记的图像之间的距离最小；在对抗框架中（b），对抗网络用来帮助图像选择器去衡量深度特征之间的距离，包括两个方面，一个是图像是否已经有了图像，另一个是是否”初始“还是”重建“。

## 相关工作
这里记录一下主动学习用在语义分割的示例。（39,24,36,34）
(a)Yang, L., Zhang, Y., Chen, J., Zhang, S., & Chen, D. Z. (2017, September). Suggestive annotation: A deep active learning framework for biomedical image segmentation. In International conference on medical image computing and computer-assisted intervention (pp. 399-407). Springer, Cham.
(b)Mackowiak, R., Lenz, P., Ghori, O., Diego, F., Lange, O., & Rother, C. (2018). Cereals-cost-effective region-based active learning for semantic segmentation. arXiv preprint arXiv:1810.09726.
(c)Sinha, S., Ebrahimi, S., & Darrell, T. (2019). Variational adversarial active learning. In Proceedings of the IEEE International Conference on Computer Vision (pp. 5972-5981).
(d)Siddiqui, Y., Valentin, J., & Nießner, M. (2020). ViewAL: Active Learning with Viewpoint Entropy for Semantic Segmentation. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (pp. 9433-9443).

## 方法
网络架构示意图如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920174252.png)
图2 网络架构示意图

首先无论是含有标签的集合，还是没有标签的集合，均需要提取特征，得到特征后，需要结合信息评价器进行评估，并经由双路的GAN网络进行样本的归类。

整个网络有三个部分需要注意，包括信息评价器(Informativeness rater)，双路判别器(Dual discriminators)，以及多玩家竞争的训练策略(Training strategy: multi-player competition)

- 信息评价器
给定在没有标签的池子中的深度特征$X_U = \{x_u : u = 1,...,N\}$。深度特征$X_U$是由任务模型决定。同时，我们设计一个ScoreNet给一个重要性分数，记为$s = \{s_u: s_u \in [0,1], u=1,...,N\}$。在网络中，存在两个分支，不确定分支(uncertainty branch)和表达性分支(representativeness branch)。 对于深度特征$x_u$，分别采用权重$1-s_u$ 和 $s_u$对应两个分支。加权的特征送到一个变分自编码器。编码器将这些特征生成再隐式空间的特征$e=\{\hat{e},\tilde{e}\}$，同时解码器再将这些特征解析回去$x_{recon} = \{\hat{x}_u, \tilde{x}_u \}$。 ScoreNet 的结构较为简单，为一个5层的MLP网络。在自编码器中，编码器和解码器采用7层的MLP。使用了Dropout层，比率为0.4，以避免过拟合。

- 双路判别器
采用了双路的判别器，分别是不确定度判别器(uncertainty discriminator uDis)和表示判别器（representativeness discriminator, rDis）。双路判别器的输入分别是深度特征$x_l$和$\hat{x}_u$，以及深度特征$x_u$和$\tilde{x}_u$。uDis用于分辨特征是有标签还是无标签，rDis用于区分特征是初始特征还是重建后的特征。双路的判别器采用和ScoreNet相同的结构。如果判别器不能够区分特征是重建特征还是原始的特征，那么说明图像的特征在没有标签的池子中有了比较好的特征表达能力。


- 训练策略：多玩家竞争
在这样的训练模式下，一个较大的重要性分数$s_u$表示图像同时具有较大的不确定度和表示性。这是因为如果$\hat{x}_u$重建得比较成功，uDis判别不出来是有标签的特征还是无标签的特征，那么$1-s_u$会较小。当然，这也意味着$s_u$较大，rDis也会认为$\tilde{x}_u$的表示性较大。在样本的选择上，具有较大的分数前k个样本被选中用于标记和训练。

训练算法示意：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920181040.png)
训练过程中有4部分损失函数，包括先验损失$L_{prior}$（用于变分自编码器），重建损失$L_{recon}$（用于变分自编码器），对抗网络损失$L_{GAN}$以及稀疏损失$L_{sparity}$（用于评分网络）。列举如下：
$$
L_{prior} = D_{KL}(q(e|x)||p(e))
$$
$$
L_{recon} = E[-logp(\phi(x_u)|\hat{e})]+E[-logp(\varphi(x_u)|\tilde{e})]
$$
$$
L_{GAN} = log(uDis(x_l)) + log(1-uDis(\hat{x}_u)) + log(rDis(x_u))+log(1-rDis(\tilde{x}_u))
$$
$$
L_{sparsity} = L_{LR} + L_{DPP}
$$
$$
L_{LR} = ||\sigma - \frac{1}{n}\sum^{n}_{t=1}s_t||_{2}
$$
$$
L_{DPP} = -log(P(s_{x'}))
$$

## 实验和结果
实验的数据集包括两个方面，一个是用于图像分类的CIFAR10和CIFAR100，另一个是用于图像分割的Cityscapes。通过各种方法的比较以及缺陷学习，可以看到该方法有一定的效果。初始样本为随机选择的训练集10%,然后每一次加入样本均加入5%。
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920175916.png)
图3 和其它主动学习的方法比较，U.B.是采用了整个数据集进行训练
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920180842.png)
图4 Ablation Study，uDis和rDis表示只用其中一路判别器的效果
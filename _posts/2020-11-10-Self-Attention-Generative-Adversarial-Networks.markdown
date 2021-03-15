---
title: "Self-Attention Generative Adversarial Networks."
layout: post
date: 2020-11-10 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---
# Self-Attention Generative Adversarial Networks. 

[ICML2019] Zhang, H., Goodfellow, I., Metaxas, D., & Odena, A. (Department of Computer Science, Rutgers University, Google Research, Brain Team, Work done while at Google Research.). Self-attention generative adversarial networks. In International Conference on Machine Learning (pp. 7354-7363). PMLR..  

## 简介
这篇文章介绍的基于自注意力的生成对抗网络SAGAN用于图像生成。该方法使得影像的判断更加显得有逻辑，如下图所示。  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107224210.png)  
图1 SAGAN图像生成示意与注意力示意  


## 相关工作
注意力模型
Bahdanau, D., Cho, K., & Bengio, Y. (2014). Neural machine translation by jointly learning to align and translate. arXiv preprint arXiv:1409.0473.  
Chen, X., Mishra, N., Rohaninejad, M., & Abbeel, P. (2018, July). Pixelsnail: An improved autoregressive generative model. In International Conference on Machine Learning (pp. 864-872). PMLR.  
Xu, T., Zhang, P., Huang, Q., Zhang, H., Gan, Z., Huang, X., & He, X. (2018). Attngan: Fine-grained text to image generation with attentional generative adversarial networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1316-1324).  
Parmar, N., Vaswani, A., Uszkoreit, J., Kaiser, Ł., Shazeer, N., Ku, A., & Tran, D. (2018). Image transformer. arXiv preprint arXiv:1802.05751.  

## 方法
本文介绍的注意力结构示意图如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107224345.png)  
图2 注意力结构示意图  
对于图像特征$x \in R^{C \times N}$，首先转换到两个特征空间$f(x)=W_fx$，$g(x)=W_gx$，并可以得到一个得分$\beta$，用以评价模型倾向于用第$i$个区域去生成第$j$个区域，定义如下：
$$
\beta_{j,i} = \frac{exp(s_{ij})}{\sum_{i=1}^{N}exp(s_{ij})}, s_{ij}=f(x_i)^Tg(x_j)
$$
再设计注意力层的输出$o = (o_1,o_2,...o_j,...o_N) \in R^{C\times N}$，定义如下：
$$
o_j = v(\sum_{i=1}^N\beta_{j,i}h(x_i)), h(x_i)=W_hx_i, v(x_i)=W_vx_i
$$
最后的输出定义为$y$，设计如下：
$$
y_i = \gamma o_i + x_i
$$
其中$\gamma$为可学习的参数。
SAGAN网络的训练Loss为：
$$
L_D = -E_{(x,y)\backsim p_{data}} [min(0,-1+D(x,y))] - E_{(z\backsim p_x, y\backsim p_{data})}[min(0,-1-D(G(z),y))]
$$
$$
L_G = -E_{x\backsim p_x,y\backsim p_{data} }D(G(z),y)
$$
## 实验和结果
采用的数据集为LSVRC2012（ImageNet）。评价指标为Inception score(IS)(a)和Frechet Inception distance(FID)(b)。前者越高效果越好，而后者越低效果越好。
(a)Reed, S. E., Akata, Z., Mohan, S., Tenka, S., Schiele, B., & Lee, H. (2016). Learning what and where to draw. In Advances in neural information processing systems (pp. 217-225).
(b)Heusel, M., Ramsauer, H., Unterthiner, T., Nessler, B., & Hochreiter, S. (2017). Gans trained by a two time-scale update rule converge to a local nash equilibrium. In Advances in neural information processing systems (pp. 6626-6637).

实验结果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107225556.png)
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201107225618.png)
图3 生成图像示意，最左一栏为FID（左边是SAGAN，右边是（c）的结果）
(c)Miyato, T., & Koyama, M. (2018). cGANs with projection discriminator. arXiv preprint arXiv:1802.05637.
---
title: "Domain Separation Networks."
layout: post
date: 2020-10-16 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---
# Domain Separation Networks. 

[NIPS2016] Bousmalis, K., Trigeorgis, G., Silberman, N., Krishnan, D., & Erhan, D. (Google Brain, Imperial College London, Google Research). Domain separation networks. In Advances in neural information processing systems (pp. 343-351).  

## 简介
这篇文章介绍的域独立网络，通过学习域不变表达，来解决域适应问题。  

## 相关工作
寻找源域到目标域的映射（26）  
Sun, B., Feng, J., & Saenko, K. (2015). Return of frustratingly easy domain adaptation. arXiv preprint arXiv:1511.05547.  
寻找域不变的表达（8,28,17）  
Ganin, Y., Ustinova, E., Ajakan, H., Germain, P., Larochelle, H., Laviolette, F., ... & Lempitsky, V. (2016). Domain-adversarial training of neural networks. The Journal of Machine Learning Research, 17(1), 2096-2030.  
Tzeng, E., Hoffman, J., Darrell, T., & Saenko, K. (2015). Simultaneous deep transfer across domains and tasks. In Proceedings of the IEEE International Conference on Computer Vision (pp. 4068-4076).  
Long, M., Cao, Y., Wang, J., & Jordan, M. (2015, June). Learning transferable features with deep adaptation networks. In International conference on machine learning (pp. 97-105). PMLR.  
尺度不变平均平方损失（6）  
Eigen, D., Puhrsch, C., & Fergus, R. (2014). Depth map prediction from a single image using a multi-scale deep network. In Advances in neural information processing systems (pp. 2366-2374).  

## 方法
本文介绍的网络结构示意图如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016224829.png)  
图1 网络结构示意图  
该网络包括5个部分：首先是独立地对源域和目标域的图像进行编码，得到特征$h_p^s$和$h_p^t$，然后是共享的编码器去学习域不变特征$h_c^s$和$h_c^t$，并利用这些特征进行源域和目标域图像的复原，得到$\hat{x}^s$和$\hat{x}^s$，当然也少不了分类网络对源域图像进行分类。
总的Loss是：
$$
\mathscr{L} = \mathscr{L}_{task} + \alpha\mathscr{L}_{recon} + \beta\mathscr{L}_{difference} + \gamma\mathscr{L}_{similarity}
$$
其中$\mathscr{L}_{task}$为任务的loss，对于分类任务，则自然为交叉熵loss：
$$
\mathscr{L}_{task} = - \sum_{i=0}^{N_s}y_i^slog\hat{y}_i^s
$$
重建的loss采用尺度不变平均平方损失，为：
$$
\mathscr{L}_{recon} = \sum_{i=1}^{N_s}\mathscr{L}_{si\_mse}(x_i^s,\hat{x}_i^s)+\sum_{i=1}^{N_t}\mathscr{L}_{si\_mse}(x_i^t,\hat{x}_i^t)
$$
其中：
$$
\mathscr{L}_{si\_mse}(x,\hat{x}) = \frac{1}{k}||x-\hat{x}||_2^2-\frac{1}{k^2}((x-\hat{x})\cdot 1_k)^2
$$
差异的Loss则是特征级别的，鼓励独立的特征和共享的特征尽可能正交，即：
$$
\mathscr{L}_{difference} = ||(H_c^s)^TH_p^s||_F^2 + ||(H_c^t)^TH_p^t||_F^2
$$
相似性loss包括两部分，其中一部分类似于对抗网络，有：
$$
\mathscr{L}_{similarity}^{DANN} = \sum_{i=0}^{N_s+N_t}{d_ilog\hat{d}_i+(1-d_i)log(1-\hat{d}_i)}
$$
另一部分Loss为特征级别的基于核方法的最大差异Loss，为
$$
\mathscr{L}_{similarity}^{MMD} = \frac{1}{(N^s)^2}\sum^{N^s}_{i,j=0}\kappa(h^s_{ci},h^s_{cj})-\frac{2}{N^sN^t}\sum^{N^s,N^t}_{i,j=0}\kappa(h^s_{ci},h^t_{cj})+\frac{1}{(N^t)^2}\sum^{N^t}_{i,j=0}\kappa(h^t_{ci},h^t_{cj})
$$

## 实验和结果
采用的数据集较小。主要为MNIST系列的玩具数据集。实验结果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016225306.png)  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016225324.png)  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016225432.png)  
文中的重建图像示意如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016225511.png)  
图2 图像重建示意  
---
title: "Learning from synthetic data: Addressing domain shift for semantic segmentation."
layout: post
date: 2020-10-17 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---
#  Learning from synthetic data: Addressing domain shift for semantic segmentation.

[CVPR2018] Sankaranarayanan, S., Balaji, Y., Jain, A., Nam Lim, S., & Chellappa, R. (UMIACS, University of Maryland, College Park, MD, GE Global Research, Niskayuna, NY and  Avitas Systems, GE Venture, Boston MA). Learning from synthetic data: Addressing domain shift for semantic segmentation. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 3752-3761).

## 简介
这篇文章介绍的通过重建合成真实图像去解决域适应问题。  
其应用示意图如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016233608.png)
图1 思路示意图  

## 相关工作
对抗方法。（7,2,28,19,14,32）  
Ganin, Y., & Lempitsky, V. (2015, June). Unsupervised domain adaptation by backpropagation. In International conference on machine learning (pp. 1180-1189). PMLR.  
Bousmalis, K., Silberman, N., Dohan, D., Erhan, D., & Krishnan, D. (2017). Unsupervised pixel-level domain adaptation with generative adversarial networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 3722-3731).  
Sankaranarayanan, S., Balaji, Y., Castillo, C. D., & Chellappa, R. (2018). Generate to adapt: Aligning domains using generative adversarial networks. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 8503-8512).  
Liu, M. Y., & Tuzel, O. (2016). Coupled generative adversarial networks. In Advances in neural information processing systems (pp. 469-477).  
Hoffman, J., Wang, D., Yu, F., & Darrell, T. (2016). Fcns in the wild: Pixel-level adversarial and constraint-based adaptation. arXiv preprint arXiv:1612.02649.   
Zhang, Y., David, P., & Gong, B. (2017). Curriculum domain adaptation for semantic segmentation of urban scenes. In Proceedings of the IEEE International Conference on Computer Vision (pp. 2020-2030).  


## 方法
本文介绍的网络结构示意图如下：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016233438.png)  
图2 网络结构示意图  
网络包括四个部分，特征提取（F网络），生成网络（G网络），分类网络（C网络）以及判别网络（D网络）。控制的Loss如下表所示：  
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201017000334.png)  

## 实验和结果
实验数据集包括SYNTHIA->Cityscapes,以及GTA5->Cityscapes。
实验结果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201016233354.png)
---
title: "Label-Driven Reconstruction for Domain Adaptation in Semantic Segmentation."
layout: post
date: 2020-09-25 12:32
image: /assets/images/markdown.jpg
headerImage: false
tag:
- paper reading
star: true
category: blog
author: Xi Wu
description: paper reading
---
# Label-Driven Reconstruction for Domain Adaptation in Semantic Segmentation. 

[ECCV2020]Yang, J., An, W., Wang, S., Zhu, X., Yan, C., & Huang, J. (University of Texas at Arlington, Texas, USA). Label-Driven Reconstruction for Domain Adaptation in Semantic Segmentation. 

## 简介
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920183024.png)
图1 思路示意图
首先采用图像到图像的变换方法，将目标域的图像变换为源域的图像，然后作分割，最后再将分割后的标签再重建出来。
框架示意图如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920222841.png)
图2 框架示意图

## 相关工作
这里记录一下像素级和特征级的适应用于语义分割。（1,37,58,52,7）
(a)Hoffman, J., Tzeng, E., Park, T., Zhu, J. Y., Isola, P., Saenko, K., ... & Darrell, T. (2018, July). Cycada: Cycle-consistent adversarial domain adaptation. In International conference on machine learning (pp. 1989-1998). PMLR..
(b)Murez, Z., Kolouri, S., Kriegman, D., Ramamoorthi, R., & Kim, K. (2018). Image to image translation for domain adaptation. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 4500-4509).
(c)Zhang, Y., Qiu, Z., Yao, T., Liu, D., & Mei, T. (2018). Fully convolutional adaptation networks for semantic segmentation. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 6810-6818).
(d)Wu, Z., Han, X., Lin, Y. L., Gokhan Uzunbas, M., Goldstein, T., Nam Lim, S., & Davis, L. S. (2018). Dcan: Dual channel-wise alignment networks for unsupervised scene adaptation. In Proceedings of the European Conference on Computer Vision (ECCV) (pp. 518-534).
(e)Chen, Y., Li, W., Chen, X., & Gool, L. V. (2019). Learning semantic segmentation from synthetic data: A geometrically guided input-output adaptation approach. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 1841-1850).

## 方法
网络架构示意图如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920183051.png)
图3 网络架构示意图
设计Loss如下：
对于分割网络G，有
$$
L_G = L_{seg}(G(X_s),Y_s) + L_{seg}(G(X_{t \rightarrow s}), Y_t^{ssl}) + \lambda L_{adv}(G(X_s),G(X_{t \rightarrow s}))
$$
$$
L_{adv}(G(X_s),G(X_{t \rightarrow s})) = E[logD(G(X_s))]+E[log(1-D(G(X_s))]
$$
对于重建网络，则是分为了3个尺度，1x,2x,3x，重建loss为：
$$
L_{adv}^{\phi} = \sum_{k=1}^3[E[logD_k(\omega_\phi,X_\phi)]] + E[log(1-D_k(\omega_\phi,M(\omega_\phi)))]
$$
$$
L_{rec}^{\phi} = E\sum_{m=1}^M[||V^{(m)}(M(\omega_\phi)) - V^{(m)}(X_\phi)||_1] + E\sum_{k=1}^3\sum_{m=1}^M[||D_k^{(n)}(\omega_\phi,X_\phi) - D_k^{(n)}(\omega_\phi,M(\omega_\phi))||_1]
$$


## 实验和结果
数据集采用 GTA5 ->Cityscapes以及SYNTHIA->Cityscapes。
数值结果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920223052.png)
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920223115.png)

视觉效果如下：
![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20200920223255.png)
图4 视觉结果图
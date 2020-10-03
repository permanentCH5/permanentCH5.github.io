

[ICCV2019]Zou, Z., Li, W., Shi, T., Shi, Z., & Ye, J. (University of Michigan, Ann Arbor, Beihang University, NetEase Fuxi AI Lab and Didi Chuxing). Generative Adversarial Training for Weakly Supervised Cloud Matting.



## Introduction
This paper introduces a cloud matting method based on generative adversarial networks (GAN). The framework consists of a cloud discriminator(D), cloud generator(G) and a cloud matting network(F). It can generate cloud image and predict the cloud reflectance and the attenuation. The overview of this method is illustrated in Fig.1.


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003094700.png)


Fig.1 Overview of this method


Although I dislike the concept of weakly supervised training since it did not utilize the ground truth image, the generating method is still valuable and is a good example in related applications. 

## Related Works
### Part I. Image Matting Applications(3,5,35,42)
Chen, G., Han, K., & Wong, K. Y. K. (2018). TOM-Net: Learning transparent object matting from a single image. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 9233-9241).


Chen, Q., Ge, T., Xu, Y., Zhang, Z., Yang, X., & Gai, K. (2018, October). Semantic human matting. In Proceedings of the 26th ACM international conference on Multimedia (pp. 618-626).


Shen, X., Tao, X., Gao, H., Zhou, C., & Jia, J. (2016, October). Deep automatic portrait matting. In European conference on computer vision (pp. 92-107). Springer, Cham.


Xu, N., Price, B., Cohen, S., & Huang, T. (2017). Deep image matting. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2970-2979).

### Part II. Generative Adversarial Network Applications(7,30,14,47,16,25)
Denton, E. L., Chintala, S., & Fergus, R. (2015). Deep generative image models using a laplacian pyramid of adversarial networks. In Advances in neural information processing systems (pp. 1486-1494).


Radford, A., Metz, L., & Chintala, S. (2015). Unsupervised representation learning with deep convolutional generative adversarial networks. arXiv preprint arXiv:1511.06434.


Isola, P., Zhu, J. Y., Zhou, T., & Efros, A. A. (2017). Image-to-image translation with conditional adversarial networks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 1125-1134).


Zhu, J. Y., Park, T., Isola, P., & Efros, A. A. (2017). Unpaired image-to-image translation using cycle-consistent adversarial networks. In Proceedings of the IEEE international conference on computer vision (pp. 2223-2232).


Ledig, C., Theis, L., Huszár, F., Caballero, J., Cunningham, A., Acosta, A., ... & Shi, W. (2017). Photo-realistic single image super-resolution using a generative adversarial network. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4681-4690).


Lutz, S., Amplianitis, K., & Smolic, A. (2018). Alphagan: Generative adversarial networks for natural image matting. arXiv preprint arXiv:1807.10088.



## Proposed Method
### Imaging Model
The imaging model is shown as Fig.2.


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003102912.png)

Fig.2 The imaging model


What we see in a remote sensing image is in fact the energy receiving from the detecting sensor $\varepsilon_{sensor}$. This part of energy can be divided into three parts, the cloud reflectance energy $\varepsilon_{c}$, the ground reflectance $\varepsilon_{gr}$ ,and the ground surface radiation energy $\varepsilon_{gs}$. However, as the clouds cover the ground, the latter two parts of ground energy will be lost to some degree. To model this, atmospheric attenuation $\alpha$ is used. The whole cloud model is built as the following equation.


$$
\varepsilon_{sensor} = \varepsilon_{c} + (1-\alpha)(\varepsilon_{gr} + \varepsilon_{gs} ) = \varepsilon_{c} + (1-\alpha)\varepsilon_g
$$


Therefore, for a cloud image $y$, it can be represented by a sum of two image, the cloud reflectance image $r_c$ and a background image $r_g$. In an image matting way, the cloud image $y$ is defined as,


$$
y = r_c + (1-\alpha)r_g
$$


For cloud detection applications, the core tasks are finding ways to estimate $r_c$ and $\alpha$ given an input image $y$. For cloud image generation, the task is to use $r_c$ and $\alpha$ in the above equation to fuse a ground image(no clouds).

### Method Based on Generative Adverserial Networks
The whole framework is as follows,


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003104620.png)


Fig.3 The whole framework.


The cloud generator(G) is responsible for generating (but I prefer to estimating) the cloud reflectance $\hat{r}_c$ and the attenuation $\hat{\alpha}$. The cloud discriminator is used to discriminate a generated image(fake) and a real image. The final cloud matting networks are used to reconstruct the cloud reflectance $\hat{r}_c$ and the generated cloud attenuation $\hat{\alpha}$. It should be noted that the generated variables are the attributions of the generated image $\hat{y}$, not the ones input cloud image $y$. Therefore, for cloud detection tasks, Cloud Matting Networks (F) are necessary. The supplementary materials have described this point.


The loss settings are as follows. The whole loss is $\mathscr{L}(D,G,F)$, which consists of two parts, the adverserial loss $\mathscr{L}\_{adv}(D,G)$, and the cloud matting loss $\mathscr{L}\_{matt}(F)$.


$$
\mathscr{L}(D,G,F) = \mathscr{L}_{adv}(D,G) + \beta\mathscr{L}_{matt}(F)
$$


It should be noted that the cloud matting loss is combined by two $L_1$ losses (shown as follows). In other literations like (a,b), they use $L_2$ losses. Maybe both two losses can work in the applications.


$$
\mathscr{L}_{matt}(F) = \mathscr{L}_{r} + \mathscr{L}_{\alpha} = E_{x,y\thicksim p(x,y)}{||r_c-\hat{r}_c||_1 + ||\alpha_c-\hat{\alpha}_c||_1}
$$


(a)Chen, G., Han, K., & Wong, K. Y. K. (2018). TOM-Net: Learning transparent object matting from a single image. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 9233-9241).


(b)Xu, N., Price, B., Cohen, S., & Huang, T. (2017). Deep image matting. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2970-2979).

## Experiments
The framework can solve many tasks including cloud detection, cloud removal, cloud montage(cloud image generation). The visualizations are as follows.


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003111106.png)


Fig.4 Cloud detection.


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003111150.png)


Fig.5 Cloud removal.


![](https://picture18810693345.oss-cn-beijing.aliyuncs.com/img/20201003111211.png)


Fig.6 Cloud montage.


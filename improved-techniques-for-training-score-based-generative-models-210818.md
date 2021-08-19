# Improved Techniques for Training Score-Based Generative Models

[TOC]

## 信息

Authors: Yang Song and Stefano Ermon

Year: NeurIPS 2020

Bibtex:

```latex
@article{song2020improved,
  title={Improved techniques for training score-based generative models},
  author={Song, Yang and Ermon, Stefano},
  journal={arXiv preprint arXiv:2006.09011},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/improved-techniques-for-training-score-based-generative-models.pdf)



## 概括

这篇文章针对 NCSN 网络（score based 生成模型）提出了五个改进。主要是在 noise level 的选择、score function 的条件设计、朗之万动态采样的参数选择和采样时网络参数的选择。作者通过用一些简单的假设，对其中的一些问题进行了分析，由此得到了解决方案（也就是具体怎么改进）。这些改进的最主要的好处是让 NCSN 网络可以用于生成更高分辨率的图像。此前的模型则只能生成低分辨率的图像。



## 方法

作者首先介绍了 NCSN 模型。然后对原来的 inference 算法最后增加了一个去噪声步骤。这个去噪实际上还是用 score function 网络。这个新的算法如下：

![屏幕快照 2021-08-19 下午12.03.37](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午12.03.37.png)

作者指出，原始的模型对于高分辨率的图像生成任务是比较差的，之后提出了四个方面的设计需要改进（实际上最后还增加了一个网络参数的改进）

1. 如何选择噪声水平 $\{\sigma_i\}^L_{i=1}$
2. score 网络的条件如何包含到网络中。
3. 朗之万动态采样的 step size如何设定。
4. 朗之万动态采样的步骤数量如何确定。

下面对作者提出的所有改进进行说明，并简要分析改进的想法来源。

1. $\sigma_L$ 足够小就可以，但是 $\sigma_1$ 必须要足够大，必须有训练数据的最大欧式距离那么大。这个是从数据的经验分布和朗之万动态的相关内容进行分析的结果。

2. $\sigma_i$ 使用几何序列的形式，固定的比例系数 $\gamma$ 满足 $\Phi(\sqrt{2 D}(\gamma-1)+3 \gamma)-\Phi(\sqrt{2 D}(\gamma-1)-3 \gamma) \approx 0.5$，这里的 $\Phi$ 是标准高斯分布的 CDF，D 是数据的维数。这个分析是从一个单点分布出发。

3. 直接将 $\sigma$ 作为 score function 的控制。即 $s_{\theta}(x, \sigma) = s_{\theta}(x)/\sigma$，这样 score 网络就是一个更简单的网络了。不过这里没有说去掉 $\sigma$ 的条件后网络结构有什么改变。需要看附录。这个分析的来源是从网络输出的范数估计。

4. T 尽可能大，根据计算量负担来选择。step size $\epsilon$ 则是通过下面的式子来确定，让这个值尽可能为 1.作者表示，在计算的时候，要用格点搜索的方法来找合适的 $\epsilon$ 而不是用优化的方法来找。
   $$
   \frac{s_{T}^{2}}{\sigma_{i}^{2}}=\left(1-\frac{\epsilon}{\sigma_{L}^{2}}\right)^{2 T}\left(\gamma^{2}-\frac{2 \epsilon}{\sigma_{L}^{2}-\sigma_{L}^{2}\left(1-\frac{\epsilon}{\sigma_{L}^{2}}\right)^{2}}\right)+\frac{2 \epsilon}{\sigma_{L}^{2}-\sigma_{L}^{2}\left(1-\frac{\epsilon}{\sigma_{L}^{2}}\right)^{2}}
   $$
   也就是选择合适的 $\epsilon$ 使得右边的结果是 1

5. 使用 exponential moving average 的方法重新计算网络的参数。具体来说，就是对每次优化更新的网络参数，通过 $\boldsymbol{\theta}^{\prime} \leftarrow m \boldsymbol{\theta}^{\prime}+(1-m) \boldsymbol{\theta}_{i}$ 的方式来计算，$\theta_i$ 是每次迭代更新的网络参数。在训练的时候，这个 EMA 没用，在 inference 的时候先用 EMA 得到最终的网络。

## 实验

实验部分主要是针对改进的消融实验，使用改进的 NCSN 模型用于生成高分辨率的图像。



## 参考文献

这篇文章没有什么需要参考的额外文献



## 更多细节

### 网络结构

相较于原始的 NCSN 模型，作者又做了一些改进。首先基本模型还是 RefineNet。原来的模型因为 $\sigma$ 的条件是通过 IN 层实现的，现在没有了，所以又变回原来的 IN 层，不过对于 IN 层的改进保留了。所以叫 IN++ 层。

pooling 层改回 max pooling，和 RefineNet 一样，并且去掉了 RefineNet block 的所有的标准化层，这样就和原本的 RefineNet 基本保持一致。作者说这是为了尽量减少差异。

激活函数还是 ELU

称 ResNet block（使用了 IN++ 而不是 BN 层）为 ResBlock，称 RefineNet block 为 RefineBlock，如果使用了原来的 CondIN++，就增加前缀 Cond。

网络结构：

![屏幕快照 2021-08-19 下午12.37.33](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午12.37.33.png)

![屏幕快照 2021-08-19 下午12.37.41](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午12.37.41.png)

### 损失函数

和 NCSN 一样

### 数据预处理

Datasets: We use the following datasets in our experiments: CIFAR-10 [2], CelebA [16], LSUN [27], and FFHQ [28]. CIFAR-10 contains 50000 training images and 10000 test images, all of resolution 32 × 32. CelebA contains 162770 training images and 19962 test images with various resolutions. For preprocessing, we ﬁrst center crop them to size 140 × 140, and then resize them to 64 × 64. We choose the church_outdoor, bedroom and tower categories in the LSUN dataset. They contain 126227, 3033042, and 708264 training images respectively, and all have 300 validation images. For preprocessing, we ﬁrst resize them so that the smallest dimension of images is 96 (for church_outdoor) or 128 (for bedroom and tower), and then center crop them to equalize their lengths and heights. Finally, the FFHQ dataset consists of 70000 high-quality facial images at resolution 1024 × 1024. We resize them to 256 × 256 in our experiments. Because FFHQ does not have an ofﬁcial test dataset, we randomly select 63000 images for training and the remaining 7000 as the test dataset. In addition, we apply random horizontal ﬂip as data augmentation in all cases.

### 训练参数设定

如果不使用改进 3，学习率是 1e-3，否则是 1e-4

Adam 优化器，这个优化器里的 $\epsilon$ 参数对 FFHQ 是 10-3，其他是 1e-8。

计算 $\sigma_1$ 的时候：When the number of training data is larger than 60000, we randomly sample 10000 of them and compute the maximum pairwise distance, which is set as σ 1 for NCSNv2.

![屏幕快照 2021-08-19 下午12.28.28](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午12.28.28.png)

### inference参数设定

使用改进后的算法，T 和 $\epsilon$ 通过改进 4 确定，网络参数使用了改进 5.

![屏幕快照 2021-08-19 下午12.03.37](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-19 下午12.03.37.png)

### 实验结果

在高分辨率的图像，新的方法表现得比 NCSN 原始模型好很多。

### 代码

无
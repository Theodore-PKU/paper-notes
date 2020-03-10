# 191229-200104 论文阅读总结

[TOC]

一共看了 21 篇论文。

## 论文列表

[Deep Residual Learning for Compressed Sensing MRI](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-residual-learning-for-compressed-sensing-mri.md)

[Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss](https://github.com/Theodore-PKU/paper-notes/blob/master/compressed-sensing-mri-reconstruction-using-a-generative-adversarial-network-with-a-cyclic-loss.md)

[Deep Learning Beamforming for Sub-Sampled Ultrasound Data](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-learning-beamforming-for-sub-sampled-ultrasound-data.md)

[ADMM-CSNet: A Deep Learning Approach for Compressive Sensing MRI](https://github.com/Theodore-PKU/paper-notes/blob/master/admm-csnet-a-deep-learning-approach-for-compressive-sensing-mri.md)

[Accelerating Magnetic Resonance Imaging via Deep Learning](https://github.com/Theodore-PKU/paper-notes/blob/master/accelerating-magnetic-resonance-imaging-via-deep-learning.md)

[MoDL: Model Based Deep Learning Architecture for Inverse Problems](https://github.com/Theodore-PKU/paper-notes/blob/master/modl-model-based-deep-learning-architecture-for-inverse-problems.md)

[Deep Convolutional Compressed Sensing for LiDAR Depth Completion](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-convolutional-compressed-sensing-for-lidar-depth-completion.md)

[Bayesian Deep Learning for Accelerated MR Image Reconstruction](https://github.com/Theodore-PKU/paper-notes/blob/master/bayesian-deep-learning-for-accelerated-mr-image-reconstruction.md)

[Full Image Recover for Block-based Compressive Sensing](https://github.com/Theodore-PKU/paper-notes/blob/master/full-image-recover-for-block-based-compressive-sensing.md)

[A Provably Convergent Scheme for Compressive Sensing under Random Generative Priors](https://github.com/Theodore-PKU/paper-notes/blob/master/a-provably-convergent-scheme-for-compressive-sensing-under-random-generative-priors.md)

[Deep Ptych: Subsampled Fourier Ptychography using Generative Priors](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-ptych-subsampled-fourier-ptychography-using-generative-priors.md)

[Image Reconstruction Is a New Frontier of Machine Learning](https://github.com/Theodore-PKU/paper-notes/blob/master/imgae-reconstruction-is-a-new-frontier-of-machine-learning.md)

[Deep Learning-Based Kronecker Compressive Imaging](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-learning-based-kronecker-compressive-imaging.md)

[Learning a Variational Network for Reconstruction of Accelerated MRI Data](https://github.com/Theodore-PKU/paper-notes/blob/master/learning-a-variational-network-for-reconstruction-of-accelerated-mri-data.md)

[Perceptual Compressive Sensing](https://github.com/Theodore-PKU/paper-notes/blob/master/perceputal-compressive-sensing.md)

[Reproducing AmbientGAN: Generative models from lossy measurements](https://github.com/Theodore-PKU/paper-notes/blob/master/reproducing-ambientgan-generative-models-from-lossy-measurements.md)

[Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior](https://github.com/Theodore-PKU/paper-notes/blob/master/training-deep-learning-based-image-denoisers-from-undersampled-measurements-without-ground-truth-and-without-image-prior.md)

[Optimization Algorithm Inspired Deep Neural Network Structure Design](https://github.com/Theodore-PKU/paper-notes/blob/master/optimization-algorithm-inspired-deep-neural-network-structure-design.md)

Training Convolutional Neural Networks and Compressed Sensing End-to-End for Microscopy Cell Detection/Cell Detection in Microscopy Images with Deep Convolutional Neural Network and Compressed Sensing (这两篇基本一样)

[Unrolled Optimization with Deep Priors](https://github.com/Theodore-PKU/paper-notes/blob/master/unrolled-optimization-with-deep-priors.md)

Deep Learning for Accelerated Ultrasound Imaging

## 论文分类

- MRI
  1. Deep Residual Learning for Compressed Sensing MRI
  2. Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss
  3. ADMM-CSNet: A Deep Learning Approach for Compressive Sensing MRI
  4. Accelerating Magnetic Resonance Imaging via Deep Learning
  5. Bayesian Deep Learning for Accelerated MR Image Reconstruction
  6. Learning a Variational Network for Reconstruction of Accelerated MRI Data
- Ultrasound Data
  1. Deep Learning Beamforming for Sub-Sampled Ultrasound Data
- inverse problem
  1. MoDL: Model Based Deep Learning Architecture for Inverse Problems

- depth completion
  1. Deep Convolutional Compressed Sensing for LiDAR Depth Completion

- image recovery
  1. Full Image Recover for Block-based Compressive Sensing
  2. Deep Learning-Based Kronecker Compressive Imaging
  3. Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior

- theory
  1. A Provably Convergent Scheme for Compressive Sensing under Random Generative Priors
  2. Unrolled Optimization with Deep Priors

- Fourier Ptychography 
  1. Deep Ptych: Subsampled Fourier Ptychography using Generative Priors

- review
  1. Image Reconstruction Is a New Frontier of Machine Learning

## 思路

- GAN
  1. Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss：堆叠两个生成网络，判别网络和生成网络的编码部分结构一样，对抗loss结合kspace loss，image loss。

- ADMM
  1. ADMM-CSNet: A Deep Learning Approach for Compressive Sensing MRI：提出了 generic ADMM-net，是对另一种admm算法的解法的拓展。
  2. Deep Convolutional Compressed Sensing for LiDAR Depth Completion: 这篇的问题模型是 deep component analysis

- model based
  1. MoDL: Model Based Deep Learning Architecture for Inverse Problems：将CNN denoiser作为正则项进行端到端训练，使用了共享参数和CG迭代算法。

- bayesian method
  1. Bayesian Deep Learning for Accelerated MR Image Reconstruction:  结合了 MC dropout and heteroscedastic loss 

- GAN and latent variable research
  1. Deep Ptych: Subsampled Fourier Ptychography using Generative Priors: 类似 [Compressed Sensing using Generative Models](https://github.com/Theodore-PKU/paper-notes/blob/master/compressed-sensing-using-generative-models.md) 的思路，用一个训练好的 GAN，通过搜索 z 来重建信号

- 迭代算法展开
  1. Learning a Variational Network for Reconstruction of Accelerated MRI Data：使用的正则项是 Fields of Experts model，每一层的参数都不一样，都可学习。

- ambient GAN
  1. Reproducing AmbientGAN: Generative models from lossy measurements: 对测量值进行判别。

- D-AMP
  1. Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior：不使用真实值来训练denoiser，方法是 SURE 的损失函数。

## 实现细节

### 网络结构

- U-net
  1. Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss：没有用拼接的方式进行 skip 连接，而是加和；输出是残差结构。

- generic ADMM-net
  1. 比较简单，每个小模块只有两层卷积层。
- MoDL
  1. 多层卷积 + BN  + ReLU

### 损失函数

- 对抗 loss + kspace loss + image loss
  1. Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss：定义loss不限制 kspace 和 image 的距离度量；对抗loss用的是对数loss。

- 多尺度 SSIM loss
  1. Deep Learning Beamforming for Sub-Sampled Ultrasound Data：对每个level的图像都有相应的计算，然后整合起来。
- NMSE：
  1. ADMM-CSNet: A Deep Learning Approach for Compressive Sensing MRI

- MSE:
  1. Accelerating Magnetic Resonance Imaging via Deep Learning
  2. MoDL: Model Based Deep Learning Architecture for Inverse Problems

- heteroscedastic loss：
  1. Bayesian Deep Learning for Accelerated MR Image Reconstruction

- 基于magnitude的mse loss：
  1. Learning a Variational Network for Reconstruction of Accelerated MRI Data

- SURE 损失函数：
  1. Training deep learning based image denoisers from undersampled measurements without ground truth and without image prior

### 特别技巧

- U-net 的每一层的block使用了三层自编码形式的残差结构，中间层的通道数是另外两层的一半。（Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss）
- refineNet，堆叠多个生成网络使效果更好（Compressed Sensing MRI Reconstruction using a Generative Adversarial Network with a Cyclic Loss）
- 共享参数，减少训练数据需求。（MoDL: Model Based Deep Learning Architecture for Inverse Problems）

## 个人思考

- 判别器和生成器的编码部分有某种相似之处
- 降低通道数的做法在某种程度上可以视作一种低秩分解？这个结构和压缩感知或深度网络表征图像流形是否有关系？
- 把逆问题中深度学习的训练过程看成是一个同时训练字典和字典编码的过程
- 如何判断图像的全局特性和局部特性，或者说对于高维数据而言，如何判断不同维度之间的独立性。如果是简单一点的单张图像，比如说纹理，可以不可以得到一个全局特性和局部特性的刻画？
- ADMM-net 的泛化性
- 更统一的框架理解不同结构的网络的各个部件的算法含义。
- 如果正则项是一种投影的特征，那么为什么要拘泥于去噪的形式？直觉上应该是一种可以直接投影到图像流形的方法。但是去噪的形式只会觉得是一种变换，如何保证这种变换保证结果一定是在图像流形上？比如输入是个非常随机的东西。当然如果有data fidelity项，可以通过这个达到某种折衷。
- 对于 [Compressed Sensing using Generative Models](https://github.com/Theodore-PKU/paper-notes/blob/master/compressed-sensing-using-generative-models.md) 这类思路，可以从生成模型生成的低维流形上的零空间来考虑。也许有和压缩感知框架对应的理论分析，比如唯一解。压缩感知因为有1范数极小，所以似乎不用太在意零空间。可以从解的存在性和选择这个角度思考。
- 在的深度学习方法中，激活函数如何用迭代算法中的 shrinkage 函数来理解。
- 在 ambient GAN中，理论上如何保持测量值分布和原始信号分布存在一一对应？之前有看到论文用 kspace 来作为判别的内容，两者本质是一样的。


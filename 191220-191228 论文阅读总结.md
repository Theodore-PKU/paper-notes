### 本周总结

一共看了 35 篇论文。下面是一个简单的汇总。



### 问题

1. MRI reconstruction
2. Low-Dose CT denoising
3. 一般的 CS 重建
4. other application
5. theory



### 思路

1. #### unfold an algorithm

- Deep ADMM-Net for Compressive Sensing MRI
  - ADMM算法，对所有参数
- Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network
  - frame-based，去噪自编码器替代算法的一部分

- DNNs for Sparse Coding and Dictionary Learning
  - proximal gradient 算法，训练激活函数
- Learned D-AMP: Principled Neural Network Based Compressive Image Recovery
  - D-AMP 算法，用网络替代去噪模型

- Deep Learning-Guided Image Reconstruction from Incomplete Data

2. #### de-aliasing（有一个比较容易得到的初值）

- Deep De-Aliasing for Fast Compressive Sensing MRI
  - GAN + 不同的loss
- DAGAN: Deep De-Aliasing Generative Adversarial Networks for Fast Compressed Sensing MRI Reconstruction
  - GAN + 不同的loss

- Deep convolutional neural networks for accelerated dynamic magnetic resonance imaging
  - Unet + 不同的loss
- Deep Generative Adversarial Networks for Compressed Sensing (GANCS) Automates MRI
  - GAN + data fidelity
- Multi-Scale Wavelet Domain Residual Learning for Limited-Angle CT Reconstruction
  - Unet + wavelet. + residual learning

3. #### from measuements to signal

- Learning to Invert: Signal Recovery via Deep Convolutional Networks
  - 测量矩阵转置 + CNN
- Convolutional Neural Networks for Non-iterative Reconstruction of Compressively Sensed Images
  - block-based，测量矩阵转置 + CNN

- DR$^2$ -Net: Deep Residual Reconstruction Network for Image Compressive Sensing
  - block-based，线性层变换 + ResNet + BM3D去噪
- Compressed Sensing MRI Using a Recursive Dilated Network
  - Recursive blocks + dilated convolution + data fidelity

4. #### learn how to measuemen signal

- DeepCodec: Adaptive Sensing and Recovery via Deep Convolutional Neural Networks
  - 自编码模型，编码就是测量值

5. #### projection networks

- One Network to Solve Them All — Solving Linear Inverse Problems using Deep Projection Models
  - 通用的投影网络

6. #### low-rank decomposition

- Nonlocal Low-Rank Tensor Factor Analysis for Image Restoration
  -  Low-Rank Tensor Decomposition + ADMM

7. #### distribution

- Compressed Sensing using Generative Models



### 网络结构

- 多尺度——Unet
  - Deep artifact learning for compressed sensing and parallel MRI
  - DAGAN: Deep De-Aliasing Generative Adversarial Networks for Fast Compressed Sensing MRI Reconstruction
  - Deep De-Aliasing for Fast Compressive Sensing MRI
  - Deep convolutional neural networks for accelerated dynamic magnetic resonance imaging
- 自编码
  - Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network

- ResNet

  

### 损失函数

- 图像域的2范数 loss
- 图像域的1范数 loss
- 对抗 loss
- vgg 的perceptual loss
- kspace 2范数 loss 
- kspace 1范数 loss



### 技巧

1.  contourlet 系数表示：方向性
   - Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network
2. 输出噪声或伪影——Residual learning：比图像对应的流形维度更低（拓扑分析的角度）
   - Deep De-Aliasing for Fast Compressive Sensing MRI
   - DAGAN: Deep De-Aliasing Generative Adversarial Networks for Fast Compressed Sensing MRI Reconstruction
   - Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network
   - Deep artifact learning for compressed sensing and parallel MRI
   - DR$^2$ -Net: Deep Residual Reconstruction Network for Image Compressive Sensing

3. data fidelity
   - Compressed Sensing MRI Using a Recursive Dilated Network
   - Deep Generative Adversarial Networks for Compressed Sensing (GANCS) Automates MRI
   - other unrolled-based methods

### Interesting Points

- 深度卷积网络也可以看成是一个framelet。自编码的模型中，编码部分使用较少数量的filters，相当于shrinkage的效果，或者是一个低秩分解的效果
  - Deep Convolutional Framelet Denosing for Low-Dose CT via Wavelet Residual Network
- 正则项和对应的 proximal 算子，几何的角度，投影，解空间和信号流形的交
  - Learned D-AMP: Principled Neural Network Based Compressive Image Recovery
  - Deep ADMM-Net for Compressive Sensing MRI
  - One Network to Solve Them All — Solving Linear Inverse Problems using Deep Projection Models
- 随机初始化的卷积网络具有的一些性质，比如直接用于分类也有一定的准确率（只是卷积核的参数是固定的）：为什么训练好的网络具有可压缩性？特征是冗余的？训练只是对网络的初始随机参数进行微调（超平面的微调）？
  - Towards Understanding the Invertibility of Convolutional Neural Networks
- 伪影的全局特征
  - Compressed Sensing MRI Using a Recursive Dilated Network
- 最优的采样方式和算法的关系
  - Learning-Based Compressive MRI


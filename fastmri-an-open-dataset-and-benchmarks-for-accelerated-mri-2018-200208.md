### Paper:

fastMRI: An Open Dataset and Benchmarks for Accelerated MRI

### Author:

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208214226.png" width="70%"/>

### Year:

2018

### Notes:

阅读日期：2020.02.08

泛读。这篇文章是介绍数据集的，以前就看过了，现在整理一下笔记。

**数据**分为四个部分：

1. Raw multi-coil k-space data：未处理过的多线圈 MR 测量数据，复数类型

2. Emulated single-coil k-space data：⽤多线圈的 k-space 数据仿真模拟的单线 圈数据，使⽤的⽅法是 ESC method（⼀种线性组合）

3. Ground-truth images：⽤多线圈的 MR 测量数据重建出的实值图像，因为是多线圈，所以采⽤了如下⽅法：

   <img src="http://latex.codecogs.com/svg.latex? \begin{array}{c}{\tilde{m}_{i}=\mathcal{F}^{-1}\left(y_{i}\right)} \\ {\tilde{m}_{\mathrm{rss}}=\left(\sum_{i=0}^{n_{c}}\left|\tilde{m}_{i}\right|^{2}\right)^{1 / 2}}\end{array}" border="0"/>

   最终图像是 $\tilde{m}_{\mathrm{rss}}$，另外在单线圈数据集中的 groundtruth images 还包括⽤ Emulated single-coil k-space data 重建出的图像。

4. DICOM images：是经过处理后的图像，包含多种扫 描设备和脉冲序列。

数据集中包含两类使⽤不同的序列⽣成的图像，PD 和 FSPD（coronal proton-density with and without fat suppression）。

Multi-coil 和 Single-coil 的数据集 training 和 testing 来源⼀样。test 和 challenge 不同。

**采样方式**采用笛卡尔采样。对于4倍加速的情形，kspace 的低频中央区域是采样 8%，其他区域随机采样。对于8倍的加速情形，kspace 的低频中央区域是采样 4%，其他区域也随机采样。

**评价指标**有三个：

1. NMSE

   <img src="http://latex.codecogs.com/svg.latex? \operatorname{NMSE}(\hat{v}, v)=\frac{\|\hat{v}-v\|_{2}^{2}}{\|v\|_{2}^{2}}" border="0"/>

2. PSNR

   <img src="http://latex.codecogs.com/svg.latex? \operatorname{PSNR}(\hat{v}, v)=10 \log _{10} \frac{\max (v)^{2}}{\operatorname{MSE}(\hat{v}, v)}" border="0"/>

3. SSIM

   <img src="http://latex.codecogs.com/svg.latex? \operatorname{SSIM}(\hat{m}, m)=\frac{\left(2 \mu_{\hat{m}} \mu_{m}+c_{1}\right)\left(2 \sigma_{\hat{m} m}+c_{2}\right)}{\left(\mu_{\hat{m}}^{2}+\mu_{m}^{2}+c_{1}\right)\left(\sigma_{\hat{m}}^{2}+\sigma_{m}^{2}+c_{2}\right)}" border="0"/>

   其中，$c_{1}=\left(k_{1} L\right)^{2}, c_{2}=\left(k_{2} L\right)^{2}, k_{1}=0.01, k_{2}=0.03, L=\max(v)$，window size 是 7x7.

最后论文给出了 TV 模型和基本 U-Net 模型的**重建效果**（single-coil 和 multi-coil）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208213932.png" width="80%"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200208213922.png" width="80%"/>

### Bibtex:

```latex
@article{zbontar2018fastmri,
  title={fastMRI: An open dataset and benchmarks for accelerated MRI},
  author={Zbontar, Jure and Knoll, Florian and Sriram, Anuroop and Muckley, Matthew J and Bruno, Mary and Defazio, Aaron and Parente, Marc and Geras, Krzysztof J and Katsnelson, Joe and Chandarana, Hersh and others},
  journal={arXiv preprint arXiv:1811.08839},
  year={2018}
}
```


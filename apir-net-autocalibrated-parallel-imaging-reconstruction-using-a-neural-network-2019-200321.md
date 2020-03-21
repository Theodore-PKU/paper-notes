### Paper:

APIR-Net: Autocalibrated Parallel Imaging Reconstruction using a Neural Network

### Author:

Chaoping Zhang, Florian Dubost, Marleen de Bruijne, Stefan Klein, Dirk H.J. Poot

### Year:

2019

### Notes:

泛读。这篇文章研究的是并行 MRI 的重建。无监督的方法。无监督指的是在训练的损失函数上只考虑 sample 的部分。这篇文章用的网络目的是将未采样的 kspace 进行填补。所以类似于 GRAPPA 或者 RAKI。网络的输入是 pattern 部分的 kspace，输出是 full kspace，损失函数比较的是 sample 部分的误差。采样部分是已知的，不需要知道其他未采样的 kspace 真值，所以是无监督的训练。实际上，因为 pattern 部分不包括 ASC 的数据，而 sample 部分包括 ASC 的数据，所以和 GRAPPA 类似，还是用 ACS 的 pattern 模式的部分数据预测 ASC 的 full 数据。

首先说明一下作者定义的和采样位置有关的三个概念，sample，pattern，acs。sample 的部分包含 pattern 和 acs，pattern 是指欠采样的方式，acs 是中心的 full 采样的部分。

GRAPPA 算法估计的卷积核是用如下方式估计出来的：
$$
\hat{n}=\arg \min \left\|\left(S-n \circledast \left(S \circ M_{\text {pattern}}\right)\right) \circ M_{a c s}\right\|_{2}^{2}+\lambda\|I n\|_{2}^{2}
$$
估计出 $n$ 之后 kspace 的补全就是：
$$
S_{p r e d i c t e d}=n \circledast \left(S \circ M_{p a t t e r n}\right)\\
S_{\text {final}}=S \circ M_{\text {sampled}}+S_{\text {predicted}} \circ\left(1-M_{\text {sampled}}\right)
$$
最终的重建结果是：
$$
X=\sqrt{\frac{1}{C} \sum_{c} | \text { iFFT }\left._{c}\left(S_{\text {final }}\right)\right|^{2}}
$$
对于这篇文章而言，只有 $S_{predict}$ 的部分使用了网络来生成，其它部分的计算和 GRAPPA 是一样的。从损失函数中可以看出整个网络的训练内容：
$$
\hat{\theta}=\arg \min \left\|\left(A_{\theta}\left(S \circ M_{\text {pattern}}\right)-S\right) \circ M_{\text {sampled}}\right\|_{2}^{2}
$$
网络结构很简单，双通道处理复数，有点类似 U-net，但是没有上下采样。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200321200326.png"/>

为了减少计算量，作者提出一种 hierarchical 的方式，从中心小部分的 kspace 开始，然后拓展到 full size。训练的图像越来越大，参数是不断继承的关系。参见算法1.

<img src= "https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200321200227.png" >

### Bibtex:

```latex
@inproceedings{zhang2019apir,
  title={APIR-Net: Autocalibrated Parallel Imaging Reconstruction Using a Neural Network},
  author={Zhang, Chaoping and Dubost, Florian and de Bruijne, Marleen and Klein, Stefan and Poot, Dirk HJ},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={36--46},
  year={2019},
  organization={Springer}
}
```

### 其他


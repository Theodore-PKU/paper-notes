### Paper:

k-Space Deep Learning for Parallel MRI: Application to Time-Resolved MR Angiography

### Author:

Eunju Cha, Eung Yeop Kim, and Jong Chul Ye

### Year:

2018

### Notes:

阅读日期：2020.01.13

精读。这篇文章解决的是动态多线圈MRI的成像问题。作者首先分析了传统采样和重建算法的问题在于过度利用了相邻帧的kspace的信息，而损失了时间上的分辨率。作者的做法则是利用深度学习，在减少相邻帧信息的情况下进行重建（通过恢复kspace系数）。

一般的采样方式 (a)：B表示高频部分，A表示低频部分，对每一帧都会采样A和B，其中高频部分是欠采样，但是多个帧的高频部分合起来可以构成完整的采样（相当于借用其他帧的kspace系数） 和作者使用的采样方式 (b)：只选取少数几帧，因此kspace是不完整的，作者提出的网络需要恢复缺失的 kspace 系数。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%888.06.27.png"/>

作者使用深度学习/神经网络来恢复kspace，其想法的逻辑是：kspace 的 Hankel matrices 具有低秩的特点，作者建立了深度学习和 Hankel matrices 低秩分解（这个在以前是用 ALOHA 算法求解，但是速度很慢）的一个联系（具体应该看参考论文[23]，这部分目前没懂）。因此，在低秩的目标下：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} &\min _{\widehat{\mathbf{Z}} \in \mathbb{C}^{N \times P}} \quad \text { RANK } \mathbb{H}_{d | P}(\hat{\mathbf{Z}})\\ &\text { subject to } \quad \mathcal{P}_{\Lambda}\left[\widehat{\mathbf{g}}_{i}\right]=\mathcal{P}_{\Lambda}\left[\widehat{\mathbf{z}}_{i}\right], \quad i=1, \cdots, P \end{aligned}"  border="0"/>

根据传统求解算法 ALOHA 以及深度学习得到：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{Z} \in \mathcal{H}^{0}} \sum_{i=1}^{P}\left\|g_{i} - \mathcal{F}^{-1}\left[\widehat{\mathbf{z}}_{i}\right]\right\|^{2}\text { subject to }  \mathcal{P}_{\Lambda}\left[\widehat{\mathbf{g}}_{i}\right]=\mathcal{P}_{\Lambda}\left[\widehat{\mathbf{z}}_{i}\right], \quad i=1, \cdots, P"  border="0"/>

最后的目标函数就是：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{\Psi}, \widetilde{\mathbf{\Psi}} \in \mathbb{R}^{2 d \times Q}} \sum_{i=1}^{P} \sum_{t=1}^{T}\left\|g_{i}^{(t)}-\mathcal{F}^{-1} \mathcal{K}\left(\widehat{\mathbf{y}}_{i}^{(t)} ; \mathbf{\Psi}, \widetilde{\mathbf{\Psi}}\right)\right\|^{2}"  border="0"/>

网络结构：使用了Harr小波分解作为pooling 和unpooling层。整体的结构仍然是 U-Net。需要注意的是输入应该是有缺失的kspace，使用了双通道来表示实部和虚部。16指的是线圈的数量。训练时，使用多帧的 GRAPPA 算法的结果作为 groundtruth，损失函数是恢复后的kspace重建出的图像和真实值的2范数 loss（似乎还有一个subtracted maximum intensity projection (MIP)，但不知道是什么。之所以这样做是因为没有 kspace 的groundtruth）。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%888.05.01.png"/>

另外，关于作者认为的神经网络以及 ReLU 激活函数起到的作用是在空间上增加某一种限制（也没懂）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-13%E4%B8%8B%E5%8D%888.04.28.png" width="50%"/>

### Bibtex:

```latex
@article{cha2018k,
  title={k-Space Deep Learning for Parallel MRI: Application to Time-Resolved MR Angiography},
  author={Cha, Eunju and Kim, Eung Yeop and Ye, Jong Chul},
  journal={arXiv preprint arXiv:1806.00806},
  year={2018}
}
```


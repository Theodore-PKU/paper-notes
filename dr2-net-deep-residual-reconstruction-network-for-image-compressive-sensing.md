### Paper:

DR$^2$ -Net: Deep Residual Reconstruction Network for Image Compressive Sensing

### Author:

Hantao Yao, Feng Dai, Dongming Zhang, Yike Ma, Shiliang Zhang, Yongdong Zhang

### Year:

2017

### Notes:

这篇文章的主要想法是用残差网络来学习重建信号时的误差（基于patch，也因此，在最后拼接得到图像后作者用BM3D去除block伪影）。作者的模型分为两个部分，第一个部分是全连接线性层，目的是将测量值变回图像域（这一种做法非常普遍）也就是初步重建的结果，第二个部分是将初步重建输入一个残差网络中，生成残差，用残差和初步重建相加得到最终的重建。（其中残差网络的最后一个block输出是否有把block的输入加上感觉不是特别明确）。训练上先训练线性层，再端到端训练。

网络结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-25%E4%B8%8B%E5%8D%888.47.36.png" style="zoom:67%;" />

训练方法：

首先训练线性层，$L\left(\left\{\mathbf{W}^{f}\right\}\right)=\frac{1}{N} \sum_{i=1}^{N}\left\|\mathbf{x}_{i}-\mathcal{F}^{f}\left(\mathbf{y}_{i}, \mathbf{W}^{f}\right)\right\|_{2}^{2}$，用这样的loss来训练，先得到一个较好的线性层。然后端到端训练线性层和残差网络 。
$$
\mathbf{x}_{i}^{*}=\mathcal{F}^{f}\left(\mathbf{y}_{i}, \mathbf{W}^{f}\right)+\mathcal{F}^{r}\left(\mathcal{F}^{f}\left(\mathbf{y}_{i}, \mathbf{W}^{f}\right), \mathbf{W}^{r}\right)\\
L\left(\left\{\mathbf{W}^{f}, \mathbf{W}^{r}\right\}\right)=\frac{1}{N} \sum_{i=1}^{N}\left\|\mathbf{x}_{i}-\mathbf{x}_{i}^{*}\right\|_{2}^{2}
$$
作者在文章中对于训练残差也提供了一种解释（并不是从流形维度更低的角度），作者认为残差接近于0，而网络的初始输出也接近于0，这样更好训练。

和其他的方法比较，主要的区别在于图像域之后接的网络，这里用了残差网络的形式（其实在很多CT，MRI的重建中非常常见），另外线性层单独训练可能起到了很好的效果（因为在论文中的实验结果上，只有线性层甚至比ReconNet还要好，而ReconNet本身已经包含有线性全连接层和卷积神经网络 ）。

> 思考：从代数上不能推断线性层处理得到的x和原来的真实信号之间的关系吗？

关于训练，作者使用了一个trick，对数据做放大或缩小后再选取patch。关于实验，作者用 standard 91 images 数据集来训练，和比较其他方法，也比较了残差网络的block的个数的影响，比较了时间开销等。

### Bibtex:

```latex
@article{yao2019dr2,
  title={Dr2-net: Deep residual reconstruction network for image compressive sensing},
  author={Yao, Hantao and Dai, Feng and Zhang, Shiliang and Zhang, Yongdong and Tian, Qi and Xu, Changsheng},
  journal={Neurocomputing},
  year={2019},
  publisher={Elsevier}
}
```
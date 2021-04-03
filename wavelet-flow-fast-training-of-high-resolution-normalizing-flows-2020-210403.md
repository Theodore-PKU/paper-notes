# Wavelet Flow: Fast Training of High Resolution Normalizing Flows

[TOC]

## 信息

Authors: Jason J. Yu, Konstantinos G. Derpanis and Marcus A. Brubaker

Year: NeurIPS 2020

Bibtex:

```
@article{yu2020wavelet,
  title={Wavelet Flow: Fast Training of High Resolution Normalizing Flows},
  author={Yu, Jason J and Derpanis, Konstantinos G and Brubaker, Marcus A},
  journal={arXiv preprint arXiv:2010.13821},
  year={2020}
}
```

cite: 2

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/wavelet-flow-fast-training-of-high-resolution-normalizing-flows.pdf)



## code

https://yorkucvil.github.io/Wavelet-Flow



## 概括

这篇文章是对 flow 模型的改进，主要的任务是生成图像。作者的思路是引入小波变换的形式，最终实现的效果是，生成效果和 glow 模型差不多，但是训练的速度快很多（15倍）。另外一个是这种方法可以实现生成高分辨率的图像。不过从实验结果上来看，生成质量还是很差的。

小波变换在这里的具体含义是说，要生成一个图像，是根据低分辨率的图像来生成高频部分的小波系数，然后和低分辨率图像通过小波逆变换合成出高分辨率的图像，然后通过逐级生成的方式最终得到结果。这里的 flow 模型刻画的是一个条件生成模型，条件是低分辨率图像，生成的内容是小波系数。计算概率的时候，利用条件概率的公式，实际上只要计算小波系数相对于某一分辨率的图像的概率，然后将这些概率连乘起来就可以了。而最后一级的小波变换，就只有图像（也就是最低分辨率，没有小波系数了）。同时，由于对给定的图像，实现可以先计算出所有层级的不同分辨率的图像，所以这些条件概率都是很容易估计的。

接下来要说明的就是这个条件 flow 模型是怎么构造的。实际上，采用了 glow 模型，但是由于这里有条件，作者把低分辨率的图像当作是另一部分输入。我的理解是，flow 模型总是要将一个隐变量映射到小波系数的分布上，此时参数网络的输入本来是隐变量，这里把低分辨率图像也加进来作为输入的一部分，以此实现条件 flow 模型。说实话，这种方式太过简单了，我认为效果差是显而易见的。

在训练方面，由于这种方法把图像的概率建模划分成了不同层次的小波系数的建模，所以每个条件 flow 模型都可以单独训练，这也许是一个不错的主意。虽然其他方法也可能会采取类似的条件生成，但如果低分辨率的图像不是已知的，计算起来就很慢，必须一步一步生成。



## 方法

这个小节对这篇文章的一些细节进行说明。

首先是模型的流程图：

![屏幕快照 2021-04-03 下午4.57.24](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-03 下午4.57.24.png)

从这个流程图中可以很清楚的看出来，实际上使用了多个 flow 模型。

本来计算概率应该是用
$$
\begin{equation}
p(\mathbf{I})=p(\mathcal{H}(\mathbf{I})) \mid \operatorname{det} \frac{\partial \mathcal{H}}{\partial \mathbf{I}}\mid
\end{equation}
$$
但作者控制了 $ \mid \operatorname{det} \frac{\partial \mathcal{H}}{\partial \mathbf{I}}\mid = 1$，因此计算概率的时候只要按照下式计算就可以了。
$$
\begin{equation}
p\left(\mathbf{I}_{n}\right)=p\left(\mathbf{I}_{0}\right) \prod_{i=0}^{n-1} p\left(\mathbf{D}_{i} \mid \mathbf{I}_{i}\right)
\end{equation}
$$
训练的损失函数就是：
$$
\begin{equation}
\log p(\mathbf{I})=\log p\left(\mathbf{I}_{0}\right)+\sum_{i=0}^{n-1} \log p\left(\mathbf{D}_{i} \mid \mathbf{I}_{i}\right)
\end{equation}
$$
采样的时候，先从 $\begin{equation}
\mathbf{I}_{0} \sim p\left(\mathbf{I}_{0}\right)
\end{equation}$ 开始，然后逐级计算：
$$
\begin{equation}
\mathbf{D}_{i-1} \sim p\left(\mathbf{D}_{i-1} \mid \mathbf{I}_{i-1}\right) \text { and } \mathbf{I}_{i}=h^{-1}\left(\mathbf{I}_{i-1}, \mathbf{D}_{i-1}\right)
\end{equation}
$$
作者在文章中提到了温度的问题。作者说很多方法会在生成的时候用温度低一些，在计算指标的时候温度高一些。不同温度的模型，表达能力是有区别的。作者说为了控制温度，在 sampling 的时候使用了 MCMC 算法。但是我没有理解为什么要这么做。

网络结构的改进：基本模型是 glow，但是 squeeze/split 结构去掉了，因为这篇文章已经用了小波变换的框架实现了变量的分离。

训练：作者说在最高分辨率的时候用了 patch-wise 的训练，无法理解。

数据集：ImageNet，LSUN，FFHQ 1024，CelebA-HQ 等。

实验结果：效果一般。

另外，作者提到，这个模型同时可以用来做超分辨生成。（如果这么说 srflow 模型不是可以拿来当作一个生成的模型的基本骨架吗）这一点直觉上没错，超分辨和生成本身可以看成一回事。

因为作者使用了人脸数据集，所以也提到了图像中的长距离关系。这个问题我觉得在所有生成模型中都要考虑。



## 参考文献

这篇文章提到了不少参考文献。

这些是其他领域的：

Ishaan Gulrajani, Kundan Kumar, Faruk Ahmed, Adrien Ali Taiga, Francesco Visin, David Vazquez, and Aaron Courville. PixelVAE: A latent variable model for natural images. In Proceedings of the International Conference on Learning Representations (ICLR), 2017.

Scott E. Reed, Aäron van den Oord, Nal Kalchbrenner, Sergio Gomez Colmenarejo, Ziyu Wang, Yutian Chen, Dan Belov, and Nando de Freitas. Parallel multiscale autoregressive density estimation. In Proceedings of the International Conference on Machine Learning (ICML), pages 2912–2921, 2017.

Ting-Chun Wang, Ming-Yu Liu, Jun-Yan Zhu, Andrew Tao, Jan Kautz, and Bryan Catanzaro. High-resolution image synthesis and semantic manipulation with conditional GANs. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR), pages 8798–8807, 2018.

Han Zhang, Tao Xu, and Hongsheng Li. StackGAN: Text to photo-realistic image synthesis with stacked generative adversarial networks. In Proceedings of the International Conference on Computer Vision (ICCV), pages 5908–5916, 2017.

Han Zhang, Tao Xu, Hongsheng Li, Shaoting Zhang, Xiaogang Wang, Xiaolei Huang, and Dimitris N. Metaxas. StackGAN++: Realistic image synthesis with stacked generative adversarial networks. IEEE Transactions on Pattern Analysis and Machine Intelligence (PAMI), 41(8): 1947–1962, 2019.

flow 模型：

Ivan Kobyzev, Simon Prince, and Marcus Brubaker. Normalizing ﬂows: An introduction and review of current methods. IEEE Transactions on Pattern Analysis and Machine Intelligence (PAMI), 2020. 这篇是一个综述。

Emiel Hoogeboom, Taco S. Cohen, and Jakub M. Tomczak. Learning discrete distributions by dequantization. arXiv preprint, arXiv:2001.11235, 2020.

Priyank Jaini, Kira A. Selby, and Yaoliang Yu. Sum-of-squares polynomial ﬂow. In Proceedings of the International Conference on Machine Learning (ICML), 5 2019.

Xuezhe Ma, Xiang Kong, Shanghang Zhang, and Eduard Hovy. MaCow: Masked convolutional generative ﬂow. In Neural Information Processing Systems (NeurIPS), pages 5891–5900, 2019.

George Papamakarios, Eric Nalisnick, Danilo Jimenez Rezende, Shakir Mohamed, and Balaji Lakshminarayanan. Normalizing ﬂows for probabilistic modeling and inference. arXiv preprint, arXiv:1912.02762, 2019.

Christina Winkler, Daniel Worrall, Emiel Hoogeboom, and Max Welling. Learning likelihoods with conditional normalizing ﬂows. arXiv preprint arXiv:1912.00042, 2019.
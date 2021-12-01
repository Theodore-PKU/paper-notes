# Robust Compressed Sensing MRI with Deep Generative Priors

[TOC]

## 信息

作者：Ajil Jalal, Marius Arvinte, Giannis Daras, Eric Price, Alexandros G. Dimakis, Jonathan I. Tamir

年份：2021

期刊会议：NeurIPS 2021

Bibtex：

```latex
@inproceedings{jalal2021robust,
  title={Robust compressed sensing mri with deep generative priors},
  author={Jalal, Ajil and Arvinte, Marius and Daras, Giannis and Price, Eric and Dimakis, Alex and Tamir, Jonathan},
  booktitle={Thirty-Fifth Conference on Neural Information Processing Systems},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/robust-compressed-sensing-mri-with-deep-generative-priors.pdf)

code：https://github.com/utcsilab/csgm-mri-langevin. 重要！



## 概括

精读。

这篇文章虽然中了 NeurIPS 2021，但是说起来方法并不复杂。这篇文章题目虽然叫 robust 重建，但实际上就是用 score-based 生成模型先训练生成 mri 图像，然后利用朗之万动态采样的方法来实现后验分布的采样。这里，后验分布作者也是分解成两个部分，这个做法和我们的想法完全一致。但是，非生成模型的部分，我认为公式存在问题，作者采用的是和 2020 年的一篇文章相同的思路。虽然作者最后在实验中的方法中增加了一个小的技巧，这个技巧某种意义上似乎等价于我们推导的结果。但由于他们使用的不是 ddpm 而是 score-based diffusion process，所以推导可能还是有一些差距。而且他们的采样方法是朗之万动态，这个采样本身就和 ddpm 的不同。

作者之所以称之为 robust，是因为这种做法对于重建的 sampling pattern 不敏感，而且作者发现重建其他的 mri 图像，效果也不错。因此称之为 robust 的方法。作者的比较试验比较多，实验结果我觉得也很符合预期。实验的数据主要是 fastMRI 的 brain 数据，考虑的是多线圈的重建，更多细节参考方法和实验的部分。

这篇文章非常值得借鉴，特别是实验部分。而且从作者的描述来看，他们应该是复现了比较对象。看看他们的代码。



## 方法

关于 mri 图像的 ground truth，作者选择了 MVUE。

RSS 的估计，没有用敏感矩阵：
$$
\hat{x}_{\mathrm{RSS}}=\sqrt{\sum_{i=1}^{N_{c}}\left|\left(F^{H} y_{i}\right)\right|^{2}}
$$
MVUE 的估计，敏感矩阵包含在 A 中。
$$
\underset{x}{\arg \min }\|y-A x\|_{2}^{2}
$$
扩散过程采用的是 Song Yang 等人的 VP 型。

朗之万动态的核心是：
$$
x_{t+1} \leftarrow x_{t}+\eta_{t} \nabla_{x_{t}} \log \mu\left(x_{t} \mid y\right)+\sqrt{2 \eta_{t}} \zeta_{t}, \quad \zeta_{t} \sim \mathcal{N}(0,1)
$$
$\nabla_{x_{t}} \log \mu\left(x_{t} \mid y\right)$ 分解成两个部分，一个用无条件生成模型估计。另外一个部分
$$
\nabla_{x_{t}} \log \mu\left(y \mid x_{t}\right)=\frac{A^{H}\left(y-A x_{t}\right)}{\sigma^{2}}
$$
这里的 $\sigma^{2}$ 是测量的高斯噪声。但是这个式子其实是有问题的。不过作者实际采用了 annealing 的版本。
$$
\nabla_{x_{t}} \log \mu\left(x_{t} \mid y\right)=f\left(x_{t} ; \beta_{t}\right)+\frac{A^{H}\left(y-A x_{t}\right)}{\sigma^{2}+\gamma_{t}^{2}}
$$
这样就实现采样了。实际上这就相当于每次只采样一步。

因为是 uncertainty 的方法，所以也可以估计不确定性，不过作者似乎没有把这个作为问题的重心，而是考虑鲁棒性。

最后有一个，作者在论文里搞了一套理论，完全不想看这个部分。



## 实验

作者比较的方法有：小波 1 范数约束的 CS 重建，MoDL，E2E-Varnet，ConvDecoder。其中 MoDL 和 E2E-Varnet 是监督训练，ConvDecoder 我感觉是一个无监督的方法（无监督还怎么比？作者在论文里也没有说）。

作者的实验思路是这样的：

1. 先给定加速程度和 sampling pattern 训练一个重建模型。只有监督学习会有影响。
2. 在不同的加速程度上进行测试。比较差异。
3. 在不同的 sampling pattern 上比较差异。
4. 对非训练类型的 mri 图像进行重建。比较差异。
5. 比较的时候用实值的结果。

比较的结果是：

1. CS 重建、MoDL、作者提出的方法，对于 sampling pattern 鲁棒。
2. CS 重建、MoDL 当加速程度上升的时候，效果变差较多。
3. MoDL 和 E2E-Varnet 只要在变动的数据上测试，效果都变得较差。
4. 作者提出的方法虽然加速程度上升的时候，指标变差了，但是图像质量还是很好。
5. 和训练相同的情形测试的时候，最好的仍然是监督学习的两种方法。
6. ConvDecoder 表现基本不太好。
7. 作者提出的方法比较慢，但是 ConvDecoder 更慢（论文里的数据太慢了，不知道为什么）

这些结论很符合预期。对于这种生成后验分布的方法，基本上都应该达到这样的效果。作者提出的方法鲁棒的原因，本质还是因为训练和重建无关。另外，E2E-Varnet 因为没有估计敏感矩阵，所以效果显得不太好。

不过作者并没有和其他的生成类方法进行比较。

关于实验的一些细节：

1. 训练的数据是 brain 的 fastMRI 多线圈数据。直接考虑了复数，作者也是直接生成复数图像的。大小似乎是 320x320
2. 采样方式和 fastMRI 保持一致（训练的时候）
3. 没有使用 RSS 的方式计算 ground truth，而是 MVUE，这个其实就是险用 ESPIRiT 估计出敏感矩阵之后，然后最小化所有线圈的 dc 得到的结果。RSS 忽略了敏感矩阵。
4. 数据量有 14539 slices
5. 重建 CS 用的是 BART toolbox，在训练集上找了最好的变分模型的参数 $\lambda$
6. 作者的 score-based 的网络是 NCSNv2，并不算特别先进。
7. 还有一些超参数，作者没有在论文中提到，他们说在代码里。



## 反思

作者的做法和我们的想法完全一致，只是我们考虑的是 ddpm 而不是 score-based diffusion，我们需要在使用 score-based model 的方法上多思考一些内容。目前还没有人直接做 kspace 域的，我认为这是一个非常好的机会。



## 参考文献

这篇文章的一些参考文献。

Mohammad Zalbagi Darestani, Akshay Chaudhari, and Reinhard Heckel. Measuring robustness in deep learning based compressive sensing. International Conference on Machine Learning, 2021. 这似乎是一篇无监督的方法。

Mathews Jacob, Jong Chul Ye, Leslie Ying, and Mariya Doneva. Computational mri: Compressive sensing and beyond [from the guest editors]. IEEE Signal Processing Magazine, 37(1):21–23, 2020. 一篇 review

Ajil Jalal, Sushrut Karmalkar, Alexandros G Dimakis, and Eric Price. Instance-optimal compressed sensing via posterior sampling. International Conference on Machine Learning, 2021. 这篇文章的采样方法就是朗之万动态？得看看。作者说用了一样的采样方法。

Varun A Kelkar, Sayantan Bhadra, and Mark A Anastasio. Compressible latent-space invertible networks for generative model-constrained image reconstruction. IEEE Transactions on Computational Imaging, 7:209–223, 2021. MRI 重建的方法。
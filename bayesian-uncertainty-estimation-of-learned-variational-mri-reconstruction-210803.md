# Bayesian Uncertainty Estimation of Learned Variational MRI Reconstruction

[TOC]

## 信息

Authors: Dominik Narnhofer, Alexander Eﬄand, Erich Kobler, Kerstin Hammernik, Florian Knoll, and Thomas Pock

Year: arXiv 2021

Bibtex:

```latex
@article{narnhofer2021bayesian,
  title={Bayesian Uncertainty Estimation of Learned Variational MRI Reconstruction},
  author={Narnhofer, Dominik and Effland, Alexander and Kobler, Erich and Hammernik, Kerstin and Knoll, Florian and Pock, Thomas},
  journal={arXiv preprint arXiv:2102.06665},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/bayesian-uncertainty-estimation-of-learned-variational-mri-reconstruction.pdf)



## 概括

作者认为存在两种不确定性，一种是 aleatoric，它和观测误差有关，另一种是 epistemic，它和模型有关。作者表示很多工作都是解决了 aleatoric 不确定性，很少解决第二种不确定性。作者认为 epistemic 不确定性可以用贝叶斯的方法解决。

作者采用的方法是基于 total deep variation TDV 的针对一般线性逆问题的方法。这个方法是将深度学习和变分模型相结合的方法。而且训练的结果具有竞争力。其次，作者利用 Bayes by Backprop framework, 可以进行贝叶斯推断，由此估计 epistemic 不确定性。具体来说，作者通过从一个多维正态分布中随机抽样出 TDV 的参数，由此可以得到不同的重建结果，并计算均值和方差（像素级别）



## 方法

训练方法：sampled optimal control problem for deterministic and stochastic MRI reconstruction？

首先，作者描述了 TDV 的求解模型。这个模型我认为看原来的论文更清楚（参考 Alexander Eﬄand, Erich Kobler, Karl Kunisch, and Thomas Pock. Variational networks: an optimal control approach to early stopping variational methods for image restoration. J. Math. Imaging Vision, 62(3):396–416, 2020. Erich Kobler, Alexander Eﬄand, Karl Kunisch, and Thomas Pock. Total deep variation for linear inverse problems. In CVPR, 2020. 这几篇，其中还有鄂老师的文章，就是所谓的 optimal control problem 的训练方法）。简单来说，就是将变分模型的正则项用一个和神经网络相关的（U-Net 结构）模型来表示，通过迭代算法（proximal 类似的算法，这个我没有细看）从初始值得到最后的 MAP 值，然后借助 optimal control 的训练方法训练模型参数和迭代过程的超参数。损失函数是
$$
J(y, z, T, \theta)=\left\|X_{S}(z, T, \theta)-Y\right\|_{1}+\tau\left(1-\operatorname{SSIM}\left(X_{S}(z, T, \theta), Y\right)\right)
$$
我个人认为这个部分不是这篇文章的关键，所以跳过细节。

epistemic 不确定性的估计。作者的表述是：we estimate the epistemic uncertainty of the previous deterministic model by sampling the weights from a learned probability distribution. 这里的关键就是这个 learned probability distribution 是什么。作者设为一个高斯分布，那么参数就是均值和协方差。协方差矩阵用 $LL^T$ 表示，L 是下三角矩阵（为了减少参数）。这个高斯分布是用来采样网络参数 $\theta$ 的。作者的做法就是其他的模型参数（T，K0）等都是固定的，只有 theta 是变动的。然后用原来训练模型的损失函数来训练均值和协方差。但是直接训练，会导致协方差变成 null matrix。所以作者增加了一个 KL 项。对于高斯分布，两个分布的 KL 散度计算是：
$$
\mathrm{KL}\left(p_{1} \| p_{2}\right)=\frac{1}{2}\left(\log \frac{\left|\Sigma_{2}\right|}{\left|\Sigma_{1}\right|}+\operatorname{tr}\left(\Sigma_{2}^{-1} \Sigma_{1}\right)+\left(\mu_{2}-\mu_{1}\right)^{\top} \Sigma_{2}^{-1}\left(\mu_{2}-\mu_{1}\right)-d\right)
$$
实际上，均值几乎没有影响，应该就是原来模型训练的结果。作者这里给的 p2 是 $\mathcal{N}\left(\mu, \alpha^{-1} \mathrm{Id}\right)$，其实就是估计一个好的协方差矩阵就行了，这里的 alpha 是一个事先给定的常数。如果 optimal control 训练的时候，就直接用 sample 的 theta 来算损失函数，那么 mu 其实就会和原来不使用 KL 散度不使用贝叶斯训练的结果差不多。

从论文的表述来看，最后的目标函数应该是
$$
\begin{gathered}
\inf \left(\mathbb{E}_{\theta \sim \mathcal{N}\left(\mu, L L^{\top}\right)}\left[\frac{1}{I} \sum_{i=1}^{I} J\left(y^{i}, z^{i}, T, \theta\right)\right]+\beta\left(\alpha \operatorname{tr}\left(L L^{\top}\right)-\log \left(\operatorname{det}\left(L L^{\top}\right)\right)\right):\right. \\
\left.T \in \mathbb{R}_{0}^{+}, \mu \in \Theta, L \in \mathbb{R}^{p \times p} \text { with } \operatorname{det}(L) \neq 0\right)
\end{gathered}
$$
也就是说前面的模型和贝叶斯的框架是合在一起训练的（即取最小化）。这里可以分为两项，第一项是 J，第二项是 f。beta 用来控制。作者的训练方法参考了Antonin Chambolle and Thomas Pock. An introduction to continuous optimization for imaging. Acta Numer., 25:161–319, 2016. 具体来说，就是 to increase stability we employ a semi-implicit gradient descent-based scheme in (8) for the optimization of L with an implicit step on f, which is equivalent to an update with a proximal map on f. 这个关于 f 的更新步骤是给 proximal 计算。作者给出了具体的计算结果（命题1）

![屏幕快照 2021-08-03 下午9.10.59](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-03 下午9.10.59.png)

最终的 update scheme 是：
$$
\left(\begin{array}{c}
T^{k+1} \\
\mu^{k+1} \\
L^{k+1}
\end{array}\right)=\left(\begin{array}{c}
T^{k}-h_{T}^{k} \nabla_{T} \mathcal{J}\left(T^{k}, \mu^{k}, L^{k}\right) \\
\mu^{k}-h_{\mu}^{k} \nabla_{\mu} \mathcal{J}\left(T^{k}, \mu^{k}, L^{k}\right) \\
\operatorname{prox}_{\tau h_{L}^{k} f}\left(L^{k}-h_{L}^{k} \nabla_{L} \mathcal{J}\left(T^{k}, \mu^{k}, L^{k}\right)\right)
\end{array}\right)
$$
（所以在这篇文章中，作者既介绍了不使用贝叶斯的 TDV 方法，也介绍了使用贝叶斯的 TDV 方法）

具体的贝叶斯计算的方法在概括中说明的比较清楚了（因为挺简单的）。这个图也很清晰地解释了整个推断的过程。

![屏幕快照 2021-08-03 下午8.22.33](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-03 下午8.22.33.png)



## 实验

训练细节详见 2.4 节。

2.5 节给出了推断的具体计算过程（我感觉挺简单的）

为了定义估计的协方差的熵的大小，作者给出了一个计算方法。我认为这个指标可以看成是对 epistemic 不确定性的一种度量，不同的参数训练后的模型，这个不确定性是不同的。作者用不同的 beta 训练处不同的模型，计算了他们的熵和 PSNR 指标的关系。如果贝叶斯的协方差越大，那么 PSNR 就越差。但是这里没有考虑 alpha 的值的影响我觉得非常奇怪。

![屏幕快照 2021-08-03 下午9.22.54](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-03 下午9.22.54.png)

实验结果方面，作者用 TDV 的模型和其他几个模型进行了对比，这里我不太确定是 TDV 的随机模型使用的是否是均值。

![屏幕快照 2021-08-03 下午9.26.21](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-08-03 下午9.26.21.png)

这里的 U-Net 是 fastMRI 数据集给出的结果。

另外两个比较的模型是：Kerstin Hammernik, Jo Schlemper, Chen Qin, Jinming Duan, Ronald M. Summers, and Daniel Rueckert. Σ-net: Systematic evaluation of iterative deep neural networks for fast parallel MR image reconstruction. arXiv, 2019. 和 Patrick Putzky, Dimitrios Karkalousos, Jonas Teuwen, Nikita Miriakov, Bart Bakker, Matthan Caan, and Max Welling. i-RIM applied to the fastMRI challenge. arXiv, 2020

我个人觉得这个结果其实挺符合实际的，考虑了随机性以后效果都会变差，但是只用 PSNR 真的是最好的吗？这仍然是重建一个结果，而不是重建分布。



## 参考文献

Florian Knoll, Kerstin Hammernik, Chi Zhang, Steen Moeller, Thomas Pock, Daniel K. Sodickson, and Mehmet Akçakaya. Deep learning methods for parallel magnetic resonance image reconstruction. IEEE Signal Process. Mag., 37(1):128–140, 2020.

Alexander Selvikvåg Lundervold and Arvid Lundervold. An overview of deep learning in medical imaging focusing on MRI. Zeitschrift für Medizinische Physik, 29(2):102–127, 2019.

这两篇参考文献是综述，不知道我之前看过没有，这里做一个记录。

Kerstin Hammernik, Teresa Klatzer, Erich Kobler, Michael P. Recht, Daniel K. Sodickson, Thomas Pock, and Florian Knoll. Learning a variational network for reconstruction of accelerated MRI data. Magn. Reson. Med., 79(6):3055–3071, 2018.

Alexander Eﬄand, Erich Kobler, Karl Kunisch, and Thomas Pock. Variational networks: an optimal control approach to early stopping variational methods for image restoration. J. Math. Imaging Vision, 62(3):396–416, 2020.

Erich Kobler, Alexander Eﬄand, Karl Kunisch, and Thomas Pock. Total deep variation for linear inverse problems. In CVPR, 2020.

这三篇文章是作者认为将深度学习和变分方法结婚的工作。regularizer is learned from data.

Erich Kobler, Alexander Eﬄand, Karl Kunisch, and Thomas Pock. Total deep variation: A stable regularizer for inverse problems. arXiv, 2020.

这篇是相关的一个工作。

Charles Blundell, Julien Cornebise, Koray Kavukcuoglu, and Daan Wierstra. uncertainty in neural networks. In ICML, volume 37, pages 1613–1622, 2015.

Yarin Gal and Zoubin Ghahramani. Dropout as a Bayesian approximation: Representing model uncertainty in deep learning. In ICML, pages 1050–1059, 2016.

Alex Kendall and Yarin Gal. What uncertainties do we need in Bayesian deep learning for computer vision? In NIPS, pages 5574–5584. Curran Associates, Inc., 2017.

Florian Wenzel, Kevin Roth, Bastiaan S. Veeling, Jakub Światkowski, Linh Tran, Stephan Mandt, Jasper Snoek, Tim Salimans, Rodolphe Jenatton, and Sebastian Nowozin. How good is the Bayes posterior in deep neural networks really? ICML, 2020.

这四篇文章是作者认为使用贝叶斯方法解决深度学习的 epistemic 不确定性的文章。不过它们都没有应用到 MRI 重建上。

Jo Schlemper, Daniel C. Castro, Wenjia Bai, Chen Qin, Ozan Oktay, Jinming Duan, Anthony N. Price, Jo Hajnal, and Daniel Rueckert. Bayesian deep learning for accelerated MR image reconstruction. In Florian Knoll, Andreas Maier, and Daniel Rueckert, editors, Machine Learning for Medical Image Reconstruction, pages 64–71. Springer International Publishing, 2018.

Vineet Edupuganti, M. Mardani, S. Vasanawala, and J. Pauly. Uncertainty quantiﬁcation in deep MRI reconstruction. IEEE Trans. Med. Imaging, 40:239–250, 2021.

这两篇文章是 MRI 重建中估计不确定性的文章，但是这两篇文章的不确定性和 epistemic 的不确定性是一样的吗？特别是后者，我们度过这篇文章，我认为有很大的差异。

Florian Knoll, Tullie Murrell, Anuroop Sriram, Naﬁssa Yakubova, Jure Zbontar, Michael Rabbat, Aaron Defazio, Matthew J. Muckley, Daniel K. Sodickson, C. Lawrence Zitnick, and Michael P. Recht. Advancing machine learning for MR image reconstruction with an open competition: Overview of the 2019 fastMRI challenge. arXiv, 2020.

这篇文章似乎是 fastMRI 数据集的一个结果展示的文章。
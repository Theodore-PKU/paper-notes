### Paper:

LEARN: Learned Experts’ Assessment-Based Reconstruction Network for Sparse-Data CT

### Author:

Hu Chen, Yi Zhang, Yunjin Chen, Junfeng Zhang, Weihua Zhang, Huaiqiang Sun, Yang Lv, Peixi Liao, Jiliu Zhou, and Ge Wang

### Year:

2018

### Notes:

阅读日期：2020.01.10

泛读。这篇文章解决的是稀疏角CT成像，使用的方法啊和 [Learning a Variational Network for Reconstruction of Accelerated MRI Data](learning-a-variational-network-for-reconstruction-of-accelerated-mri-data.md) 这篇文章其实很像，都是对目标函数的梯度迭代法的展开，只不过作者并不是真的使用了 fields of expert 的正则项的形式，使用的是一个3层的卷积网络。作者认为他们的效果好的原因在于参数都是可学习的，但比较的方法似乎都比较旧，所以感觉说服力不强。

作者在推导时，使用了 fields of experts 的正则项。即：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}x &=\arg \min _{x} E(x) \\&=\arg \min _{x} \frac{\lambda}{2}\|A x-y\|_{2}^{2}+\sum_{k=1}^{K} \phi_{k}\left(G_{k} x\right), \quad \text { s.t. } x \geq 0\end{aligned}"  border="0"/>

对应的梯度迭代法为：

<img src="http://latex.codecogs.com/svg.latex? x^{t+1}=x^{t}-\left(\lambda^{t} A^{T}\left(A x^{t}-y\right)+\sum_{k=1}^{K}\left(G_{k}^{t}\right)^{T} \gamma_{k}^{t}\left(G_{k}^{t} x^{t}\right)\right)"/>

其中<img src="http://latex.codecogs.com/svg.latex? \gamma_k^t"/>表示<img src="http://latex.codecogs.com/svg.latex? \phi_k"/>的一阶梯度。

这种迭代法展开后就变成类似残差结构的网络。作者在论文中说为了增加灵活性，实际用三层卷积网络来替代<img src="http://latex.codecogs.com/svg.latex? \sum_{k=1}^{K}\left(G_{k}^{t}\right)^{T} \gamma_{k}^{t}\left(G_{k}^{t} x^{t}\right)">。网络的输入是用FBP或IR算法得到的初始值。网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-10%E4%B8%8B%E5%8D%882.16.36.png" width="70%"/>

作者表示，因为有 data fidelity 项，所以梯度计算和普通的 CNN 不一样，没仔细推过，不清楚有啥区别。实验方面，作者的实验内容还是比较丰富的，比较了不同的方法（ASD-POCS，Dual-DL，PWLS-TGV，GammaReg，FBPConvNet，但我感觉挺旧的方法 ），不同的超参数的结果，不同的views 数量。

### Bibtex:

```latex
@article{chen2018learn,
  title={LEARN: Learned experts’ assessment-based reconstruction network for sparse-data CT},
  author={Chen, Hu and Zhang, Yi and Chen, Yunjin and Zhang, Junfeng and Zhang, Weihua and Sun, Huaiqiang and Lv, Yang and Liao, Peixi and Zhou, Jiliu and Wang, Ge},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1333--1347},
  year={2018},
  publisher={IEEE}
}
```


# AlignFlow: Cycle Consistent Learning from Multiple Domains via Normalizing Flows

[TOC]

## 信息

Authors: Aditya Grover, Christopher Chute, Rui Shu, Zhangjie Cao, Stefano Ermon

Year: AAAI 2020

Bibtex:

```
@inproceedings{grover2020alignflow,
  title={Alignflow: Cycle consistent learning from multiple domains via normalizing flows},
  author={Grover, Aditya and Chute, Christopher and Shu, Rui and Cao, Zhangjie and Ermon, Stefano},
  booktitle={Proceedings of the AAAI Conference on Artificial Intelligence},
  volume={34},
  number={04},
  pages={4028--4035},
  year={2020}
}
```

cite: 11



## 概括

这篇文章的想法非常简单，用 flow 模型将不同 domain 的信号映射到同一个隐变量空间中，提出了一个叫 AlignFlow 的模型。由于假设隐变量空间相同，那么 domain A 的信号通过 flow 模型映射到隐变量 Z 后，必须通过另一个 flow 模型映射到 domain B 中的信号。由于 flow 模型是可以结合对抗 loss 的训练，所以作者的损失函数包含四项，分别是 A-B 转换的判别、B-A 转换的判别，A 生成、B 生成。这种想法不仅可以用于两个 domain 的转换，也可以用于多个 domain 的转换。在这里，似乎没有看到监督信息？假如没有 pair data，那么这种训练似乎也是合理的，但如果有 pair data，我觉得有监督 loss 似乎更合理。不过由于这篇文章仅仅是应用，而且对于 flow 模型本身没有任何改进，所以并没有仔细看。

个人认为，这篇文章的本质思想还是隐变量表示信号的思想，这种想法在越来越多的应用中体现出来。



## 方法

这里仅仅给出损失函数和示意图。
$$
\begin{array}{l}
\mathcal{L}_{\text {AlignFlow }}\left(G_{\mathrm{B} \rightarrow \mathrm{A}}, C_{\mathrm{A}}, C_{\mathrm{B}} ; \lambda_{\mathrm{A}}, \lambda_{\mathrm{B}}\right) \\
=\mathcal{L}_{\mathrm{GAN}}\left(C_{\mathrm{A}}, G_{\mathrm{B} \rightarrow \mathrm{A}}\right)+\mathcal{L}_{\mathrm{GAN}}\left(C_{\mathrm{B}}, G_{\mathrm{A} \rightarrow \mathrm{B}}\right) \\
-\lambda_{\mathrm{A}} \mathcal{L}_{\mathrm{MLE}}\left(G_{\mathrm{Z} \rightarrow \mathrm{A}}\right)-\lambda_{\mathrm{B}} \mathcal{L}_{\mathrm{MLE}}\left(G_{\mathrm{Z} \rightarrow \mathrm{B}}\right)
\end{array}
$$
<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-04-09 下午10.06.03.png" alt="屏幕快照 2021-04-09 下午10.06.03" style="zoom:50%;" />

另外，在 cycleGAN 中，所谓的一致性在 flow 模型中由于可逆性已经被完美地解决了。


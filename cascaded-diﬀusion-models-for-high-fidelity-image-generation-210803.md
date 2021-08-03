# Cascaded Diﬀusion Models for High Fidelity Image Generation

[TOC]

## 信息

Authors: Jonathan Ho, Chitwan Saharia, William Chan, David J. Fleet, Mohammad Norouzi, Tim Salimans

Year: arXiv 2021

Bibtex:

```latex
@article{ho2021cascaded,
  title={Cascaded Diffusion Models for High Fidelity Image Generation},
  author={Ho, Jonathan and Saharia, Chitwan and Chan, William and Fleet, David J and Norouzi, Mohammad and Salimans, Tim},
  journal={arXiv preprint arXiv:2106.15282},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/cascaded-diﬀusion-models-for-high-fidelity-image-generation.pdf)



## 概括

这篇文章讲的是 cascaded 结构的扩散生成模型，用来生成高分辨率的高质量图像。基本的想法是通过一个标准的扩散模型生成低分辨率图像，然后用一个或多个超分辨扩散模型生成高分辨率图像。

这篇文章的模型主要用于类别生成的任务。整个生成过程是简单易懂的。这篇文章比较重要的是提出了 conditioning augmentation（因为 cascaded 的结构在其他文章里已经提到了），作者认为是非常有效的技巧来改善生成的结果。使用该技巧超越了 BigGAN-deep 和 VQ-VAE-2。

作者还展示如何高效训练网络、以及超参数的选择以达到最优的采样质量。



## 方法

关于扩散模型，基本上和此前的文章差不多，但是有一些细节感觉和原来的不太一样了，不知道这个部分是怎么处理的。

整个 cascaded 模型，每一个扩散模型都是条件扩散模型（因为这篇文章考虑的是类别生成）。但是不同阶段的条件是不同的，只有第一个生成模型用的条件是类别，其他超分辨用的都是低分辨率的图像。作者表示，这个条件必须作为逆扩散过程的一个输入。

网络结构方面基本采用了 DDPM 方法的常用做法。超分辨部分和 SR3 是一样的（因为就是用 SR3 作为超分辨的模型）。而类别的条件，是和扩散时间 t 一样的，通过增加 embedding 的方法加到网络的中间层去。这个方法应该是和 DDPM 的最早的方法一样。

cascaded 结构的好处大概可以总结为：1. 分解为不同的模型；2. 每个阶段的模型可以单独训练和调整，以达到最优的小姑。3. 将生成的主要难点集中于低分辨率的部分。



## conditioning augmentation

这个技巧概括起来就是对于超分辨模型的条件输出（低分辨率图像）增加一个数据增广的部分。这个增广可以是任意的形式，作者发现最有效最简单的就是在低分辨率的地方，增加高斯噪声，在高分辨率的地方，使用高斯模糊。

增加高斯噪声的部分，就是用 forward process 的噪声。

而在高斯模糊的部分，作者发现最有效的是 128x128 或者 256x256 的超分辨部分。作者使用的窗口大小是 3x3，sigma 是通过超参数搜索得到的一个范围。训练的时候在这个范围里随机采样一个，50% 的数据使用这种增广。推断的时候，不使用增广。

在低分辨率的地方使用高斯噪声，作者在论文中给出了的一种做法是生成低分辨率图像的时候并不是在 t = 0 的时候停止，而是在 t = s 的时候停止。为此，这个模型变得更复杂了，因为 s 也变成了一个参数，训练的过程也更复杂一些。另一种做法只是在 t = 0 的生成结果之上，重新增加 s 步的扩散来增加高斯噪声的部分。第二种做法更简单一些，而且对于 s 的超参数搜索也更简单。理论上两种做法应该差别很小，因为两个增广的数据的边缘分布应该是一样的。



## 实验

作者在第一个生成模型部分，加了一些改进。在后面的超分辨部分，实验了两种conditioning augmentation，在高分辨率的部分实验了高斯模糊增广的效果。

在实验中，作者最后是用 early stoping 的方式选择 FID （over 10k samples）最好的模型。最终的 FID 使用了 over 50k samples。计算 IS 值的时候，先生成 50k 样本，然后用分成十份来计算均值和方差（这是什么含义？作者说用的是 T. Salimans, I. Goodfellow, W. Zaremba, V. Cheung, A. Radford, and X. Chen. Improved techniques for training gans. In Advances in Neural Information Processing Systems, pages 2234–2242, 2016. 的方法）

关于 imagenet 数据集的预处理方法，采用的 BigGAN 的方式。

实验主要分为两类，一类是计算 FID 和 IS 的最终结果。一类是消融实验，因为这篇文章的技巧涉及很多参数的选择。

附录 B 给出了模型的很多超参数，可以参考。


# Unleashing Transformers: Parallel Token Prediction with Discrete Absorbing Diffusion for Fast High-Resolution Image Generation from Vector-Quantized Codes

[TOC]

## 信息

作者：Sam Bond-Taylor, Peter Hessey, Hiroshi Sasaki, Toby P. Breckon, Chris G. Willcocks

年份：2021

期刊平台：arXiv

Bibtex：暂无

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/unleashing-transformers-parallel-token-prediction-with-discrete-absorbing-diffusion-for-fast-high-resolution-image-generation-from-vector-quantized-codes.pdf)

code：https://github.com/samb-t/unleashing-transformers

这篇文章的参考文献挺多可以看的，特别是 vector-quantized 相关的文章，以及这篇文章所用到的技术。



## 概括

通读。

这篇文章结合了 vector-quantized 模型和 discrete diffusion model。前者是用来训练 codebook 和一个从 code 到图像的生成部分。后者是使用一个 Transformer 来生成 code array。codebook 的部分类似于 VQ-VAE，但是 code 的生成由 discrete diffusion model 的逆过程来控制。这个逆过程很特别，有点类似于自回归，但是不是逐一生成，而是同时生成很多个 patch 对应的 code。更多细节参考方法部分。

不过这篇文章有很多内容还不太明白。如果有机会，需要重新读一读。



## 方法

我们对这篇文章的方法的一些细节进行说明。

首先整体上来说，这篇文章的方法基于 vector-quantized 的模型。这种方法的经典应用是 VQ-VAE。简单来说，就是 code 由 codebook 构成。codebook 由很多可选的 code，任何一个图像可以用 codebook 的 code 来表示（也就是编码），同时通常有一个解码器来表示。生成则是从 code 来生成。不过这篇文章的方法在生成 code 部分，使用的是 discrete diffusion model。因此这包含了两种模型，训练也就分成了两步。第一步是 codebook 部分的训练，另一个部分则是 code 的生成。

codebook 的训练的基本内容应该和 vector-quantized 模型的方法差不多。这篇文章中，每个图像的 patch 对应一个 code。编码器将图像映射为一个 code 构成的 array。这里的 code 是通过和 codebook 中已有的 code 进行比较找到最接近的 code。解码器则将 code 映射成重建的图像。这个部分就是一个 CNN 的自编码器，只不过编码采用了 code 的形式。不过这个部分怎么训练我还是没有明白。因为 code 是找最接近的，并不能求导，如何训练网络没有搞清楚。作者在论文中写的损失函数是：
$$
\mathcal{L}_{\mathrm{VQ}}=\mathcal{L}_{\mathrm{rec}}+\left\|\operatorname{sg}[E(\boldsymbol{x})]-\boldsymbol{z}_{q}\right\|_{2}^{2}+\beta\left\|\operatorname{sg}\left[\boldsymbol{z}_{q}\right]-E(\boldsymbol{x})\right\|_{2}^{2}
$$
但是不明白 sg 是什么。另外，作者还用了对抗训练的方法，增加了一个判别器，使得生成质量更好。

仅仅训练 codebook 是不够的，因为生成的时候，是从 code 进行生成，需要先生成 code 的 array 才可以。这里作者考虑的是类似自回归的方法，但又有很多不同。自回归的方法是逐一生成 code，非常慢，不适合高分辨率的图像。作者使用的 discrete diffusion model，这和扩散模型有很大的关系，但是是离散的，不太明白具体的方法。不过从论文中的描述来看，作者的想法是通过逆扩散过程生成 code array。这里的扩散过程实际是一个增加 mask 的过程。mask 就是不断遮挡部分 code，直到所有的 code 都被 mask 挡住。逆过程就是从无到有，逐渐生成一些 code，然后预测其他 code，直到所有的 code 都生成完毕。这个过程和自回归是不同的，因为这个生成是同时生成多个区域，而不是逐一生成。当 code array 生成好之后，就可以利用解码器生成图像了。关于这个部分的训练，也不太明白，所以此处忽略。这个部分的模型用的是 Transformer 的模型。

另外，生成的时候，作者还考虑了如何生成更大的图像。作者的方法就是生成多个子区域，在预测 code 生成的时候，可以从不同的子区域来预测。

两步训练的过程，可以用下面的图来理解。

![屏幕快照 2021-12-01 上午12.16.15](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-12-01 上午12.16.15.png)

最后关于实验的部分，作者主要是进行了一些生成的实验，然后用一些指标来衡量。看起来效果还可以。

> 指标的相关参考文献
>
> Tuomas Kynk¨a¨anniemi, Tero Karras, Samuli Laine, Jaakko Lehtinen, and Timo Aila. Improved Precision and Recall Metric for Assessing Generative Models. In H. Wallach, H. Larochelle, A. Beygelzimer, F. d'Alch´e-Buc, E. Fox, and R. Garnett, editors, Advances in Neural Information Processing Systems, volume 32, 2019.
>
> Muhammad Ferjad Naeem, Seong Joon Oh, Youngjung Uh, Yunjey Choi, and Jaejun Yoo. Reliable Fidelity and Diversity Metrics for Generative Models. In International Conference on Machine Learning, pages 7176–7185, 2020.
>
> Mehdi SM Sajjadi, Olivier Bachem, Mario Lucic, Olivier Bousquet, and Sylvain Gelly. Assessing Generative Models via Precision and Recall. In Advances in Neural Information Processing Systems, volume 31, 2018.

这篇文章还有一个没有明白的是温度，不知道温度在生成过程中起到了什么作用，怎么控制的。



## 参考文献

Rewon Child. Very Deep VAEs Generalize Autoregressive Models and Can Outperform Them on Images. In International Conference on Learning Representations, 2021.

Rewon Child, Scott Gray, Alec Radford, and Ilya Sutskever. Generating Long Sequences with Sparse Transformers. arXiv preprint arXiv:1904.10509, 2019. 自回归的方法

Patrick Esser, Robin Rombach, and Bj¨orn Ommer. Taming Transformers for High-Resolution Image Synthesis. arXiv:2012.09841, 2021. 自回归的方法

Niki Parmar, Ashish Vaswani, Jakob Uszkoreit, Lukasz Kaiser, Noam Shazeer, Alexander Ku, and Dustin Tran. Image Transformer. In ICML, 2018. 自回归的方法

Yilun Du and Igor Mordatch. Implicit Generation and Generalization in Energy-Based Models. In Advances in Neural Information Processing Systems, volume 33, 2019. EBM 方法

Ali Razavi, Aaron van den Oord, and Oriol Vinyals. Generating Diverse High-Fidelity Images with VQ-VAE-2. NeurIPS 32, 2019.

Hiroshi Sasaki, Chris G Willcocks, and Toby P Breckon. UNIT-DDPM: UNpaired Image Translation with Denoising Diffusion Probabilistic Models. arXiv preprint arXiv:2104.05358, 2021.

Arash Vahdat, Karsten Kreis, and Jan Kautz. Scorebased Generative Modeling in Latent Space. arXiv preprint arXiv:2106.05931, 2021.

Aaron van den Oord, Oriol Vinyals, and koray kavukcuoglu. Neural Discrete Representation Learning. NeurIPS 30, 2017.

Zhisheng Xiao, Karsten Kreis, Jan Kautz, and Arash Vahdat. VAEBM: A Symbiosis between Variational Autoencoders and Energy-based Models. In International Conference on Learning Representations, 2021.
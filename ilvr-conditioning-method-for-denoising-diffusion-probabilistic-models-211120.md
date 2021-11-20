# ILVR: Conditioning Method for Denoising Diffusion Probabilistic Models

[TOC]

## 信息

作者：Jooyoung Choi, Sungwon Kim, Yonghyun Jeong, Youngjune Gwon, Sungroh Yoon

期刊会议：arXiv

年份：2021

Bibtex：

```latex
@article{choi2021ilvr,
  title={Ilvr: Conditioning method for denoising diffusion probabilistic models},
  author={Choi, Jooyoung and Kim, Sungwon and Jeong, Yonghyun and Gwon, Youngjune and Yoon, Sungroh},
  journal={arXiv preprint arXiv:2108.02938},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/ilvr-conditioning-method-for-denoising-diffusion-probabilistic-models.pdf)

code: https://github.com/rosinality/denoising-diffusion-pytorch 这个是 ddpm 的一个 pytorch 代码。https://github.com/mseitzer/pytorch-fid 这个是 FID 的一个代码。



## 概括

通读。

这篇文章提出的 ILVR 方法利用了一个训练好的 ddpm 生成模型，来解决一些条件生成的问题。这里的条件比较宽泛，比如 image translation（实际上可以考虑做域迁移），paint to image 之类的。作者的想法是，对于这些问题，source 图像和 target 图像具有相似的结构，但是细节是不同的。于是作者在 ddpm 模型的采样过程中，设计了一个投影步。投影的过程利用了图像的结构相似性。而这个相似性在论文中被称为高级语义信息。实际上，这个语义信息就是低分辨率的部分是相似的（作者的具体做法是低分辨率的上采样）。所以在生成 target 图像时，每次迭代都会替换过程图像的低分辨率部分（也就是高级语义信息），保持这个部分和噪声化的 source 图像的低分辨率上采样相等。凡是类似的图像生成任务都可以用这种方法解决。这篇论文实际上提出了一种框架，也就是投影框架，只要能够定义出两个域之间的相似性的条件，并且可以构造出一个投影的映射，就可以实现这一点。

从本质上来说，这种投影的方法和 MRI 重建的投影方法是一致的。



## 方法

由于是一个 learning free 的方法，所以实际上扩散过程的定义和 ddpm 一样。不过逆过程要考虑条件。在这篇文章中，这个条件用 $c$ 表示，它并不是图像，而是一个高级语义。因此，逆过程写成：
$$
\begin{aligned}
p_{\theta}\left(x_{0} \mid c\right) &=\int p_{\theta}\left(x_{0: T} \mid c\right) d x_{1: T} \\
p_{\theta}\left(x_{0: T} \mid c\right) &=p\left(x_{T}\right) \prod_{t=1}^{T} p_{\theta}\left(x_{t-1} \mid x_{t}, c\right)
\end{aligned}
$$
这篇文章的高级语义的定义是：
$$
\phi_{N}: \text{a linear low-pass filtering operation} \\
c: \phi_{N}(y)=\phi_{N}\left(x_{0}\right)
$$
$\phi_N$ 实际上就是一个下采样 + 上采样的算子。N 表示的是放大缩小的倍数。如果 N 越大，那么这个语义就越抽象，细节部分越少，N 越小则反之。

作者利用了如下的近似假设：
$$
p_{\theta}\left(x_{t-1} \mid x_{t}, c\right) \approx p_{\theta}\left(x_{t-1} \mid x_{t}, \phi_{N}\left(x_{t-1}\right)=\phi_{N}\left(y_{t-1}\right)\right)
$$
这里的 $y_{t-1}$ 是 source 图像，论文里叫 reference 图像，的高斯噪声化结果，也就是施加了扩散过程的结果。如何实现上式的采样呢？

作者的想法非常简单：
$$
\begin{aligned}
&x_{t-1}^{\prime} \sim p_{\theta}\left(x_{t-1}^{\prime} \mid x_{t}\right) \\
&x_{t-1}=\phi_{N}\left(y_{t-1}\right)+\left(I-\phi_{N}\right)\left(x_{t-1}^{\prime}\right)
\end{aligned}
$$
 即在每次迭代之后，将 reference 图像的 $\phi_N$ 结果替换进来，这是一个完全的 projection 过程。于是就得到了下面的采样算法：

![屏幕快照 2021-11-20 下午8.23.49](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午8.23.49.png)

这相当于在迭代的过程中，不断修正迭代的方向，使其在语义信息部分保持和 reference 图像的一致性。而且，我们还可以在不同的时刻进行控制，比如只有初始的部分控制，迭代后期不控制等等。

作者还分析了这种方法可以用于什么样的任务，以及 $N$、时间的影响。

![屏幕快照 2021-11-20 下午8.26.20](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午8.26.20.png)

这里的 $(T, k)$ 表示的就是在 $T$ 到 $t$ 时段使用这种投影。首先我们知道的是，只有 reference 图像的高级语义部分（$\phi_N$）和 ddpm 生成的图像的高级语义部分保持一致，这样才可以实现生成。不过这里生成的就不是 reference 类型了。（这一点似乎和域迁移相反，域迁移的问题往往是因为 target domain 的图片比较少，不过如果是实验的数据集，应该数量还是够的吧）

而 N、时刻的影响则是非常好懂的，我们可以用下图来理解：

![屏幕快照 2021-11-20 下午8.31.18](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午8.31.18.png)
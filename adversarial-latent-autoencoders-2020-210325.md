# Adversarial latent autoencoders

[TOC]

## 信息

Authors: Stanislav Pidhorskyi, Donald A. Adjeroh and Gianfranco Doretto

Year: CVPR 2020

Bibtex:

```
@inproceedings{pidhorskyi2020adversarial,
  title={Adversarial latent autoencoders},
  author={Pidhorskyi, Stanislav and Adjeroh, Donald A and Doretto, Gianfranco},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={14104--14113},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/adversarial-latent-autoencoders.pdf)



## code

https://github.com/podgorskiy/ALAE



## 概括

个人感觉这篇文章讲故事的能力极强。这篇文章的主旨是设计一个 AE 网络，使得它既有 GAN 的生成能力，也能学到 disentagled 表示。为了实现这一点，作者采用了 GAN 的生成器结构，并且抛弃了 VAE 的框架，把 latent space 定义为从先验分布 z 变动之后的 w。虽然作者称其为 ALAE，Adversarial Latent Autoencoder，但是仔细观察后会发现，其实这个网络结构就是 GAN + inverse GAN 的结构。只是 inverse GAN 的网络同时也是判别器的一部分。而 invser GAN 本身就类似于 Encoder，GAN 的生成器是 Decoder，这样这两者就构成了 AE 结构。具体来说，作者使用了 styleGAN 的生成器作为基础。styleGAN 的生成器可分解为两个部分，z 到 w 的映射，以及生成的主干网络。作者把这两个部分分离出来，分别记为 F 和 G，判别器也分成两个部分，分别记为 E 和 D，E 的作用就是将图像 x 映射到 w，它是一个卷积网络，输出则是表示 style 信息的 w，输出和 F 假设是相同的。所以可以认为 E 就是一个 inverse GAN 网络，只不过同时也是判别器的一部分。而判别器中的 D 则可以看作是对 w 的判别。

由于保持了 GAN 的部分，所以整体的训练、技巧都可以继续使用 GAN 的相关成果。至于 AE 部分，因为 inverse GAN 结构的存在，作者增加了一个损失函数是关于 w 的，即用于生成的 w 和生成后图像经过 E 得到 w 要接近。

另外，作者在 MNIST 数据集上的实验使用了更简单的 G 和 E 模型（MLP）。只有在高分辨率的数据集上使用 styleGAN 的模型。

由于 inverse GAN 结构的存在，可以有 latent variable manipulation 的操作。其实就是从可以混合从不同图像那里得到的 w。由于 AE 重建的其实是 w 的 style 信息，所以在这篇文章中提到的重建，并不是完全重建图像，而是保持 style，生成不同的图像。



## 方法

首先对 GAN 网络的生成器和判别器进行分解：
$$
\begin{equation}
\mathrm{G}=G \circ F, \quad \text { and } \quad \mathrm{D}=D \circ E
\end{equation}
$$
整体的网络结构和训练、生成过程：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-25 下午5.26.39.png" alt="屏幕快照 2021-03-25 下午5.26.39" style="zoom:50%;" />

损失函数由两部分组成，一个是正常的 GAN 训练的损失函数，还有一个是 $\Delta$，其形式是：
$$
\begin{equation}
\Delta(F \| E \circ G \circ F)=E_{p(z)}\left[\|F(z)-E \circ G \circ F(z)\|_{2}^{2}\right]
\end{equation}
$$
F 实际上和 styleGAN 的做法一样，就是一个 MLP 网络。而判别器中的 D 也是 MLP 网络（只不过只有三层）

E 和 G 的结构则是：

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-25 下午5.28.04.png" alt="屏幕快照 2021-03-25 下午5.28.04" style="zoom:50%;" />

可以看出来，E 就是一个 inverse GAN 网络，而 G 和 styleGAN 是完全一样的。这里需要说明的是 E 如何得到 w。这里涉及到 IN 和 C 的结构。通过 IN 层，可以获得各个通道的均值和方差 $\begin{equation}
\mu\left(y_{i}^{E}\right) \text { and } \sigma\left(y_{i}^{E}\right)
\end{equation}$，C 模块是一个线性层，具体的计算是：
$$
\begin{equation}
w=\sum_{i=1}^{N} C_{i}\left[\begin{array}{l}
\mu\left(y_{i}^{E}\right) \\
\sigma\left(y_{i}^{E}\right)
\end{array}\right]
\end{equation}
$$
图中的 style 就是均值和方差，这和 w 通过 A 得到 style 是相反的过程。

训练相对复杂，一共有三步，其中两步和 GAN 训练有关，还有一步是和 E G 有关。实际上，这里的 AE 可以看成是 $G\circ E$，是隐变量到隐变量的 AE，而不是图像到图像的 AE。这就导致重建的时候，无法做到完全重建图像，因为图像经过 E 得到的只有 style 的信息，而经过 G 生成的时候，收到 noise 的影响。由于判别器由 E 和 D 构成，E 的网络结构某种程度上限制了判别器，这很可能是生成效果从指标上比 styleGAN 差的原因。严格意义上说，E 不仅输出了 w，其主干部分的输出是和 D 相关的。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-03-25 下午5.40.57.png" alt="屏幕快照 2021-03-25 下午5.40.57" style="zoom:50%;" />

## 反思

如果这篇文章单独训练一个判别器，也许效果会更好。为什么一定要把 E 放在判别器中呢？如果把 E 单独作为一个网络，只要能够实现图像的 style 特征提取，似乎已经足够了。

和一般的 inverse GAN 不同的是，并不是映射到最初是的隐变量，而是 w，这一点我感觉有一点启发性。
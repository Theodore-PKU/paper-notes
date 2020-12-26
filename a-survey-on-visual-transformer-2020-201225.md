# A Survey on Visual Transformer

[TOC]

### 信息

![屏幕快照 2020-12-25 下午4.19.45](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-25 下午4.19.45.png)

arXiv 2020

[Paper](/Users/xieyutong/Documents/Research/PaperReading/Papers/a-survey-on-visual-transformer.pdf)



### 概括

这篇文章首先对 Transformer 模型进行了简单地说明，然后介绍了 NLP 领域以 Transformer 为基础的一些预训练模型。之后按照应用类别介绍 Transformer 结构的网络在 CV 领域的相关工作。



### 综述内容

#### 分类

##### iGPT

这个模型包含两个阶段，第一个阶段是预训练模型，第二个阶段是微调阶段。

预训练阶段使用了自回归模型和 BERT 类型的损失函数。这里的自回归模型就是一个像素的序列模型。在微调阶段，增加了一个分类的头。首先要训练一个概率损失函数。
$$
\begin{equation}
L_{A R}=\underset{x \sim X}{\mathbb{E}}[-\log p(x)]
\end{equation}
$$

$$
\begin{equation}
p(x)=\prod_{i=1}^{n} p\left(x_{\pi_{i}} \mid x_{\pi_{1}}, \cdots, x_{\pi_{i-1}}, \theta\right)
\end{equation}
$$

这里的 $\pi$ 的顺序就是一般的顺序。另外，作者还增加了一个 BERT 损失函数。这个 BERT 损失函数由一个 mask 构成，mask 表示序列里的一部分值是未知的。作者考虑了 0.15 概率的出现率。在对 mask 进行抽样后，计算损失函数：
$$
\begin{equation}
L_{B E R T}=\underset{x \sim X M}{\mathbb{E}} \underset{i \in M}{\mathbb{E}} \sum_{i \in M}\left[-\log p\left(x_{i} \mid x_{[1, n] \backslash M}\right)\right]
\end{equation}
$$
在预训练阶段选择 $L_{AR}$ 或 $L_{BERT}$ 其中的一个进行训练（我猜是随机的）。

使用了 GPT-2 的 transformer decoder block。在计算 AR loss 的时候，用一个上三角 mask 来保证 attention 和 condition 一致。

在微调的时候，通过 average pooling 操作得到特征向量，然后再接分类器。实际上 fine-tune 的时候，分类损失函数和 $L_{AR}$ 或 $L_{BERT}$ 是一起训练的。

##### ViT

![屏幕快照 2020-12-25 下午7.03.56](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-25 下午7.03.56.png)

这篇文章几乎采用了和 Transformer 一模一样的形式。中把图像分成 patch，然后 flatten 成向量构成序列。因为每个 patch 大小相同，所以向量的长度是固定，可以用一个线性投影变换到一个 D 维空间中（embedding）。在 patch embedding 中还增加了一个表示位置的信息。（他们也试了2d的位置信息，但是没有什么大的变化）。另外在这个模型中，只利用了 encoder 的部分，没有 decoder，因为 encoder 的输出通过一个 MLP head 直接得到分类结果了。

ViT 模型也分为预训练阶段和微调阶段。预训练阶段，encoder 之后接的是另外一个层（但是不知道损失函数是啥样的）。作者发现 fine-tune 的时候使用分辨率更高的图像效果会好。此时序列比预训练的时候更多，但也导致位置信息无效。所以在预训练的时候对位置信息做了额外的处理。

实验结果显示，如果预训练的数据越多，效果会越好，甚至可以达到分类的 SOTA 效果。

##### 总结

iGPT 模型和 ViT 模型，前者是生成模型和自监督模型的结合（由于生成模型是自回归，所以和 Transformer 有关系），后者利用 patch 构成序列。iGPT 的结果并不是很好，ViT 模型在数据很多的情况下效果不错，但是如何利用 patch 内部的信息以及 patch 直接的关系仍然是一个问题（但是这个不是用 transformer 解决吗？）

#### High-level Vision

##### Object Detection

neck-based：这种方法的 neck 指的是多尺度信息融合模块。有人提出了 Feature Pyramid Transformer (FPT) 模型，由三个 transformer 构成。

head-based：这部分没懂。

frame-based：这个方法的代表是 DERT 模型。首先用一个 CNN 提取特征，再加上位置信息得到 embedding。其他好几篇工作是 follow DERT 模型。

![屏幕快照 2020-12-25 下午7.28.15](/Users/xieyutong/Pictures/screenshot/屏幕快照 2020-12-25 下午7.28.15.png)

##### segmentation

没有啥特别的东西。有的方法是基于 DERT 模型。

#### Low-level Vision

这部分提到了 image transformer 模型，TTSR 模型，IPT 模型。这些模型最好仔细看论文。

#### 最后

在文章的后面两个主要章节，讨论了 self-attention 的一般形式和self-attention 机制在 CV 的应用，以及如何使模型更高效的尝试。self-attention 主要是为了解决 long-range 的问题，或者说是感受野的大小问题。



### 参考文献

这篇文章引用的论文很多，但是大部分应该是都不会看的。看其中几篇我认为有用的。

1. Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Łukasz Kaiser, and Illia Polosukhin. Attention is all you need. Advances in neural information processing systems, 30:5998–6008, 2017. Transformer 模型本身，需要更仔细地理解一下。
2. Hanting Chen, Yunhe Wang, Tianyu Guo, Chang Xu, Yiping Deng, Zhenhua Liu, Siwei Ma, Chunjing Xu, Chao Xu, and Wen Gao. Pre-trained image processing transformer. arXiv preprint arXiv:2012.00364, 2020. 这篇是 IPT 模型，我觉得需要仔细看看。因为涉及到很多 low-level 的任务。
3. Mark Chen, Alec Radford, Rewon Child, Jeffrey Wu, Heewoo Jun, David Luan, and Ilya Sutskever. Generative pretraining from pixels. In International Conference on Machine Learning, pages 1691–1703. PMLR, 2020 预训练是生成模型，虽然最后是分类任务。
4. Zi-Hang Jiang, Weihao Yu, Daquan Zhou, Yunpeng Chen, Jiashi Feng, and Shuicheng Yan. Convbert: Improving bert with span-based dynamic convolution. Advances in Neural Information Processing Systems, 33, 2020. self-attention 的改进使其更高效。
5. Niki Parmar, Ashish Vaswani, Jakob Uszkoreit, Łukasz Kaiser, Noam Shazeer, Alexander Ku, and Dustin Tran. Image transformer. 直接的应用，生成模型。
6. Fuzhi Yang, Huan Yang, Jianlong Fu, Hongtao Lu, and Baining Guo. Learning texture transformer network for image super-resolution. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 5791–5800, 2020. 用于超分辨的模型。
7. Nicolas Carion, Francisco Massa, Gabriel Synnaeve, Nicolas Usunier, Alexander Kirillov, and Sergey Zagoruyko. End-to-end object detection with transformers. In ECCV, 2020. 有很多 follow 的 DERT 模型



### 反思

Transformer 和 self-attention，两者之间的关系以及 Transformer 和 RNN 的关系，他们和 NLP 的关系；图像分布、为什么要用 self-attention 或者 transformer，它们在 CV 的作用是什么，这些问题都需要重新捋一捋。不过得先看看上述的这几篇参考文献。

近期的文献阅读的方向是需要思考的。除了 transformer 在 CV 的应用，最好也要看一下 GCN 的相关知识。
### Paper:

Joint CS-MRI Reconstruction and Segmentation with a Uniﬁed Deep Network

### Author:

Liyan Sun, Zhiwen Fan, Yue Huang, Xinghao Ding, John Paisley

### Year:

2018

### Notes:

阅读日期：2020.01.14

泛读。这篇文章解决的是MRI重建问题，作者的想法是结合分割任务，一起学习。总的来说，虽然作者的方法看起来和一般的多任务学习有一点区别，但个人感觉实际上就是多任务学习。

MRN（重建网络）是由多个自编码形式的block 和data fidelity 单元构成 cascade 的形式，还使用了 residual  shortcut 的形式。MSN 是一个 U-Net 结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-14%E4%B8%8A%E5%8D%889.30.34.png" width="50%"/>

两者结合起来构成下图的网络：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-14%E4%B8%8A%E5%8D%889.40.03.png"/>

训练方式。首先预训练 MRN 和 MSN 两个模型，MSN 用的是 full-sample data 训练。预训练结束之后，MSN 的 encoder 丢弃，只保留 encoder，并且接到 MRN 的每个 encoder 之后，重复N次。然后一起训练。分割网络的损失是 cross-entropy，重建网络的损失函数是图像 2 范数。fine-tuning 的时候两个损失函数加一起（cross-entropy 会有N项，取平均）。

问题：如何保证MRN训练的encoder和MSN的decoder 之间可用，如果继续训练的时候，MSN 的decoder 完全变化了，那就没有任何意义了。感觉和多任务没什么区别。关于这一点作者没有说明和讨论。因此这篇文章的参考意义不大。

### Bibtex:

```latex
@inproceedings{sun2019joint,
  title={Joint CS-MRI reconstruction and segmentation with a unified deep network},
  author={Sun, Liyan and Fan, Zhiwen and Ding, Xinghao and Huang, Yue and Paisley, John},
  booktitle={International Conference on Information Processing in Medical Imaging},
  pages={492--504},
  year={2019},
  organization={Springer}
}
```


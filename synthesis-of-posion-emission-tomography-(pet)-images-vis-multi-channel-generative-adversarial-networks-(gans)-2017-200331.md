### Paper:

Synthesis of Positron Emission Tomography (PET) Images via Multi-channel Generative Adversarial Networks (GANs)

### Author:

Lei Bi, Jinman Kim, Ashnil Kumar, Dagan Feng, and Michael Fulham

### Year:

2017

### Notes:

泛读。这篇文章研究的并不是 PET 重建，而是用对应的 CT 图像和分割的肿瘤 label 生成 PET 图像。方法很简单，就是将 label 和 CT 图像作为生成器的输入，输出是合成的 PET 图像。因为使用了 GAN，所以有对抗损失函数。除此之外，还有图像域的 1 范数 loss。没特别的参考价值。这篇文章提到之前有的做法输入没有同时使用 label 和 CT，一般是单独使用。

网络结构非常简单：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200331094415.png"/>

### Bibtex:

```latex
@incollection{bi2017synthesis,
  title={Synthesis of positron emission tomography (PET) images via multi-channel generative adversarial networks (GANs)},
  author={Bi, Lei and Kim, Jinman and Kumar, Ashnil and Feng, Dagan and Fulham, Michael},
  booktitle={molecular imaging, reconstruction and analysis of moving body organs, and stroke imaging and treatment},
  pages={43--51},
  year={2017},
  publisher={Springer}
}
```

### 其他


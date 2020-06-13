On the Expressive Power of Deep Neural Networks

Author: Maithra Raghu, Ben Poole, Jon Kleinberg, Surya Ganguli,  Jascha Sohl Dickstein

Year: 2017

Published in: ICML. 2017

```latex
@inproceedings{raghu2017expressive,
  title={On the expressive power of deep neural networks},
  author={Raghu, Maithra and Poole, Ben and Kleinberg, Jon and Ganguli, Surya and Dickstein, Jascha Sohl},
  booktitle={Proceedings of the 34th International Conference on Machine Learning-Volume 70},
  pages={2847--2854},
  year={2017},
  organization={JMLR. org}
}
```

Link: https://github.com/Theodore-PKU/paper-notes/blob/master/on-the-expressive-power-of-deep-neural-networks.md

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/on-the-expressive-power-of-deep-neural-networks.md



这篇文章也讨论激活 pattern 的数量，不过在此基础上还讨论了一个新的概念，trajectory，指的是输入空间的一条曲线。作者借这个概念和实验指出了一些事实。

定理1: 关于激活 pattern 的数量，对于 ReLU DNN (宽度为 $k$，输入是 $m$，层数是 $n$)，作者给出的上界是 $O(k^{mn})$。还包含 hard-tanh 的部分，但是这个激活函数不是我们研究的内容。

定理2: 输入空间的每个 region，ReLU 网络表示的函数限制在该区域上是凸函数。

> 我觉得这个分析可能非常有用。神经网络的优化也许不应该从优化损失函数入手，而是优化函数的拟合。每个样本点在所在的区域都是凸函数。

剩下的内容都是关于 trajectory 的讨论。第一个是长度，作者表示随着深度的增加，trajectory 映射后的长度在不断增加，而且变得越来越复杂。并且给出了一个关于长度的定理。另外 trajectory 会通过不同的 region，通过的 region 的数量也和网络结构存在一定的关系。最后还讨论了 trajectory 长度的变化和 BN 层的关系，认为 BN 层的效果是让 trajectory 的长度变化不会随着深度增加而继续增加，控制其长度。并借此提出替代 BN 层的正则化方法，即对 trajectory 的长度进行限制。

> 这些讨论虽然直观上合理，但是实际上感觉没什么用，也很难成为一个有用的分析工具。
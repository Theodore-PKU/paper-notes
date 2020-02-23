### Paper:

Deep Generative Adversarial Neural Networks for Compressive Sensing MRI

### Author:

Morteza Mardani, Enhao Gong, Joseph Y. Cheng , Shreyas S. Vasanawala, Greg Zaharchuk, Lei Xing, and John M. Pauly

### Year:

2018

### Notes:

这篇文章和 [Deep Generative Adversarial Networks for Compressed Sensing (GANCS) Automates MRI](deep-generative-adversarial-networks-for-compressed-sensing-(gancs)-automates-mri.md) 的作者是一样的，这两篇文章也基本相同，这一篇是18年的，发表出来了，所以应该算是修改的版本（题目也稍微改了）。因此这个笔记里只保留原来文章中没有提到的部分。

总结内容为：ResNet网络作为生成器，判别器比较普通。使用LSGAN的损失函数。生成部分的损失还包括kspace的loss，image的loss。另外重建的时候还融合了kspace信息，将生成的图像的傅立叶高频系数填补到空缺中得到最终结果。

相较于原来那篇文章，改动的地方是图像域的loss 可以是 1范数和 2范数的加权。对于训练细节，作者提到为了让训练更稳定，一开始先不加对抗loss，这样可以让生成器先得到一个比较好的分布 ，然后再逐步改变损失函数的权重。但是在作者的实验中，关于权重的变化和选取的说明不是很清楚，不知道到底是什么样的设定效果最好。事实上，在这样的模型中，GAN 只是起到一个对抗loss的作用，并不是决定性的因素。

整体的生成网络结构：<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%885.03.54.png" style="zoom:50%;" />

判别器结构：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8B%E5%8D%885.04.00.png" style="zoom:50%;" /> 

### Bibtex:

```latex
@article{mardani2018deep,
  title={Deep generative adversarial neural networks for compressive sensing MRI},
  author={Mardani, Morteza and Gong, Enhao and Cheng, Joseph Y and Vasanawala, Shreyas S and Zaharchuk, Greg and Xing, Lei and Pauly, John M},
  journal={IEEE transactions on medical imaging},
  volume={38},
  number={1},
  pages={167--179},
  year={2018},
  publisher={IEEE}
}
```
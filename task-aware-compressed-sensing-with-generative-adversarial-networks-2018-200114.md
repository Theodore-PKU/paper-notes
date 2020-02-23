### Paper:

Task-Aware Compressed Sensing with Generative Adversarial Networks

### Author:

Maya Kabkab, Pouya Samangouei, Rama Chellappa

### Year:

2018

### Notes:

阅读日期：2020.01.14

精读。这篇文章解决的是压缩感知重建的问题，思路沿袭了 Bora 的gan的思路，但是有两点改进。在 Bora 的方法中，GAN 的训练和压缩感知本身没有关系，可以独立地分离。作者希望在GAN训练时可以增加信号重建的task。作者的做法是，在训练GAN的时候也训练latent variable z。这是第一个改进，对应的算法就是在原来 GAN 的训练时，除了常规的 G、D 的训练，增加一个 z 的训练。第二个改进是要解决没有足够多的 non-compression 的数据（感觉就是真实的信号），作者的处理方法是把判别器作用在测量值上，而不是重建的信号（这种做法值得商榷，我感觉和 DIP 就很像了，个人认为这种做法实际不可行，作者的实验数据都是很简单的），实验结果上表现得不错。

算法1（对z的训练）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%888.56.21.png" width="50%"/>

算法2（另外的判别器）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-15%E4%B8%8A%E5%8D%888.56.40.png" width="50%"/>

> 这个想法也有一定道理，既然最终是在 z 上搜索，就没有必要要求 z 一定要满足高斯分布或保持和原来的初始分布一致不变，如果训练时通过对 z 的训练可以得到更好网络表达，输入域的概率分布变化了也没有关系。最后作者还做实验说明经过这种训练的 z 包含有更多的信息，可以用来分类。不过在分类的情况下，作者增加了一个loss，但是并不是我们所关心的，因此不讨论这部分。

### Bibtex:

```latex
@inproceedings{kabkab2018task,
  title={Task-aware compressed sensing with generative adversarial networks},
  author={Kabkab, Maya and Samangouei, Pouya and Chellappa, Rama},
  booktitle={Thirty-Second AAAI Conference on Artificial Intelligence},
  year={2018}
}
```


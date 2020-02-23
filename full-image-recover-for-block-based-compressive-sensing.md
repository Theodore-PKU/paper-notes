### Paper:

Full Image Recover for Block-based Compressive Sensing

### Author:

Xuemei Xi, Chenye Wang, Jiang Du, Guangming Shi

### Year:

2018

### Notes:

泛读。这篇文章内容没有什么新意。总结起来就是用卷积（stride比较大）作为测量矩阵，获得测量值（也是2维的）后用 deconv 卷积层，接多个residual 卷积层得到重建图像。训练是端到端的，损失函数为2范数。作者在论文中说自己的工作的亮点在于克服了 block based 的重建方法的 blocky error。但是这种做法太常见了。

网络结构示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%883.53.27.png" style="zoom:50%;" />

### Bibtex:

```latex
@inproceedings{xie2018full,
  title={Full image recover for block-based compressive sensing},
  author={Xie, Xuemei and Wang, Chenye and Du, Jiang and Shi, Guangming},
  booktitle={2018 IEEE International Conference on Multimedia and Expo (ICME)},
  pages={1--6},
  year={2018},
  organization={IEEE}
}
```
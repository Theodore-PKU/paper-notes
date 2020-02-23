### Paper:

Accelerating Magnetic Resonance Imaging via Deep Learning

### Author:

Shanshan Wang, Zhenghang Su,Leslie Ying,Xi Peng,Shun Zhu Feng Liang, Dagan Feng 5 and Dong Liang

### Year:

2016

### Notes:

泛读。这篇文章可能早期的在MRI重建中使用深度学习的论文。使用的方法比较简单，大致的做法是先训练一个网络，用这个网络作为一个正则项来重建。训练时损失函数为图像域的2范数。输入是zero-fill重建，输出为去伪影的结果。网络结构为3层卷积网络，使用了ReLU激活函数。

假设训练好的网络为 $C$，作者在论文中提供了三种重建方式：

1. 把 $C$ 作为正则项
   $$
   \underset{u}{\operatorname{argmin}}\left\{\left\|C\left(F^{H} f ; \hat{\Theta}\right)-u\right\|_{2}^{2}+\lambda\left\|f-F_{M} u\right\|_{2}^{2}\right\}
   $$
   

2. 增加其他正则项
   $$
   \underset{u}{\operatorname{argmin}}\left\{\left\|C\left(F^{H} f ; \hat{\Theta}\right)-u\right\|_{2}^{2}+\lambda\left\|f-F_{M} u\right\|_{2}^{2}+\beta \operatorname{Reg}(u)\right\}
   $$

3. 两阶段重建。先用网络重建 $C\left(F^{H} f ; \hat{\Theta}\right)$，然后用这个作为传统压缩感知算法的初值重建。

### Bibtex:

```latex
@inproceedings{wang2016accelerating,
  title={Accelerating magnetic resonance imaging via deep learning},
  author={Wang, Shanshan and Su, Zhenghang and Ying, Leslie and Peng, Xi and Zhu, Shun and Liang, Feng and Feng, Dagan and Liang, Dong},
  booktitle={2016 IEEE 13th International Symposium on Biomedical Imaging (ISBI)},
  pages={514--517},
  year={2016},
  organization={IEEE}
}
```
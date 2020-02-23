### Paper:

Deep-Learning-Based Multi-Modal Fusion for Fast MR Reconstruction

### Author:

Lei Xiang, Yong Chen , Weitang Chang, Yiqiang Zhan, Weili Lin, Qian Wang , and Dinggang Shen

### Year:

2019

### Notes:

阅读日期：2020.02.17

泛读。这篇文章研究的是 MRI 重建。作者考虑的是 T2 的图像采样时间比 T1 长，所以提出的融合不同模态的方法就是用 full-sample 的 T1 图像和 under-sample 的 T2 图像重建 T2 图像。方法就是一个 U-net，用了 dense 的结构。输入是两个图像的合并。没有什么特别的地方。一图看懂一文的类型。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217214453.png"/>

### Bibtex:

```latex
@article{xiang2018deep,
  title={Deep-Learning-Based Multi-Modal Fusion for Fast MR Reconstruction},
  author={Xiang, Lei and Chen, Yong and Chang, Weitang and Zhan, Yiqiang and Lin, Weili and Wang, Qian and Shen, Dinggang},
  journal={IEEE Transactions on Biomedical Engineering},
  volume={66},
  number={7},
  pages={2105--2114},
  year={2018},
  publisher={IEEE}
}
```


### Paper:

A Deep Ensemble Network for Compressed Sensing MRI

### Author:

Huafeng Wu, Yawen Wu, Liyan Sun, Congbo Cai, Yue Huang, and Xinghao Ding

### Year:

2018

### Notes:

阅读日期：2020.02.29

泛读。这篇文章研究的是 MRI，其实非常简单，所谓的 ensemble（也就是集成）指的是把迭代过程中的中间输出在最后汇总起来，用一个卷积层进行综合得到最终的重建结果。网络的基本结构是 cascade 网络，卷积部分和 data fidelity 交替。

网络结构示意图（CNN unit 部分有dense的结构，不过这里的的dense都是先变成单通道，然后在加进去）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200301143851.png"/>

### Bibtex:

```latex
@inproceedings{wu2018deep,
  title={A Deep Ensemble Network for Compressed Sensing MRI},
  author={Wu, Huafeng and Wu, Yawen and Sun, Liyan and Cai, Congbo and Huang, Yue and Ding, Xinghao},
  booktitle={International Conference on Neural Information Processing},
  pages={162--171},
  year={2018},
  organization={Springer}
}
```


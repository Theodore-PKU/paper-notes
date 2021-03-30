# Flow-based Generative Models for Learning Manifold to Manifold Mappings

[TOC]

## 信息

Authors: Xingjian Zhen, Rudrasis Chakraborty, Liu Yang, Vikas Singh

Year: AAAI 2021

Bibtex:

```
@article{zhen2020flow,
  title={Flow-based Generative Models for Learning Manifold to Manifold Mappings},
  author={Zhen, Xingjian and Chakraborty, Rudrasis and Yang, Liu and Singh, Vikas},
  journal={arXiv preprint arXiv:2012.10013},
  year={2020}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/flow-based-generative-models-for-learning-manifold-to-manifold-mappings.pdf)



## 概括

这篇文章题目是 manifold to manifold，想表达的意思两点含义。第一点是 x 所在空间不是实数空间，而是非欧空间，距离等定义都和欧式空间不一样，所以称为 manifold，作者认为一般的 flow 模型考虑的是欧几里得空间，对于非欧空间，作者使用了一个 chart map，大概的意思就是对 manifold 数据进行变换，在原始的 glow 模型基础上，做了相应的修改。第二点是作者考虑了一个问题，从一个 manifold 到另一个 manifold，关于这一点，作者设计了两个平行的 flow 模型来处理。

作者在这篇文章中考虑了三种不同的 manifold 数据，给出了相应的具体的变换和数学公式。

个人觉得这个工作对于自己的课题没有什么参考意见。所以没有非常详细地看。
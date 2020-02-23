### Paper:

Image Prediction for Limited-angle Tomography via Deep Learning with Convolutional Neural Network

### Author:

Hanming Zhang, Liang Li, Kai Qiao, Linyuan Wang, Bin Yan, Lei Li, Guoen Hu

### Year:

2016

### Notes:

这篇文章研究的问题是CT中的有限角问题。作者的想法就是用神经网络来实现去伪影，没有其他的什么模型。输入是FBP的重建结果，带伪影，输出是干净的图像。这么做的理由是研究证明有限角产生的伪影具有相似的特征，所以用深度学习的方法来捕捉这种特征结构并且消除。作者使用的网络结构非常简单只有三层，其中中间层用的是1x1卷积，ReLU 的激活函数。其他没有什么要提的了。

另外，作者回顾了有限角的重建算法的两个思路：1. 弥补缺失的 sinogram，2. 增加图像的先验信息作为正则项。

### Bibtex:

```latex
@article{zhang2016image,
  title={Image prediction for limited-angle tomography via deep learning with convolutional neural network},
  author={Zhang, Hanming and Li, Liang and Qiao, Kai and Wang, Linyuan and Yan, Bin and Li, Lei and Hu, Guoen},
  journal={arXiv preprint arXiv:1607.08707},
  year={2016}
}
```
### Paper:

Output Encoding by Compressed Sensing for Cell Detection with Deep Convnet

### Author:

Yao Xue, Nilanjan Ray

### Year:

2018

### Notes:

这篇文章主要内容是利用压缩感知，在 output encoding 的框架下解决一个细胞检测的任务，并不是一个压缩感知的求解算法的相关文章，其中用到的深度学习的方法也只是为了回归预测。细胞检测的任务是作为 output encoding 的测试。output encoding 的意思是说将具有稀疏性质的lable 转换成其他的域的表示。然后预测出新的表示，再变换回稀疏label。比如细胞检测中，细胞的位置就是具有稀疏性质的label。压缩感知在其中起到的作用就是将稀疏label经过测量矩阵变换得到测量值。使用机器学习的模型回归预测测量值，再用压缩感知算法重建回label。

作者解释为什么要使用压缩感知的原因在于，这样预测回归的向量长度要短很多，作者认为这样相当于是一种正则化。另外在几何上，这种变换是保距的。

一种简单的获得测量值的方法是将groudtruth 的二值图像变成向量后乘上一个随机高斯矩阵。但是作者为了减小矩阵的大小，使用了更复杂的转换方式。首先在图像外围定义多条直线（不同的角度），计算每一个细胞位置到直线的距离，这样可以得到一个更短的向量，长度只有 $\sqrt{w^2 + h^2}$，事实上计算距离的有一个格点离散 的过程。（如果细胞的分布不是完全稀疏的就不行了）。设有 $L$ 条直线，就得到 L 个 $f_l$ （其实有了 $f_l$ ，就大致知道细胞的位置了。多个直线可以更鲁棒），然后对 $f_l$ 乘上高斯随机矩阵，得到 $y_l$ ，再将 $y_l$ 拼接起来。重建的时候用 DAL 算法得到 $f_l$ 的估计，由 $f_l$ 得到预测的位置，再取平均。

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-17%E4%B8%8A%E5%8D%8811.47.50.png)

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-17%E4%B8%8A%E5%8D%8811.54.25.png" style="zoom:50%;" />

作者使用的机器学习方法是 deep learning，使用的网络有两种，AlexNet 和 152层的 ResNet。在回归预测的时候，使用了多任务学习，另一个学习目标是预测细胞数量。作者使用了patch 的训练方式，对于细胞检测，patch的做法当然没有什么问题。损失函数是欧几里得距离的损失函数。

### Bibtex:

```latex
@inproceedings{xue2018output,
  title={Output encoding by compressed sensing for cell detection with deep convnet},
  author={Xue, Yao and Ray, Nilanjan},
  booktitle={Workshops at the Thirty-Second AAAI Conference on Artificial Intelligence},
  year={2018}
}
```
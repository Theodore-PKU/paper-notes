# Image Synthesis and Editing with Stochastic Differential Equations

[TOC]

## 信息

作者：Chenlin Meng, Yang Song Jiaming Song, Jiajun Wu, Jun-Yan Zhu, Stefano Ermon

期刊会议：arXiv

年份：2021

Bibtex:

```
@article{meng2021sdedit,
  title={Sdedit: Image synthesis and editing with stochastic differential equations},
  author={Meng, Chenlin and Song, Yang and Song, Jiaming and Wu, Jiajun and Zhu, Jun-Yan and Ermon, Stefano},
  journal={arXiv preprint arXiv:2108.01073},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/image-synthesis-and-editing-with-stochastic-differential-equations.pdf)



## 概括

通读。

这篇文章是 Yang Song 等人利用 SDE 生成模型解决图像编辑问题的尝试。他们考虑的是一种 learning free 的方法，也就是说 SDE 生成模型和图像编辑问题无关，可以单独训练，一旦这个模型训练好之后，就可以使用这篇文章提出的采样方法实现图像编辑。在图像编辑的任务中，输入图像和生成的结果差异是很大的。作者的想法也很简单，对输入图像施加高斯噪声，作为 SDE 逆扩散过程的初始值（此时保留基本的图像结构，但是细节完全被噪声破坏），然后进行迭代去噪。这个过程通常会持续多次，保证图像逐渐变得更真实。有的图像编辑的任务会保留大部分的区域（都是真实的图像部分），因此采样的时候，实际上会保留这些区域，只有需要生成的部分交给 SDE 模型。

这篇文章考虑的问题虽然和我们的课题无关，但是这个应用的想法比较有趣。



## 方法

这篇文章讨论的图像编辑的任务具体有三种。分别是从粗画笔图画的图像生成、图像中增加一个粗画笔编辑（在粗画笔的部分生成）、图像组合（将一幅图画的一部分重新生成）。作者利用 score-based（SDE） 模型来解决这些问题。ddpm 模型是事先训练好的，仅仅是对采样的过程进行重新设计，因此是 learning-free 的方法。

![屏幕快照 2021-11-20 下午4.54.29](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.54.29.png)

我们以粗画笔图画的图像生成为例来描述作者的想法。SDE 描述了从高斯分布到图像分布的变化。当我们有一个 stroke 图像时，先将这个图像加噪声，高斯化，然后把这个结果作为 SDE 逆过程的初始值进行不断迭代去噪。这个过程可以重复多次。另外如何选择加多少噪声（perturb 的程度），从哪个时刻 t 开始进行去噪声（这两者应该是一一对应的），这是需要针对不同的任务来考虑（我们不关注细节了）。

![屏幕快照 2021-11-20 下午4.54.43](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.54.43.png)

采样算法如下：

![屏幕快照 2021-11-20 下午4.54.52](/Users/xieyutong/Pictures/screenshot/屏幕快照 2021-11-20 下午4.54.52.png)

这里的 K 控制的就是重复的次数。$t_0$ 控制的是逆过程的起始时刻。$\Omega$ 表示的是在哪些区域进行生成。对于粗画笔图像生成任务，是整个区域，对于粗画笔编辑，则是粗画笔的区域。这种使用 $\Omega$ 控制的方式非常类似于我们在 kspace 区域固定一部分系数的想法。

作者实验的效果挺不错的，而且和其他方法比较提升很大（当然不太了解这个领域，因此不过多评价）。
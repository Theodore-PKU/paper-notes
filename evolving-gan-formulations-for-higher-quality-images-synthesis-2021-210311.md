# Evolving GAN Formulations for Higher Quality Image Synthesis

[TOC]

## 信息

Authors: Santiago Gonzalez, Mohak Kant, Risto Miikkulainen

Year: arXiv 2021

Bibtex:

```latex
@article{gonzalez2021evolving,
  title={Evolving GAN Formulations for Higher Quality Image Synthesis},
  author={Gonzalez, Santiago and Kant, Mohak and Miikkulainen, Risto},
  journal={arXiv preprint arXiv:2102.08578},
  year={2021}
}
```

[Paper](file:///Users/xieyutong/Documents/Research/PaperReading/Papers/evolving-gan-formulations-for-higher-quality-images-synthesis.pdf)



## 概括

这篇文章的研究思路是将 GAN 训练时的损失函数写成一个三阶函数的形式，然后在训练的过程中不断更新三阶函数的参数。换言之是一个不定的损失函数。使用进化算法得到最终的三阶函数形式。这种方法我认为不是我要研究的方向。因为这种做法需要足够多的优化的背景才能分析其有效性。我本身认为这种做法是不必要的。而且作者验证这个方法所用的 task 不是常见的 task，让人感到可疑。
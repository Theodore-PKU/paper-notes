### Paper:

PILOT: Physics-Informed Learned Optimal Trajectories for Accelerated MRI

### Author:

Tomer Weiss, Ortal Senouf, Sanketh Vedula, Oleg Michailovich, Michael Zibulevsky, Alex Bronstein

### Year:

2019

### Notes:

阅读日期：2020.02.21

精读。这篇文章研究的是 MRI 的采样策略。作者之前写过一篇相同主题的文章，用的方法是对笛卡尔采样的 position 的选择进行概率参数化，然后进行端到端的学习。这篇文章则是基于物理的限制，直接将采样点作为优化的参数之一（不过这部分参数化没有看懂）。总体的网络结构为：full-sample ksapce 是输入，经过一个 sampling 层得到 undersample 采样，然后经过傅立叶格点化得到可以处理的 kspace，然后用一个 U-net 进行重建。关于训练的损失函数，有两个部分，一个是重建的task loss，一个是采样的物理限制（具体来说就是一阶和二阶导数的上界）。训练的时候，作者提出了两种方式。第一种：采样 scheme 用某种传统的采样方式，作为初始化进行训练。第二种方式：初始采样 scheme 完全随机，在不考虑物理限制的情况下进行训练，得到一个离散的 sampling scheme，用旅行商问题的贪心算法得到一个连续的 scheme，最后在此基础上，考虑物理限制的训练得到最终的 sampling scheme。

物理限制：

第一个是一阶梯度：

<img src="http://latex.codecogs.com/svg.latex? |\dot{k}| \approx \frac{\left|k_{i+1}-k_{i}\right|}{d t} \leq v_{\max }=\gamma G_{\max }" border="0"/>

第二个是二阶梯度：

<img src="http://latex.codecogs.com/svg.latex? |\ddot{k}| \approx \frac{\left|k_{i+1}-2 k_{i}+k_{i-1}\right|}{d t^{2}} \leq a_{\max }=\gamma S_{\max }" border="0"/>

对应的损失函数是：

<img src="http://latex.codecogs.com/svg.latex? L_{\text{const}} = \sum_i \max \left(0,|\dot{k}|-v_{\max }\right) + \max  \left(0,|\ddot{k}|-a_{\max }\right)" border="0"/>

重建任务比较简单，是一个 1 范数：

<img src="http://latex.codecogs.com/svg.latex? L_{\text {task }}=\left\|\hat{\mathbf{Z}}-\mathcal{F}^{-1}(\mathbf{X})\right\|_{1}" border="0"/>

总的损失函数是：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{k}, \boldsymbol{\theta}} \sum_{(\mathbf{X}, \mathbf{Z})} L_{\mathrm{task}}\left(R_{\boldsymbol{\theta}}\left(\hat{\mathcal{F}}_{\mathbf{k}}^{-1}\left(\mathcal{S}_{\mathbf{k}}(\mathbf{X})\right)\right), \mathbf{Z}\right)+L_{\mathrm{const}}(\mathbf{k})" border="0"/>

作者提出的两种训练方式：PILOT 是用一个已有的合适的 trajectory 作为 scheme 的初始化，然后用带有 const loss 的损失函数进行训练（可以看成是对已有的 trajectory 的 fine-tuning），而 PILOT-TSP 则是为了避免局部最优，避免初始化的影响，试图找到更好的 trajectory 提出的训练方法。实验结果上看，后者不一定比前者好

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221111726.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221111735.png"/>

下面给出训练后的 trajectory 的例子：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221112117.png"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200221112136.png"/>

### Bibtex:

```latex
@article{weiss2019pilot,
  title={PILOT: Physics-Informed Learned Optimal Trajectories for Accelerated MRI},
  author={Weiss, Tomer and Senouf, Ortal and Vedula, Sanketh and Michailovich, Oleg and Zibulevsky, Michael and Bronstein, Alex},
  journal={arXiv preprint arXiv:1909.05773},
  year={2019}
}
```


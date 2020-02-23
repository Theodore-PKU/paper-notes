### Paper:

MRI Reconstruction with Interpretable Pixel-Wise Operations Using Reinforcement Learning

### Author:

Wentian Li, Xidong Feng, Haotian An, Xiang Yao Ng, Yu-Jin Zhang

### Year:

2019

### Notes:

阅读日期：2020.02.14

泛读。这篇文章研究的是 MRI 重建，用的方法是强化学习。作者把每一个 pixel 都当作一个 agent，所以是是一个  multi-agent 的强化学习。动作的可选择空间是主要是卷积操作（也就是 pixel 的变化定义为卷积的结果），这些卷积操作有的包含可变参数，这些可变参数是通过一个网络来计算的。reward 其实就是和 groundtruth 的差值绝对值的变化，折扣系数是 1，episode 的取 3 步。整体的结构包括四个部分，首先有一个共同的特征抽取网络，计算参数的一个网络分支，计算 policy 的网络分支，和一个计算 value 的网络分支。在测试时，对强化学习的结果施加一个 data consistency 的计算。训练采用的是 A2C 的训练方式。

action：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214123217.png" width="70%"/>

reward：

<img src="http://latex.codecogs.com/svg.latex? r^{(t)}=| x^{(t)}-x_{\text {target}}|-| x^{(t+1)}-x_{\text {target}} |" border="0"/>

network：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200214123253.png" width="70%"/>

> 这篇文章促使我去思考强化学习这个操作和投影的关系

### Bibtex:

```latex
@article{limri,
  title={MRI Reconstruction with Interpretable Pixel-Wise Operations Using Reinforcement Learning},
  author={Li, Wentian and Feng, Xidong and An, Haotian and Ng, Xiang Yao and Zhang, Yu-Jin}
}
```


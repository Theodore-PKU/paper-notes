### Paper:

Intelligent Parameter Tuning in Optimization-Based Iterative CT Reconstruction via Deep Reinforcement Learning

### Author:

Chenyang Shen, Yesenia Gonzalez, Liyuan Chen, Steve B. Jiang, and Xun Jia

### Year:

2018

### Notes:

阅读日期：2020.02.09

泛读。这篇文章研究的是用强化学习来调节CT重建算法中的参数。作者考虑的重建算法是基于 TV 正则项的重建算法。通过强化学习获得的是 TV 项的系数（这个系数是 pixel-wise）。作者设定的强化学习的环境是：状态 s 是每个 pixel 为中心的一个 patch；动作 a 有五个可选，不改变，增加或减少10%，50%；奖励 r 是一个和真实图像有关的误差计算，因为只有在训练时涉及到奖励，所以可以使用真实图像。强化学习的策略是 Q-learning。每次调整完，都重新用 ADMM 算法算一个重建结果。没有看具体的网络设计和试验，个人感觉这种做法有一些多此一举。因为合适的参数只有一个，很难说一定要建立强化学习的状态。不过这样做的好处是不用自己调参算 label，这也许其采用强化学习合理的地方吧。根据动作的离散化，在应用时，应该会多次调节，才能得到一个最好的调参结果。

目标函数：

<img src="http://latex.codecogs.com/svg.latex? f^{*}=\arg \min _{f} \frac{1}{2}|P f-g|^{2}+|\lambda \cdot \nabla f|" border="0"/>

奖励的计算（$S_{f^*}(x), S_{f}(x)$ 表示的都是相应的 patch）：

<img src="http://latex.codecogs.com/svg.latex? r^{k}(x)=\frac{\left|S_{f^*}(x)\right|}{\left|S_{f^{k+1}}(x)-S_{f^{*}}(x)\right|}-\frac{\left|S_{f^{*}}(x)\right|}{\left|S_{f^{k}}(x)-S_{f^{*}}(x)\right|}" border="0"/>

### Bibtex:

```latex
@article{shen2018intelligent,
  title={Intelligent parameter tuning in optimization-based iterative CT reconstruction via deep reinforcement learning},
  author={Shen, Chenyang and Gonzalez, Yesenia and Chen, Liyuan and Jiang, Steve B and Jia, Xun},
  journal={IEEE transactions on medical imaging},
  volume={37},
  number={6},
  pages={1430--1439},
  year={2018},
  publisher={IEEE}
}
```


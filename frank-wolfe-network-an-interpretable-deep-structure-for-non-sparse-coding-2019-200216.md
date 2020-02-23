### Paper:

Frank-Wolfe Network: An Interpretable Deep Structure for Non-Sparse Coding

### Author:

Dong Liu, Ke Sun, Zhangyang Wang, Runsheng Liu, and Zheng-Jun Zha

### Year:

2019

### Notes:

阅读日期：2020.02.16

精读。这篇文章研究的是比较一般的 p-范数 coding 的问题。通常 p = 0 或 p = 1时，对应的是 sparse 的 coding，对于 p 取其他值，特别是 > 1时，就不是稀疏的了。这篇文章考虑的正是这种情形，虽然我不太懂这种 coding 的意义在哪里。作者参考的是 Frank-Wolfe 算法，并且将其展开为网络的形式。Frank-Wolfe 算法解决的其实是一般的带约束凸问题，本身的算法比较简单，应用在 p-范数 coding 的问题上，和 thresholding 有一点点相似（这个相似的算子称为 $pool_p$，可以看成是类似激活函数的存在。）。在 p-范数 coding 的问题中，有两个问题要解决，一个是字典，一个是 p 的选择。这篇文章先把字典 D 固定，以求解 coding 的过程推导出网络的结构，然后将涉及的 D 的所有运算都当作是可学习的参数，p 可以事先选择，也可以在训练中学习。当然也可以把所有的线性全连接部分改成卷积的形式。网络输出是 coding z，所以训练的时候需要有 coding，因此作者总是设计出一些数据集对应的 coding 之后才能训练。另外，作者还试图将这个算法拓展到分类问题（光学数字识别），使用的方式是先预训练网络，然后在网络的最后一层加一个线性层（把 coding 当作是特征）和 softmax 层。作者还试图将算法拓展到和图像处理相关的问题上，比如去噪和超分辨，但是作者的做法仅仅是在最后一层加一个线性层，然后直接训练。作者在比较图像处理的实验中，所用的对比方法虽然看似比较先进，但是网络层数都非常有限，感觉这种比对方式没有太大意义，有粉饰的感觉，不可取。而且在图像处理的部分，已经完全看不到 p-范数 coding 的意义，从实际结果上看，更像是一个单纯的网络设计，而且这个网络设计比较特别的地方在于激活函数部分。

下面介绍各个部分的细节。

P-范数 coding 的数学描述：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned}\left\{\left\{\mathbf{z}_{i}^{*}\right\}, D^{*}\right\}=& \arg \min \sum_{i=1}^{N}\left\|\mathbf{x}_{i}-D \mathbf{z}_{i}\right\|_{2}^{2} \\ & \text { subject to } \forall i,\left\|\mathbf{z}_{i}\right\|_{p} \leq c \end{aligned}" border="0"/>

F-W 算法求解的一般问题描述及解法：

<img src="http://latex.codecogs.com/svg.latex? \min _{\mathbf{z}} f(\mathbf{z}) \text { s.t. } \mathbf{z} \in \mathcal{Z}" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{s}^{t} &:=\arg \min _{\mathbf{s} \in \mathcal{Z}} \mathbf{s}^{\top} \nabla f\left(\mathbf{z}^{t}\right) \\ \mathbf{z}^{t+1} &:=\left(1-\gamma^{t}\right) \mathbf{z}^{t}+\gamma^{t} \mathbf{s}^{t} \end{aligned}" border="0"/>

注意，$s^t$ 的计算在 p-范数 coding 的任务中有相应的表达式：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} s_{i}^{t}=-c \cdot \operatorname{sign}\left(\nabla_{i} f\left(\mathbf{z}^{t}\right)\right) \frac{\left|\nabla_{i} f\left(\mathbf{z}^{t}\right)\right| \frac{1}{p-1}}{\left(\sum_{j=1}^{m}\left|\nabla_{j} f\left(\mathbf{z}^{t}\right)\right|^{\frac{p}{p-1}}\right)^{\frac{1}{p}}} \\ i=1,2, \ldots, m \end{aligned}" border="0"/>

其中：$\nabla f\left(\mathbf{z}^{t}\right)=D^{\top}\left(D \mathbf{z}^{t}-\mathbf{x}\right)$，步长通常取 $\gamma^{t}:=\frac{2}{t+2}$。作者还解释了在某些 p 的取值下 $s^t$ 计算的直观结果。这个运算也被计作：$\mathbf{s}^{t}=\operatorname{pool}_{p}\left(\nabla f\left(\mathbf{z}^{t}\right)\right)$

作者还分析了 $pool_p$ 算子的分解形式：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{y}^{t}=\frac{\mathbf{u}^{t}}{\left\|\mathbf{u}^{t}\right\|_{\frac{p}{p-1}}} \quad \text { If Step } 1: p \text { -conjugate normalization }} \\ {s_{i}^{t}=-c \cdot \operatorname{sign}\left(y_{i}^{t}\right) \cdot\left|y_{i}^{t}\right|^{\frac{1}{p-1}} \quad \text { If Step } 2: p \text { -exponential neuron }}\end{array}"  border="0"/>

> 我觉得是一个很好的理解深度学习和传统算法之间的关联的角度

建立网络的时候，用如下表达：

<img src="http://latex.codecogs.com/svg.latex? \nabla f\left(\mathbf{z}^{t}\right)=W_{t} \mathbf{z}^{t}+W_{0} \mathbf{x}" border="0"/>

这里的 $W_t,W_0$ 都是可训练的参数（这里就相当于全连接了，也可以替换成卷积）。而 p 也参与到计算中，所以也可以设为可学习的参数。作者的实验表明，p 一般会收敛到一个固定的值。而且如果事先固定 p 为收敛的值时，效果反而会不好。在原来的字典学习中，一般对 $D$ 也有范数的限制。但在这里 D 已经转化为参数了，所以作者用 weight decay 替代了这个约束。

> 这会不会和 DIP 有点像。如果一起训练 input 和 weight，重建效果应该最好，但是如果固定其中一个，即使是初始化为之前的训练结果，效果也不一定好。因为重新训练的时候有随机性？

作者用 LSTM 来解释这个网络结构 

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200217113751.png"/>

作者的模拟实验（即恢复 coding 和字典 D）中，可以实现已知 coding，进行训练。而在字符识别的任务中，coding 是经过两次 PCA 的计算得到的。其他实验我觉得很一般，不说明了

### Bibtex:

```latex
@article{liu2019frank,
  title={Frank-Wolfe Network: An Interpretable Deep Structure for Non-Sparse Coding},
  author={Liu, Dong and Sun, Ke and Wang, Zhangyang and Liu, Runsheng and Zha, Zheng-Jun},
  journal={IEEE Transactions on Circuits and Systems for Video Technology},
  year={2019},
  publisher={IEEE}
}
```


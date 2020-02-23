### Paper:

Unrolled Projected Gradient Descent for Multi-spectral Image Fusion

### Author:

Suhas Lohit, Dehong Liu, Hassan Mansour, Petros T. Boufounos

### Year:

2019

### Notes:

阅读日期：2020.02.22

泛读。这篇文章研究的是多频谱图像的问题。使用的方法如题目所述，是展开一个投影梯度算法。其中，投影算子用 CNN 表示，CNN 的结构是四层的卷积 + residual，比较简单。对于这个问题，有一个难点是测量矩阵包含一个未知的 blur 卷积。因此作者考虑将这个部分也参数化（用一个卷积化的参数来表示），连同所有的参数和 CNN 一起训练。实验表明，这样做的效果会比事先制定卷积不存在更好。也没啥参考价值。

原始目标函数：

<img src="http://latex.codecogs.com/svg.latex? \mathbf{x}^{*}=\operatorname{argmin} \frac{1}{2}\|\mathbf{y}-\mathbf{A} \mathbf{x}\|_{2}^{2} \text { s.t. } \mathbf{x} \in \mathcal{C}" border="0"/>

投影一阶梯度算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{w}^{k+1}=\mathbf{x}^{k}+\alpha \mathbf{A}^{T}\left(\mathbf{y}-\mathbf{A} \mathbf{x}^{k}\right)} \\ {\mathbf{x}^{k+1}=\Pi_{C}\left(\mathbf{w}^{k+1}\right)}\end{array}" border="0"/>

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200222143202.png" width="70%"/>

forward operator A 的参数化（其实这部分细节没仔细看）：

<img src="http://latex.codecogs.com/svg.latex? \begin{aligned} \mathbf{K}_{\mathbf{A}}=\mathbf{K}_{\mathbf{B}}+\mathbf{K}_{\mathbf{I}}, \text { s.t. } & \sum_{i=1}^{S} \sum_{j=1}^{S} \mathbf{K}_{\mathbf{A}}(i, j)=1 \\ & \mathbf{K}_{\mathbf{A}}(i, j) \geq 0, \forall i, j \in\{1, \ldots, S\} \end{aligned}" border="0"/>

### Bibtex:

```latex
@inproceedings{lohit2019unrolled,
  title={Unrolled projected gradient descent for multi-spectral image fusion},
  author={Lohit, Suhas and Liu, Dehong and Mansour, Hassan and Boufounos, Petros T},
  booktitle={ICASSP 2019-2019 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  pages={7725--7729},
  year={2019},
  organization={IEEE}
}
```


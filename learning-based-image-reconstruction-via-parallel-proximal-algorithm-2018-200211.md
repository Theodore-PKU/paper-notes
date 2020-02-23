### Paper:

Learning-based Image Reconstruction via Parallel Proximal Algorithm

### Author:

Emrah Bostan, Ulugbek S. Kamilov, and Laura Waller

### Year:

2018

### Notes:

阅读日期：2020.02.11

泛读。这篇文章其实非常简单，就是对 FBBP 算法（这个算法其实和 ISTA 非常相似）进行展开，其中只有 shrinkage 函数和 step size 是可变参数，其他都是固定的。用数据集进行训练，损失函数是 2 范数。没有什么参考价值。

这篇文章定义的正则项（用的是小波变换来当作 TV 项）：

<img src="http://latex.codecogs.com/svg.latex? \mathcal{R}_{\mathrm{TV}}(\mathbf{x})=\tau \sqrt{2} \sum_{k=1}^{4} \sum_{n \in \mathcal{H}_{k}}\left|\left[\mathbf{W}_{k} \mathbf{x}\right]_{n}\right|" border="0"/>

对应的算法 FPPA（感觉和 ISTA 很像）：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{s}^{t} \leftarrow \mu_{t} \mathbf{x}^{t-1}+\left(1-\mu_{t}\right) \mathbf{x}^{t-2}} \\ {\mathbf{z}^{t} \leftarrow \mathbf{s}^{t}-\gamma_{t} \mathbf{H}^{\mathrm{T}}\left(\mathbf{H} \mathbf{s}^{t}-\mathbf{y}\right)} \\ {\mathbf{x}^{t} \leftarrow \mathbf{W}^{\mathrm{T}} \mathcal{T}\left(\mathbf{W} \mathbf{z}^{t}, 2 \sqrt{2} \tau \gamma_{t}\right)}\end{array}" border="0"/>

作者提出的参数化部分（shrinkage 部分）

<img src="http://latex.codecogs.com/svg.latex? \mathcal{T}_{k}^{t}(x)=\sum_{p=-P}^{P} c_{k p}^{t} \varphi\left(\frac{x}{\Delta}-p\right)" border="0"/>

step size 部分：

<img src="http://latex.codecogs.com/svg.latex? \gamma=\phi(\alpha)=\left\{\begin{array}{ll}{\mathrm{e}^{\alpha-1}} & {\text { if } \alpha \leq 1} \\ {\alpha} & {\text { otherwise }}\end{array}\right." border="0"/>

这里的 $\gamma$ 的参数是 $\alpha$.

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{s}^{t} \leftarrow \mu_{t} \mathbf{x}^{t-1}+\left(1-\mu_{t}\right) \mathbf{x}^{t-2}} \\ {\mathbf{z}^{t} \leftarrow \mathbf{s}^{t}-\phi\left(\alpha_{t}\right) \mathbf{H}^{\mathrm{T}}\left(\mathbf{H s}^{t}-\mathbf{y}\right)} \\ {\mathbf{x}^{t} \leftarrow \sum_{k=1}^{4} \mathbf{W}_{k}^{\mathrm{T}} \mathcal{T}_{k}^{t}\left(\mathbf{W}_{k} \mathbf{z}^{t}\right)}\end{array}" border="0"/>

参数化之后的 TPPA：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\mathbf{s}^{t} \leftarrow \mu_{t} \mathbf{x}^{t-1}+\left(1-\mu_{t}\right) \mathbf{x}^{t-2}} \\ {\mathbf{z}^{t} \leftarrow \mathbf{s}^{t}-\phi\left(\alpha_{t}\right) \mathbf{H}^{\mathrm{T}}\left(\mathbf{H s}^{t}-\mathbf{y}\right)} \\ {\mathbf{x}^{t} \leftarrow \sum_{k=1}^{4} \mathbf{W}_{k}^{\mathrm{T}} \mathcal{T}_{k}^{t}\left(\mathbf{W}_{k} \mathbf{z}^{t}\right)}\end{array}" border="0"/>

### Bibtex:

```latex
@article{bostan2018learning,
  title={Learning-based image reconstruction via parallel proximal algorithm},
  author={Bostan, Emrah and Kamilov, Ulugbek S and Waller, Laura},
  journal={IEEE Signal Processing Letters},
  volume={25},
  number={7},
  pages={989--993},
  year={2018},
  publisher={IEEE}
}
```


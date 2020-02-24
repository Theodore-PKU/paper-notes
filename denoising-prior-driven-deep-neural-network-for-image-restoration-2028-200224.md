### Paper:

Denoising Prior Driven Deep Neural Network for Image Restoration

### Author:

Weisheng Dong, Peiyao Wang, Wotao Yin, Guangming Shi, Fangfang Wu, and Xiaotong Lu

### Year:

2018

### Notes:

阅读日期：2020.02.24

泛读。这篇文章研究的是图像复原，诸如去模糊、超分辨等任务。使用的方法是 unroll 迭代算法，迭代算法的框架是类似 ADMM 的交替迭代子问题，其实也很常见，然后把和正则项有关的 proximal 算子用一个去噪网络替代，另外一个 least square 子问题的解用一步迭代的 CG 更新。神经网络比较简单，是 DCNN。另外，在 CG 更新中的一些矩阵，作者也考虑变成可训练的参数。所有的迭代步骤都展开成网络，并进行端到端的训练。参考价值不大。

<img src="http://latex.codecogs.com/svg.latex? (\boldsymbol{x}, \boldsymbol{v})=\underset{\boldsymbol{x}, \boldsymbol{v}}{\operatorname{argmin}} \frac{1}{2}\|\boldsymbol{y}-\mathbf{A} \boldsymbol{x}\|_{2}^{2}+\lambda J(\boldsymbol{v}), \text { s.t. } \boldsymbol{x}=\boldsymbol{v}" border="0"/>

改成无约束的问题就是：

<img src="http://latex.codecogs.com/svg.latex? (\boldsymbol{x}, \boldsymbol{v})=\underset{\boldsymbol{x}, \boldsymbol{v}}{\operatorname{argmin}} \frac{1}{2}\|\boldsymbol{y}-\mathbf{A} \boldsymbol{x}\|_{2}^{2}+\eta\left\|\boldsymbol{x}-\boldsymbol{v}^{(t)}\right\|_{2}^{2}+\lambda J(\boldsymbol{v})" border="0"/>

求解算法：（把 f 用去噪网络表示）

<img src="https://cdn.mathpix.com/snip/images/3O8zX-c28OlcdJstnSRTyVPSWE5yYUqmcb5gco_x6Sg.original.fullsize.png" width="70%"/>

<img src="https://cdn.mathpix.com/snip/images/gJnXWuOh75tCk8rdSARSRegDIOftrJ3U0LSgevUQAL0.original.fullsize.png" />

### Bibtex:

```latex
@article{dong2018denoising,
  title={Denoising prior driven deep neural network for image restoration},
  author={Dong, Weisheng and Wang, Peiyao and Yin, Wotao and Shi, Guangming and Wu, Fangfang and Lu, Xiaotong},
  journal={IEEE transactions on pattern analysis and machine intelligence},
  volume={41},
  number={10},
  pages={2305--2318},
  year={2018},
  publisher={IEEE}
}
```


### Paper:

Deep learning for undersampled MRI reconstruction

### Author:

Chang Min Hyun, Hwa Pyung Kim, Sung Min Lee, Sungchul Lee, and Jin Keun Seo

### Year:

2018

### Notes:

泛读。这篇文章研究的是 MRI 的重建，使用的方法很简单，U-Net + kspace correction。不过作者提到了一些比较有用的信息。

作者指出，如果是极小化下式，就得到 zero-fill 重建（因此 zero-fill 重建也是有一定的合理性）

<img src="http://latex.codecogs.com/svg.latex? y_{b}= \underset{y \text { s.t. } S \circ \mathcal{F}_{y}=x}{\arg\min}  \|y\|_{\ell^{2}}" border="0"/>

作者在讨论采样方式的时候，指出采样方式应该满足下式（seperate 原则）：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{y}_{1} \neq \boldsymbol{y}_{2} \text { implies }\left|\mathcal{F}^{-1}\right| \circ \mathcal{P} \circ \mathcal{S} \circ \mathcal{F}\left(\boldsymbol{y}_{1}\right) \neq\left|\mathcal{F}^{-1}\right| \circ \mathcal{P} \circ \mathcal{P} \circ \mathcal{F}\left(\boldsymbol{y}_{2}\right)" border="0"/>

也就是不同的图像，zero-fill 的结果应该不同。而对于 uniform 的采样方式则会出一些问题。下式是2-fold加速和 4-fold 加速，假设是平均间隔的采样，根据 Poisson summation formula，有下面的结果：

<img src="http://latex.codecogs.com/svg.latex? y_{2-\text{fold }}(n, m)=y(n, m)+y(n, m+N / 2)" border="0"/>

<img src="http://latex.codecogs.com/svg.latex? y_{4 \text { -fold }}(n, m)=\sum_{j=0}^{3} y\left(n, m+\frac{j N}{4}\right)" border="0"/>

是否完全包含低频部分有很大的差异：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-20%E4%B8%8B%E5%8D%882.20.12.png"/>

从 poisson summation formula 来看，kspace 中的高频部分的缺失，zero-fill 重建的结果感觉可以看成是高频部分的平移叠加？

流程图（作者使用的 U-Net，对最终输出图像做了 kspace correction。训练网络使用的是图像 2 范数）

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-20%E4%B8%8B%E5%8D%882.06.29.png" width="70%"/>

实际上，作者考虑的都是实数。kspace 的correction就是把原始的欠采样 kspace 直接替换重建结果的kspace 系数。

> 整体的思路没有什么特别的地方，不过关于采样方式，uniform sampling 的讨论值得思考。特别是作者提出的 seperate 原则。

### Bibtex:

```latex
@article{hyun2018deep,
  title={Deep learning for undersampled MRI reconstruction},
  author={Hyun, Chang Min and Kim, Hwa Pyung and Lee, Sung Min and Lee, Sungchul and Seo, Jin Keun},
  journal={Physics in Medicine \& Biology},
  volume={63},
  number={13},
  pages={135007},
  year={2018},
  publisher={IOP Publishing}
}
```


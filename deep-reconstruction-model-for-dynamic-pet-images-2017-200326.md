### Paper:

Deep reconstruction model for dynamic PET images

### Author:

Jianan Cui, Xin Liu, Yile Wang, Huafeng Liu

### Year:

2017

### Notes:

泛读。这篇文章研究的是 PET 的重建，而且是动态 PET 的重建。动态 PET 和静态 PET 的差别主要在于，动态 PET 为了有更好的时间分辨率，相邻帧之间的间隔时间短，所以光子计数就比较少，因此空间分辨率会下降，这是一个 tradeoff。这篇文章在处理 PET 重建上，其实也没有考虑时间上的连续，本质仍然是每一帧独立处理。使用的方法总结起来就是先用一个传统的算法 maximum likelihood expectation maximization (MLEM) 得到初步重建结果，然后分解成 patch，输入训练好的 SAE 中，最后对输出的 patch 做高斯平均得到最终的重建图像。这种方法可以归结到神经网络直接重建这一类。

SAE 的结构非常简单，因为这篇文章特别早，就是线性层和激活层。线性层是全连接的矩阵形式。所谓的 SAE，指的是有多个一层的 encoder，最后一层是 decoder。训练时，先训练一个单层 encoder + decoder，然后保留 encoder，用其输出作为下一个 AE 的输入，这样不断训练就可以得到多个 encoder，只有最后一个 decoder 是保留的。

关于训练数据，作者提到了蒙特卡洛。这里顺便对 PET 的成像进行一点说明。PET 的成像时由放射性元素的分布作为 image，释放的光子以不同的方向发出，被周围的接收器检测的时候，光子的计数实际上时不同位置的体素包含的放射性元素发出的，因此不同位置发出的光子到达具体的一个接收器有一个概率，因此物理模型就是 $y = Gx$，$G$ 表示的是概率的分布。因为是光子计数，所以 $y$ 是一个 poisson 分布，但是由于随机性，所以检测到的 $y$ 必然是由误差的，实际的公式应该是 $y = Gx + \epsilon$，$\epsilon$ 不是高斯分布，而是 poisson 误差分布。因为这样的概率分布，所以传统的算法都是统计模型。蒙特卡洛指的是，假设 x 已知，我们可以直接模拟出（假设 G 已知）y 的结果，生成很多个 y。另外，这里似乎和压缩感知的关系不大，因为 G 似乎没有理由是 under-sampled。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200326200144.png"/>

### Bibtex:

```latex
@article{cui2017deep,
  title={Deep reconstruction model for dynamic PET images},
  author={Cui, Jianan and Liu, Xin and Wang, Yile and Liu, Huafeng},
  journal={PloS one},
  volume={12},
  number={9},
  year={2017},
  publisher={Public Library of Science}
}
```

### 其他


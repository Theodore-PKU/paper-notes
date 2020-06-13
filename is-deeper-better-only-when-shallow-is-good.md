### Is Deeper Better only when Shallow is Good?

Author: Eran Malach, Shai Shalev-Shwartz

Year: 2019

Published in: NeurIPS 2019

```latex
@inproceedings{malach2019deeper,
  title={Is Deeper Better only when Shallow is Good?},
  author={Malach, Eran and Shalev-Shwartz, Shai},
  booktitle={Advances in Neural Information Processing Systems},
  pages={6426--6435},
  year={2019}
}
```

[Link](https://github.com/Theodore-PKU/paper-notes/blob/master/is-deeper-better-only-when-shallow-is-good.md)

Note: /Users/xieyutong/Documents/Research/PaperReading/Notes/is-deeper-better-only-when-shallow-is-good.md



Motivation: a certain network architecture can express some function does not mean that we can learn it from training data in reasonable training time.

Conclusion: in some distributions, the success of learning deep networks depends on whether the distribution can be approximated by shallower networks.



**Preliminaries**

depth separation results: families of functions or distributions that are realizable with deep networks of modest width, but require exponential number of neurons to approximate by shallow networks.

IFS and fractal distributions:

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605084516143.png" alt="image-20200605084516143" style="zoom:50%;" />

Define a distribution: 
$$
\mathcal{D}_{n}=\frac{1}{2}\left(\mathcal{D}_{n}^{+}+\mathcal{D}_{n}^{-}\right)\\
$$
domain of $\mathcal{D}_{n}^{+}$ is $K_n$, label = +1, domain of $\mathcal{D}_{n}^{-}$, label = -1.



Target: represent the fractal distributions.

Network: ReLU MLPs of depth $t$ and width $k$.

Error: $L_{\mathcal{D}}\left(\mathcal{H}_{k, t}\right):=\min _{h \in \mathcal{H}_{k, t}} L_{\mathcal{D}}(h)$, $\mathcal{H}_{k, t}$ is the family of all functions that are implemented by a neural-network of width $k$ and depth $t$.



**Result**

1. depth separation property (expressiveness)

![image-20200605085411843](/Users/xieyutong/Pictures/screenshot/image-20200605085411843.png)

![image-20200605085422761](/Users/xieyutong/Pictures/screenshot/image-20200605085422761.png)

![image-20200605085434568](/Users/xieyutong/Pictures/screenshot/image-20200605085434568.png)



2. error bounds and approximation curve $P(j)=\mathbb{P}_{(\boldsymbol{x}, y) \sim \mathcal{D}_{n}}\left[\boldsymbol{x} \notin K_{j} \text { or } y=1\right]$

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605085539713.png" alt="image-20200605085539713" style="zoom:50%;" />

![image-20200605085934259](/Users/xieyutong/Pictures/screenshot/image-20200605085934259.png)

![image-20200605085959122](/Users/xieyutong/Pictures/screenshot/image-20200605085959122.png)

3. Optimization Analysis

   Cantor distributions

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605090142657.png" alt="image-20200605090142657" style="zoom:50%;" />

![image-20200605090244138](/Users/xieyutong/Pictures/screenshot/image-20200605090244138.png)

​	It shows that every Cantor distribution that cannot be approximated by a shallow network (achieving error greater than 2 1 ), cannot be learned by a deep network (when training with gradient-based algorithms).

<img src="/Users/xieyutong/Library/Application Support/typora-user-images/image-20200605091513472.png" alt="image-20200605091513472" style="zoom:50%;" />



**Experiments**

1. depth separation result

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605090755026.png" alt="image-20200605090755026" style="zoom:50%;" />

2. approximation curve has a crucial effect on learning the distribution (learning depth 5 network on 2D Cantor set of depth 5)

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605090831738.png" alt="image-20200605090831738" style="zoom:50%;" />

3. The effect of depth on learning CIFAR-10

<img src="/Users/xieyutong/Pictures/screenshot/image-20200605090907384.png" alt="image-20200605090907384" style="zoom:50%;" />
### Paper:

Self-Supervised Deep Active Accelerated MRI

### Author:

Kyong Hwan Jin, Michael Unser, and Kwang Moo Yi

### Year:

2019

### Notes:

阅读时间：2020.01.31

精读。这篇文章研究的是 MRI 的重建，是目前看到的第一篇结合了深度学习的文章。作者的主要想法是 sampling scheme 和重建同时进行。作者的重建过程依靠两个网络实现，一个网络是重建网络 ReconNet，输入是当前采样策略下 zero-fill 重建，输出是对应的高质量图像，另一个网络是采样网络 SampleNet，输入是重建网络的输出，其输出是一个概率分布 policy，表示下一个采样的position的概率，依照这个概率最大的 position 进行下一步采样，然后继续使用重建网络重建，依次进行，知道采样率达到限制。这样做的好处显然是可以最有效的利用采样 scheme 的灵活性。这么做的最大难点在于如何训练 SampleNet。针对这个问题，作者使用的方法是蒙特卡洛搜索树 MCST，也就是用强化学习的方法来训练网络。MCST 的目的是根据当前 SampleNet 得出的 policy（下一次的采样 position），用搜索的方法找到该 policy 的改进（仍然是下一次的采样），用这个改进作为 SampleNet 的 target，同时这个改进对应的新的采样 Scheme 可以得到一个新的 zero-fill 重建，又可以作为 ReconNet 的训练样本。事实上，MCST 就是用来产生训练样本，而两个网络的训练，使用的保存的各种样本随机训练（从算法中可以看出）。MCST 的方法，简单概述起来用当前的 policy 和 ReconNet 进行 T 步的深度搜索，计算出最终的 psnr 值，然后通过平均和最大的 psnr 值来确定当前的下一个采样 position 选择的 reward，根据 reward 来 improve policy。两个网络的结构相对简单。最重要的是MCST 的方法来同时训练两个网络。虽然题目叫 self-unsupervised，但是在 ReconNet 训练时还要用到 groundtruth。

MCST 方法的详细说明：

1. MCTS 要随机增加多个采样line，构成了树的结构
2. $P$ 表示采样scheme，$Q$ 表示该采样scheme的平均psnr，$V$ 表示该采样scheme的最大psnr，$C_{puct}$ 是控制参数，用于计算上界（这部分是强化学习的一种做法），$N$ 表示遇到这种采样scheme 的次数，$\delta$ 表示随机的dirac概率扰动。下式是每个 action 的 value 上界

<img src="http://latex.codecogs.com/svg.latex? U(\mathbf{P}, a)=(1-\alpha) Q\left(\left[\begin{array}{c}{\mathbf{P}} \\ {\mathbf{1}_{a}}\end{array}\right]\right)+\alpha V\left(\left[\begin{array}{c}{\mathbf{P}} \\ {\mathbf{1}_{a}}\end{array}\right]\right) +C_{p u c t}\left((1-\epsilon) \boldsymbol{\pi}_{a}+\epsilon \boldsymbol{\delta}\right) \sqrt{\frac{N(\mathbf{P})}{N\left(\left[\begin{array}{c}{\mathbf{P}} \\ {\mathbf{1}_{a}}\end{array}\right]\right)}}" border="0"/>

3. 蒙特卡洛模拟的过程

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%883.30.43.png"/>

4. 整体的训练过程示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%883.31.18.png"/>

注意：MCST 获得的只是一个训练样本，对于 ReconNet 来说是输入，对于 SampleNet 来说是 target

两个网络的示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%882.42.55.png"/>

测试时的重建过程示意图：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%881.42.50.png" width="60%"/>

算法汇总：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-31%E4%B8%8B%E5%8D%883.38.33.png"/>

### Bibtex:

```latex
@article{jin2019self,
  title={Self-supervised deep active accelerated MRI},
  author={Jin, Kyong Hwan and Unser, Michael and Yi, Kwang Moo},
  journal={arXiv preprint arXiv:1901.04547},
  year={2019}
}
```


### Paper:

Bayesian Deep Learning for Accelerated MR Image Reconstruction

### Author:

Jo Schlemper, Daniel C. Castro, Wenjia Bai, Chen Qin, Ozan Oktay, Jinming Duan, Anthony N. Price, Jo Hajnal, and Daniel Rueckert

### Year:

2018

### Notes:

这篇文章，作者的视角是贝叶斯深度学习，主要想法是把两个贝叶斯深度学习中的技巧 MC dropout and heteroscedastic loss 结合到两种用于 MRI 重建的网络中。作者分析了深度学习方法中两个不确定性来源，一个是图像本身的不确定性 aleatoric uncertainty，不仅包括噪声，也包括图像内容；一个是网络参数的不确定性 epistemic uncertainty。通过结合两个技巧，作者可以分析 pixel 级别的不确定性和 error 之间的关系，设计相关实验。MC dropout 指的是对网络的连接随机丢弃，heteroscedastic loss 指的是损失函数中 pixel 的2范数有权重，权重是由该像素的不确定性（也就是方差决定）。

模型：$p\left(\mathbf{x} | \mathbf{x}_{u}, \mathbf{w}\right)=\mathcal{N}\left(\mathbf{x} | f^{\mathbf{w}}\left(\mathbf{x}_{u}\right), g^{\mathbf{w}}\left(\mathbf{x}_{u}\right)\right)$，$f^{\mathbf{w}}\left(\mathbf{x}_{u}\right)$ 表示经过 MC dropout 的平均结果。$g^{\mathbf{w}}\left(\mathbf{x}_{u}\right)$ 指的是预测的不确定性（只针对 pixel，而不是整个图像的协方差）。训练时，损失函数是 heteroscedastic loss：
$$
\mathcal{L}_{\text {Het. }}(\mathbf{w})=\frac{1}{N|\mathcal{D}|} \sum_{\left(\mathbf{x}_{u}, \mathbf{x}\right) \in \mathcal{D}} \sum_{i=1}^{N} \frac{1}{2 g_{i}^{\mathbf{w}}\left(\mathbf{x}_{u}\right)}\left\|\mathbf{x}_{i}-f_{i}^{\mathbf{w}}\left(\mathbf{x}_{u}\right)\right\|^{2}+\frac{1}{2} \log g_{i}^{\mathbf{w}}\left(\mathbf{x}_{u}\right)
$$
在测试时，通过计算多次 MC dropout 的结果来确定预测值和方差大小，也就是 $\mathbb{E}[\mathbf{x}] \approx \frac{1}{T} \sum_{t=1}^{T} f^{\mathbf{w}_{t}}\left(\mathbf{x}_{u}\right)$, $\mathbb{V}[\mathbf{x}] \approx \frac{1}{T} \sum_{t=1}^{T} g^{\mathbf{w}_{t}}\left(\mathbf{x}_{u}\right)+\frac{1}{T} \sum_{t=1}^{T}\left(f^{\mathbf{w}_{t}}\left(\mathbf{x}_{u}\right)\right)^{2}-\left(\frac{1}{T} \sum_{t=1}^{T} f^{\mathbf{w}_{t}}\left(\mathbf{x}_{u}\right)\right)^{\mathbf{z}}$. 对于复数的处理是：$\mathbb{V}[\mathbf{z}]=\mathbb{V}[\mathfrak{R}(\mathbf{z})]+\mathbb{V}[\mathfrak{I}(\mathbf{z})]$

网络结构：基本结构是 DC-CNN (Schlemper, J., Caballero, J., Hajnal, J.V., Price, A., Rueckert, D.: A deep cascade of convolutional neural networks for dynamic mr image reconstruction. IEEE Transactions on Medical Imaging 37 (2017)) 和 Unet (Han, Y., Yoo, J., Kim, H.H., Shin, H.J., Sung, K., Ye, J.C.: Deep learning with domain adaptation for accelerated projection-reconstruction MR. Magnetic resonance in medicine 80(3), 1189–1205 (2018)). 设计 $g^{\mathbf{w}}$ 的方式，对于 DC-CNN 由两种，一种是独立的一个网络，一种是作为 $f^{\mathbf{w}}$ 网络的另一个输出。对于Unet，使用相同的  Encoder，不同的Decoder。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2020-01-02%E4%B8%8B%E5%8D%883.53.27.png" style="zoom:50%;" />

实验的内容：

- 比较不同的加速情况
- 比较不同的采样方式
- 比较不同的数据集的泛化性

训练样本4000，测试样本1000和另一个数据集。采样方式由 1维笛卡尔，径向，低频。数据增广有平移、放缩、旋转。MC dropout 的数量是20次，0.2 的概率丢弃连接。Adam with $α$ = 10 −4 , $β_1$  = 0.9, $β_2$ = 0.999, $\alpha$ 每100次epoch✖️0.1.

从结果上看，DC-CNN（表现最好） 的贝叶斯版本效果变差，Unet 的贝叶斯版本效果变好。模型的不确定性和误差有关，但是误差大的地方并不是不确定性最高的。对于来源图像的不确定性，主要集中在边界上。当加速程度增加，不确定性变大。但总体来说，论文里对实验结果的叙述很不完整。

### Bibtex:

```latex
@inproceedings{schlemper2018bayesian,
  title={Bayesian deep learning for accelerated MR image reconstruction},
  author={Schlemper, Jo and Castro, Daniel C and Bai, Wenjia and Qin, Chen and Oktay, Ozan and Duan, Jinming and Price, Anthony N and Hajnal, Jo and Rueckert, Daniel},
  booktitle={International Workshop on Machine Learning for Medical Image Reconstruction},
  pages={64--71},
  year={2018},
  organization={Springer}
}
```
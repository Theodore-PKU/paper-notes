### Paper:

Image Reconstruction by Splitting Deep Learning Regularization from Iterative Inversion

### Author:

Jiulong Liu, Tao Kuang, and Xiaoqun Zhang

### Year:

2018

### Notes:

阅读日期：2020.02.10

泛读。这篇文章研究的是图像重建，具体的实验对象是 MRI，不过作者表示也可以用于其他重建的问题。作者提出的是一种计算的框架，其中涉及到神经网络，但是不讨论神经网络具体如何设计。网络在这里的作用相当于是图像表示的一种 prior，或者说重建的图像应该是和网络的输出非常相关的。作者考虑了自编码和 GAN 两种网络，对应的损失函数就略有不同。作者建构的模型是网络+data consistency+稀疏项，训练和测试都是使用 ADMM 算法，不过，训练时会训练网络参数，而测试的时候，网络参数时固定的，所以两个算法不完全一样。比较疑惑的是，在训练时，网络参数是如何训练，因为有很多样本，在优化 ADMM 的子问题的时候，对于网络参数的训练是否是迭代很多次？

自编码网络的损失函数：

<img src="http://latex.codecogs.com/svg.latex? \min _{\theta} L_{H}(\boldsymbol{x}, \theta):=\|f(\boldsymbol{x}, \theta)-\tilde{\boldsymbol{x}}\|_{2}^{2}" border="0"/>

GAN 的损失函数：

<img src="http://latex.codecogs.com/svg.latex? \max _{\theta_{d}} \min _{\theta_{g}} L_{G}\left(\boldsymbol{x}, \theta_{g}, \theta_{d}\right)=\frac{1}{m} \sum_{i=1}^{m}\left[\log \left(D\left(\tilde{\boldsymbol{x}}_{i}, \theta_{d}\right)\right)\right]+\frac{1}{m} \sum_{i=1}^{m}\left[\log \left(1-D\left(G\left(\boldsymbol{x}_{i}, \theta_{g}\right), \theta_{d}\right)\right)\right]" border="0"/>

传统的目标函数，也就是 data consistency 和稀疏项：

<img src="http://latex.codecogs.com/svg.latex? J(\boldsymbol{x})=\eta\left(\sum_{i=1}^{m}\left\|A \boldsymbol{x}_{i}-\boldsymbol{y}_{i}\right\|_{2}^{2}+\mu\left\|\mathcal{D} \boldsymbol{x}_{i}\right\|_{1}\right)" border="0"/>

总的损失函数就是网络的损失函数和传统目标函数的结合。此时目标函数（以自编码网络的例子）就是

<img src="http://latex.codecogs.com/svg.latex? \min _{\boldsymbol{x}, \theta} L_{H}(\boldsymbol{x}, \theta)+J(\boldsymbol{x})" border="0"/>

作者训练网络不是单独训练，而是结合到目标函数中，使用的方法是 ADMM 算法：

<img src="http://latex.codecogs.com/svg.latex? \begin{array}{l}{\min _{\boldsymbol{x}, \theta, \boldsymbol{z}} L_{H}(\boldsymbol{x}, \theta)+J(\boldsymbol{z})} \\ {\text { s.t. } \quad \boldsymbol{x}=\boldsymbol{z}}\end{array}" border="0"/>

迭代算法是：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{\boldsymbol{z}^{k+1}=\arg \min _{\boldsymbol{z}} J(\boldsymbol{z})+\frac{\rho}{2}\left\|\boldsymbol{x}^{k}-\boldsymbol{z}+\boldsymbol{b}^{k}\right\|_{2}^{2}} \\ {\theta^{k+1}=\arg \min _{\theta} L_{H}\left(\boldsymbol{x}^{k}, \theta\right)} \\ {\boldsymbol{x}^{k+1}=\arg \min _{\boldsymbol{x}} L_{H}\left(\boldsymbol{x}, \theta^{k+1}\right)+\frac{\rho}{2}\left\|\boldsymbol{x}-\boldsymbol{z}^{k+1}+\boldsymbol{b}^{k}\right\|_{2}^{2}} \\ {\boldsymbol{b}^{k+1}=\boldsymbol{b}^{k}+\left(\boldsymbol{x}^{k+1}-\boldsymbol{z}^{k+1}\right)}\end{array}\right." border="0"/>

初始化：

<img src="http://latex.codecogs.com/svg.latex? \boldsymbol{x}^{0}=\boldsymbol{z}^{0}=\arg \min _{\boldsymbol{z}} J(\boldsymbol{z})" border="0"/>

第一个子问题可以也可以用 ADMM 求解，第二个子问题就是网络优化，第三项则是对 input 优化。

在训练完网络后，测试时的 ADMM 算法就是：

<img src="http://latex.codecogs.com/svg.latex? \left\{\begin{array}{l}{z^{k+1}=\arg \min _{z} J(z)+\frac{\rho}{2}\left\|x^{k}-z+b^{k}\right\|_{2}^{2}} \\ {\hat{x}^{k+1}=f\left(x^{k}, \theta^{*}\right)} \\ {x^{k+1}=\frac{1}{1+\rho}\left(\hat{x}^{k+1}+\rho\left(z^{k+1}-b^{k}\right)\right)} \\ {b^{k+1}=b^{k}+\left(\hat{x}^{k+1}-z^{k+1}\right)}\end{array}\right." border="0"/>

### Bibtex:

```latex
@inproceedings{liu2018image,
  title={Image reconstruction by splitting deep learning regularization from iterative inversion},
  author={Liu, Jiulong and Kuang, Tao and Zhang, Xiaoqun},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={224--231},
  year={2018},
  organization={Springer}
}
```


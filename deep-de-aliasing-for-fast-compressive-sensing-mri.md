### Paper:

Deep De-Aliasing for Fast Compressive Sensing MRI

### Author:

Simiao Yu , Hao Dong, Guang Yang, Greg Slabaugh, Pier Luigi Dragotti, Xujiong Ye, Fangde Liu, Simon Arridge, Jennifer Keegan, David Firmin, and Yike Guo

### Year:

2017

### Notes:

这篇文章解决的问题是压缩感知的MRI重建。使用的方法是条件GAN。主要的贡献在于使用了Unet的结构，损失函数除了用了对抗loss，还有图像域的mse loss，vgg 的perceptual loss，以及网络的输出是残差（也就是说网络输出和输入的和才是生成的干净图像，这是文中所谓的 refinement learning）。

在论文中，作者提到了已经有人尝试将生成网络作为图像的正则项，放到传统的CS-MRI的框架中进行重建：
$$
\min _{\mathbf{x}} \frac{1}{2}\left\|\mathbf{F}_{u} \mathbf{x}-\mathbf{y}\right\|_{2}^{2}+\lambda \mathcal{R}(\mathbf{x})+\zeta\left\|\mathbf{x}-f_{\operatorname{cnn}}\left(\mathbf{x}_{u} | \hat{\theta}\right)\right\|_{2}^{2}
$$
需要说明的是 perceptual loss 指的是图像传入一个VGG net 之后某一层（文中选了第四个卷积层）的输出作为一个特征表示，然后计算生成图像的特征和真实图像的特征。

对抗loss：
$$
\min _{\theta_{G}} \max _{\theta_{D}} \mathbb{E}_{\mathbf{x}_{t} \sim p_{\text {train }}\left(\mathbf{x}_{t}\right)}\left[\log D_{\theta_{D}}\left(\mathbf{x}_{t}\right)\right]+\mathbb{E}_{\mathbf{x}_{u} \sim p_{G}\left(\mathbf{x}_{u}\right)}\left[\log \left(1-D_{\theta_{D}}\left(G_{\theta_{G}}\left(\mathbf{x}_{u}\right)\right)\right)\right]
$$
其他loss：
$$
\begin{array}{cl}
{\min _{\theta_{G}}} & {\alpha \frac{1}{2}\left\|\mathrm{x}_{t}-\hat{\mathrm{x}}_{u}\right\|_{2}^{2}+\beta \frac{1}{2}\left\|f_{\mathrm{vgg}}\left(\mathrm{x}_{t}\right)-f_{\mathrm{vgg}}\left(\hat{\mathrm{x}}_{u}\right)\right\|_{2}^{2}+\log \left(1-D_{\theta_{D}}\left(\hat{\mathrm{x}}_{u}\right)\right)} \\
{\text { s.t. }} & {\hat{\mathrm{x}}_{u}=G_{\theta_{G}}\left(\mathrm{x}_{u}\right)}
\end{array}
$$
网络结构：

![](https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-24%E4%B8%8B%E5%8D%8810.55.01.png)

关于实验，作者做的也比较多，也比较了自己提出的方法的各个loss的效果（对比实验）

### Bibtex:

```latex
@article{yu2017deep,
  title={Deep de-aliasing for fast compressive sensing MRI},
  author={Yu, Simiao and Dong, Hao and Yang, Guang and Slabaugh, Greg and Dragotti, Pier Luigi and Ye, Xujiong and Liu, Fangde and Arridge, Simon and Keegan, Jennifer and Firmin, David and others},
  journal={arXiv preprint arXiv:1705.07137},
  year={2017}
}
```
### Paper:

CT Image Denoising with Perceptive Deep Neural Networks

### Author:

Qingsong Yang, Pingkun Yan, Mannudeep K. Kalra, and Ge Wang

### Year:

2017

### Notes:

这一篇文章是关于 perceptual loss (应该是第一个提出这种loss的)。作者认为传统的 mse loss 不能很好地衡量图像质量，会过度平滑，损失对人类视觉重要的图像特征。作者的具体做法就是，将去噪后的图片和真实值在预训练的VGG网络中抽取的特征作为比较的对象（还是欧式距离），假设特征图为 $h \times w \times d$ 大小，则损失函函数为：
$$
\mathcal{L}_{\phi_{i}}\left(\hat{I}, I_{g t}\right)=\frac{1}{h w d}\left\|\phi_{i}(\hat{I})-\phi_{i}\left(I_{g t}\right)\right\|^{2}
$$
作者使用的网络结构，去噪部分很简单，整体的 data flow 也很简洁。作者的实验结果（可以选择不同的层的 perceptual loss），因为还是用 psnr，ssim 等指标，所以并没有看出明显的优势。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-28%E4%B8%8B%E5%8D%888.23.18.png" style="zoom:50%;" />

### Bibtex:

```latex
@article{yang2017ct,
  title={CT image denoising with perceptive deep neural networks},
  author={Yang, Qingsong and Yan, Pingkun and Kalra, Mannudeep K and Wang, Ge},
  journal={arXiv preprint arXiv:1702.07019},
  year={2017}
}
```
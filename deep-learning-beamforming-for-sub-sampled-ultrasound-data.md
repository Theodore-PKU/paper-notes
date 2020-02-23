### Paper:

Deep Learning Beamforming for Sub-Sampled Ultrasound Data

### Author:

Walter Simson, Magdalini Paschali, Nassir Navab, Guillaume Zahnd

### Year:

2018

### Notes:

泛读。这篇文章是关于超声重建的。比较有用的信息是超声重建问题和MRI重建有一些相似之处，都是因为扫描时间比较长所以需要减少采样的次数。作者认为每一个 lines（超声采样的结果是图像的列）之间有语意的相关性，所以使用深度学习的方法来学习这种相关性。因此针对的问题 就是在较少采样lines 的情况下重建图像。关于网络架构，作者只提到了encoder，decoder，BN层，dropout，以及skip connection，很不详细。另外作者使用的损失函数值的提一下，包含两个部分，也是图像域的1范数损失函数，一个是不同尺度的SSIM。

一般的SSIM可以写成这个形式：
$$
\mathcal{L}_{\mathrm{SSIM}}=1-\frac{2 \mu_{x} \mu_{y}+c_{1}}{\mu_{x}^{2}+\mu_{y}^{2}+c_{1}} \cdot \frac{2 \sigma_{x y}+c_{2}}{\sigma_{x}^{2}+\sigma_{y}^{2}+c_{2}}=1-l(p) \cdot c s(p)
$$
作者所谓的多尺度的SSIM，就是在一个图像金字塔中计算不同的 $cs(p)$
$$
\mathcal{L}_{\mathrm{MS}-\mathrm{SSIM}}=1-l_{M}(p) \cdot \prod_{j=1}^{M} c s_{j}(p)
$$
网络结构（作者放的图）：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-31%E4%B8%8B%E5%8D%889.31.45.png" style="zoom: 33%;" />

### Bibtex:

```latex
@inproceedings{simson2018deep,
  title={Deep learning beamforming for sub-sampled ultrasound data},
  author={Simson, Walter and Paschali, Magdalini and Navab, Nassir and Zahnd, Guillaume},
  booktitle={2018 IEEE International Ultrasonics Symposium (IUS)},
  pages={1--4},
  year={2018},
  organization={IEEE}
}
```
### Paper:

Content-Adaptive Image Compressed Sensing Using Deep Learning

### Author:

Liqun Zhong, Shuai Wan, Leyi Xie, Shun Zhang

### Year:

2018

### Notes:

阅读日期：2020.01.10

泛读。这篇文章解决的问题是压缩感知的测量值数量问题。作者的思路是基于 block CS，对于不同的block，分配不同数量的测量值。之前有一篇文章是根据 patch 图像的像素分布的方差来表示对应的复杂度，这一篇更直接，用重建的mse来判断。作者先训练好51个模型，每个模型对应不同数量的测量值的重建（方法是数层深度学习），测量值数量是 5，10，15，...，255。先给每个 patch 分配5个测量值的数量，然后计算哪一个patch的mse和测量值数量最相关，也就是增加相同的测量值，mse 变化最大。找到最佳patch之后，给这个patch增加5个测量值数量。重复该步骤知道测量值数量总数达到限定值。虽然从作者给出的效果上看，非常好，psnr值非常高，但总的来说，这种做法没有什么实际意义，运行时间会很长。

### Bibtex:

```latex
@inproceedings{zhong2018content,
  title={Content-Adaptive Image Compressed Sensing Using Deep Learning},
  author={Zhong, Liqun and Wan, Shuai and Xie, Leyi and Zhang, Shun},
  booktitle={2018 Asia-Pacific Signal and Information Processing Association Annual Summit and Conference (APSIPA ASC)},
  pages={57--61},
  year={2018},
  organization={IEEE}
}
```
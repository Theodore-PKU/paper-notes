### Paper:

Penalized-likelihood PET Image Reconstruction Using 3D Structural Convolutional Sparse Coding

### Author:

Nuobei Xie, Kuang Gong, Ning Guo, Zhixin Qin, Zhifang Wu, Huafeng Liu, Quanzheng Li

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 重建，但是没有使用深度学习的方法，而是 CSC 的方法。作者表示这是第一次将 CSC 用于 PET 重建。CSC 方法和字典学习很像，只不过使用了卷积，因此处理对象是整幅图像。不过在这篇文章中，因为处理的是 3D 的 PET 图像，所以在计算时，作者还是转化为了 block 的计算。优化的目标函数就是对数似然函数 + CSC 表示的正则项。优化时，整体上分成两个部分，正则项部分又划分为多个子任务。需要说明的是，卷积的参数是用 3D MRI 训练的。因为不是深度学习方法，所以就不参考了。

### Bibtex:

```latex
@article{xie2019penalized,
  title={Penalized-likelihood PET Image Reconstruction Using 3D Structural Convolutional Sparse Coding},
  author={Xie, Nuobei and Gong, Kuang and Guo, Ning and Qin, Zhixin and Wu, Zhifang and Liu, Huafeng and Li, Quanzheng},
  journal={arXiv preprint arXiv:1912.07180},
  year={2019}
}
```

### 其他


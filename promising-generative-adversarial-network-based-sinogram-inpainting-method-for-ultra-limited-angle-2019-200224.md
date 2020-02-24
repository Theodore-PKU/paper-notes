### Paper:

Promising Generative Adversarial Network Based Sinogram Inpainting Method for Ultra-Limited-Angle Computed Tomography Imaging

### Author:

Ziheng Li , Ailong Cai, Linyuan Wang, Wenkun Zhang, Chao Tang, Lei Li, Ningning Liang and Bin Yan

### Year:

2019

### Notes:

阅读日期：2020.02.24

泛读。这篇文章研究的是 CT 的有限角重建。使用的方法是 GAN，不过这篇文章尝试对 sinogram 进行 inpaiting（这个方法并不是首创，Zhao, J.; Chen, Z.; Zhang, L.; Jin, X. Unsupervised Learnable Sinogram Inpainting Network (SIN) for Limited Angle CT reconstruction，这篇文章更早）。对于有限角，sinogram 不是完整的，所以作者用 GAN 来实现 sinogram 的 inpainting，之后再用 FBP 算法得到重建的图像。损失函数包括对抗loss，sinogram loss 和 image loss。生成网络是 U-net，判别器网络的输入是生成器输出的完整 sinogram 和有限角的 sinogram（对于 real sample，不知道完整的是不是就是真实的 sinogram）。感觉没有太多的参考价值（MRI 的 kspace 也可以这么搞，和 RAKI 算法就很类似了，只不过用的神经网络不同）

 <img src="https://cdn.mathpix.com/snip/images/JPTJHQY4D58_Cm73sPQrcqokh2a-wFlJiWTperajYJs.original.fullsize.png" />

<img src="https://cdn.mathpix.com/snip/images/oMluIYdHzABn1syd1puW5aY7yEbNGOEvLplWDjCXsG8.original.fullsize.png" />

### Bibtex:

```latex
@article{li2019promising,
  title={Promising Generative Adversarial Network Based Sinogram Inpainting Method for Ultra-Limited-Angle Computed Tomography Imaging},
  author={Li, Ziheng and Cai, Ailong and Wang, Linyuan and Zhang, Wenkun and Tang, Chao and Li, Lei and Liang, Ningning and Yan, Bin},
  journal={Sensors},
  volume={19},
  number={18},
  pages={3941},
  year={2019},
  publisher={Multidisciplinary Digital Publishing Institute}
}
```


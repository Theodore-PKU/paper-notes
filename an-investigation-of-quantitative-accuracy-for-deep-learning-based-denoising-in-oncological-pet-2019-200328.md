### Paper:

An investigation of quantitative accuracy for deep learning based denoising in oncological PET

### Author:

Wenzhuo Lu, John A Onofrey, Yihuan Lu, Luyao Shi, Tianyu Ma, Yaqiang Liu and Chi Liu

### Year:

2019

### Notes:

泛读。这篇文章研究的是 PET 的重建，不过作者还关注的是 lung nodule 这个生理上的图像显示效果。整体上说，这篇文章主要是实验性质。很多内容和实验有关。主要有三种网络，CAE，U-net，和 U-net 为生成网络的 GAN，基于 patch 的训练和去噪。方法归类上属于 CNN 投影后处理的类型。作者还比较了 2D U-net，2.5D U-net（输入是 3 slice），传统去噪算法，带 residual 结构的 U-net 等。最终得出的结果大概是 GAN/3D U-net 比较好。

网络结构看图就可以了。

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/20200328151841.png"/>

### Bibtex:

```latex
@article{lu2019investigation,
  title={An investigation of quantitative accuracy for deep learning based denoising in oncological PET},
  author={Lu, Wenzhuo and Onofrey, John A and Lu, Yihuan and Shi, Luyao and Ma, Tianyu and Liu, Yaqiang and Liu, Chi},
  journal={Physics in Medicine \& Biology},
  volume={64},
  number={16},
  pages={165019},
  year={2019},
  publisher={IOP Publishing}
}
```

### 其他

Nie D, Trullo R, Lian J, Wang L, Petitjean C, Ruan S, Wang Q and Shen D 2018 Medical image synthesis with deep convolutional adversarial networks IEEE Trans. Biomed. Eng. 65 2720–30

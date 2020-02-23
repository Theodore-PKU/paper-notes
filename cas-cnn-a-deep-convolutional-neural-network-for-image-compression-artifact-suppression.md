### Paper:

CAS-CNN: A Deep Convolutional Neural Network for Image Compression Artifact Suppression

### Author:

Lukas Cavigelli, Pascal Hager, Luca Benini

### Year:

2017

### Notes:

这篇文章解决的问题是如果将有损压缩的图像变得质量更高（去压缩伪影），和压缩感知的重建问题差别较大，但是有一定相似之处。作者的做法主要是网络设计上的，所以只看网络设计的部分，实验及结果部分没有看。

网络结构整体上类似于Unet，有不同尺度的信息的传递。先下采样然后上采样。上采样的时候会用一个卷积层来生成一个当前分辨率下的图像，这个图像和真实图像放缩到相应分辨率的图像做mse loss 来帮助训练。这样以来，一共有两种loss，一种是最终图像的loss，一种是中间生成的低分辨率图像。训练时，一开始这两种loss都训练，当loss收敛后，只训练最终图像的mse loss进行微调。另外生成的低分辨率图像也会作为网络的中间层的输入的一部分。结构如下所示：

<img src="https://raw.githubusercontent.com/Theodore-PKU/pictures/master/%E6%88%AA%E5%B1%8F2019-12-26%E4%B8%8A%E5%8D%8810.37.38.png" style="zoom:50%;" />

### Bibtex:

```latex
@inproceedings{cavigelli2017cas,
  title={CAS-CNN: A deep convolutional neural network for image compression artifact suppression},
  author={Cavigelli, Lukas and Hager, Pascal and Benini, Luca},
  booktitle={2017 International Joint Conference on Neural Networks (IJCNN)},
  pages={752--759},
  year={2017},
  organization={IEEE}
}
```
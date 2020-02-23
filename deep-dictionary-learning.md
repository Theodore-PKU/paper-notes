### Paper:

Deep Dictionary Learning

### Author:

SNIGDHA TARIYAL, ANGSHUL MAJUMDAR, (Senior Member, IEEE), RICHA SINGH, (Senior Member, IEEE), and MAYANK VATSA, (Senior Member, IEEE)

### Year:

2016

### Notes:

深度学习和字典学习的结合。模型：
$$
\min _{D_{1}, \ldots D_{N}, Z}\left\|X-D_{1} \varphi\left(D_{2} \varphi\left(\ldots \varphi\left(D_{N} Z\right)\right)\right)\right\|_{F}^{2}+\mu\|Z\|_{1}
$$
激活函数也可以是线性的。

只有最后一层（最后一次分解，考虑稀疏性）。训练时是greedy的方式。逐层训练。测试时只需要极小化code Z。

一些好的观点：1. 字典学习就是矩阵分解。2. 单层分解和多次矩阵分解是不一样的。自编码器也是某一种字典。

实验内容：仍然是把Z当作是一种表示，以此表示用SVM或KNN等方法来测试表示的效果。

思考：生成模型和这些模型都差不多，不过训练方式不一样。

### Bibtex:

```latex
@article{tariyal2016deep,
  title={Deep dictionary learning},
  author={Tariyal, Snigdha and Majumdar, Angshul and Singh, Richa and Vatsa, Mayank},
  journal={IEEE Access},
  volume={4},
  pages={10096--10109},
  year={2016},
  publisher={Ieee}
}
```


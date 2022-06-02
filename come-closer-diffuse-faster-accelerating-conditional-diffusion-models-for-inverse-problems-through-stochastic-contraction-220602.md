# Come-Closer-Diffuse-Faster: Accelerating Conditional Diffusion Models for Inverse Problems through Stochastic Contraction

[TOC]

## 信息

作者：Hyungjin Chung, Byeongsu Sim, Jong Chul Ye

期刊会议年份：CVPR 2022

Bibtex：

```latex
@article{chung2021come,
  title={Come-Closer-Diffuse-Faster: Accelerating Conditional Diffusion Models for Inverse Problems through Stochastic Contraction},
  author={Chung, Hyungjin and Sim, Byeongsu and Ye, Jong Chul},
  journal={arXiv preprint arXiv:2112.05146},
  year={2021}
}
```



## 概括

精读。

这篇文章的考虑的是 inverse problem 中使用 unconditional diffusion model 和 data consistency 结合的 conditional diffusion model 的加速采样的策略。基本想法是通过提供一个更好的 reverse process 的初值来实现生成，而不是从完全的高斯噪声出发。作者还提到这个方法和此前的 inverse problem 的一些方法可以结合起来。作者用了很大篇幅对这种加速策略进行了理论分析。



## 方法

用了什么方法解决问题。

加速采样的基本想法是：从一个不太准确的初始值开始，先进行一个 diffusion 的过程，然后从这个时刻（小于完整的 unconditional 采样的 T）开始进行 reverse process 生成。这个生成过程和原本的 sampling + DC 一样。初始值可以是 corrupted image，也可以是用一个神经网络得到的初始值。作者认为初始值越接近 true data，那么效果越好，reverse process 开始的时刻更小（也就是迭代的步数更少）。这个想法和 SDEdit 非常接近，但是考虑的问题有一些差异。这篇文章是从加速采样的角度出发，SDEdit 则是从图像转换的角度出发。另外，SDEdit 中，reverse process 的初始时刻相对来说更大，这是和对应的任务有关的，而在这篇文章中，初始时刻可以非常小。

如果用一个图来表示作者的想法，那么用下图可以很好地说明：

![屏幕快照 2022-05-11 上午1.00.23](/Users/xieyutong/Pictures/screenshot/屏幕快照 2022-05-11 上午1.00.23.png)

我认为作者的想法是正确的。但是作者的理论推导存在很大的问题。首先，从某些极端的例子可以发现一些错误。其次，从作者的推导中，我们也发现了一些问题。我的猜测是应该有一个更准确的理论分析结果，而不是作者现在给出的（定理二）。

下面，我们主要从理论推导的角度对这篇文章的基本想法进行分析。

首先，对于一个扩散模型，其 SDE 形式为：
$$
d \boldsymbol{x}=\boldsymbol{f}(\boldsymbol{x}, t) d t+g(t) d \boldsymbol{w}
$$
可以定义如下的扩散过程：
$$
\boldsymbol{x}_{N^{\prime}}=a_{N^{\prime}} \boldsymbol{x}_{0}+b_{N^{\prime}} \boldsymbol{z}, \boldsymbol{z} \sim \mathcal{N}(\mathbf{0}, \boldsymbol{I})
$$
这里的 $N^{\prime}$ 表示某个中间时刻，$N$ 表示 T 时刻。

作者定义的 sample + DC 的 inference 算法可以表示为：
$$
\begin{aligned}
&\boldsymbol{x}_{i-1}^{\prime}=\boldsymbol{f}\left(\boldsymbol{x}_{i}, i\right)+g\left(\boldsymbol{x}_{i}, i\right) \boldsymbol{z}_{i} \\
&\boldsymbol{x}_{i-1}=\boldsymbol{A} \boldsymbol{x}_{i-1}^{\prime}+\boldsymbol{b}
\end{aligned}
$$
这里的 $f$ 和 $g$ 的定义已经和 SDE 的不一样了，是 reverse process 的定义。另外，作者还假设了 $A$ 是一个 non-expansive mapping 满足：
$$
\left\|\boldsymbol{A} \boldsymbol{x}-\boldsymbol{A} \boldsymbol{x}^{\prime}\right\| \leq\left\|\boldsymbol{x}-\boldsymbol{x}^{\prime}\right\|, \quad \forall \boldsymbol{x}, \boldsymbol{x}^{\prime}
$$
作者假设这是一个线性映射。这个 non-expansive mapping 条件是有用的。

作者首先给出了一个引理。这个引理很简单。

Lemma 1. Let $\tilde{\boldsymbol{x}}_{0} \in \mathbb{R}^{n}$ and $\boldsymbol{x}_{0} \in \mathbb{R}^{n}$ be the groundtruth clean image and its initial estimate, respectively, and the initial estimation error is denoted by $\varepsilon_{0}=\left\|\boldsymbol{x}_{0}-\tilde{\boldsymbol{x}}_{0}\right\|^{2}$. Suppose, furthermore, that $\boldsymbol{x}_{N^{\prime}}$ and $\tilde{\boldsymbol{x}}_{N^{\prime}}$ denote the forward diffusion from $\boldsymbol{x}_{0}$ and $\tilde{\boldsymbol{x}}_{0}$, respectively, using (11). Then, the estimation error after the forward diffusion is given by
$$
\begin{aligned}
\bar{\varepsilon}_{N^{\prime}} &:=\mathbb{E}\left\|\boldsymbol{x}_{N^{\prime}}-\tilde{\boldsymbol{x}}_{N^{\prime}}\right\|^{2} \\
&=a_{N^{\prime}}^{2} \varepsilon_{0}+2 b_{N^{\prime}}^{2} n
\end{aligned}
$$
接下来，作者给出了定理一，并给出了不同 diffusion 模型的结论。

<img src="/Users/xieyutong/Pictures/screenshot/屏幕快照 2022-05-11 上午1.09.45.png" alt="屏幕快照 2022-05-11 上午1.09.45" style="zoom:50%;" />

核心式子是：
$$
\bar{\varepsilon}_{0, r} \leq \frac{2 C{\tau}}{1-\lambda^{2}}+\lambda^{2 N^{\prime} \bar{\varepsilon}_{N^{\prime}}}
$$
然后作者给出了定理二：

Theorem 2 (Shortcut path). For any $0<\mu \leq 1$, there exists a minimum $N^{\prime}\left(=t_{0} N\right)$ such that $\bar{\varepsilon}_{0, r} \leq \mu \varepsilon_{0}$. Furthermore, $N^{\prime}$ decreases as $\varepsilon_{0}$ gets smaller. 

这个定理是最终作为作者提出的想法是有效的理论保证。这个定理似乎想说，如果要生成地更好，尽可能地使用更大的 $N^{\prime}$。在给定精度的情况下，如果 $\varepsilon_0$ 越小，那么 $N^{\prime}$ 可以小一些。

个人认为，定理一和定理二都有问题，但是定理二的问题更大一些。特别是定理二，作者试图通过这个定理来说明，总是存在一个 $N^{\prime}$ 使得 reverse process 的结果会比初始的误差小很多。因此作者提出的这种方法就是有效的。但是只要考虑一个极端情况，就会发现定理二存在问题。首先，我们假设 sample + DC 的方法实现了 inverse problem 的后验分布的采样。这个假设本身就包含了一个含义，只要 T 时刻的初始值不同，几乎不可能生成完全相同的结果，因为整个过程都是随机的。但是这些生成的结果应该满足后验分布。所以是局限在一个很小的范围内。那么考虑 $\varepsilon_0 = 0$ 的情形，相同的初始值，施加不同的噪声得到不同的 $x_{N^{\prime}}$ 时，不论 $N^{\prime}$ 是什么值，重新经过 reverse process 之后，都不应该得到相同的生成结果。当然如果 $N^{\prime}$ 越小，生成结果的差异也应该越小，但是不可能等于 0。所以定理二在这种情况下就不成立。因为 $\bar{\varepsilon}_{0, r} > 0$. 如果 $\varepsilon_0 > 0$，而且相当大。按照定理二的含义，应该是 $N^{\prime}$ 越大，$\bar{\varepsilon}_{0, r}$ 会越小，但是即使 $N^{\prime} = N$，最终的 $\bar{\varepsilon}_{0, r}$ 也不可能是 0. 作者这里似乎认为不论 T 时刻的随机噪声是什么值，最终都会收敛到同一个值。作者在论文中说：

Hence, it is unnecessary for us to start sampling from $t = T$. Rather, we can start from an arbitrary timestep $t = t, 0 < T$, and still converge faster to the same point that could be achieved when starting the sampling procedure at $t = T$.

然而这显然是不对的。

接下来，我们从定理一和定理二的整个推导流程来分析其中存在的问题。作者这里考虑了 DDPM，DDIM，SMLD。我们仅仅以 DDPM 举例。DDIM 比较特殊，因为生成的过程中不包含随机噪声。但是我觉得还是有问题。不过不管怎样，我们只用 DDPM 的例子。

首先，作者考虑了压缩映射的概念：

Definition 1 (Contraction on $\mathbb{R}^{n}$ ). A function $f: \mathbb{R}^{n} \mapsto \mathbb{R}^{n}$ is called a contraction mapping if there exists a real number $0 \leq \lambda<1$ such that for all $\boldsymbol{x}$ and $\boldsymbol{y}$ in $\mathbb{R}^{n}$
$$
\|f(x)-f(y)\| \leq \lambda\|\boldsymbol{x}-\boldsymbol{y}\|
$$
利用中值定理，只要
$$
\sigma_{\max }\left(\frac{\partial \boldsymbol{f}(\boldsymbol{x})}{\partial \boldsymbol{x}}\right) \leq \lambda<1
$$
成立，那么 $f(x)$ 就是压缩映射，系数是 $\lambda$. where $\sigma_{\max }(\boldsymbol{A})$ denotes the largest singular value of a matrix $\boldsymbol{A}$. 

定理一的本质是应用了压缩映射的性质。这里，给出一个更一般的定理 [22]。

Theorem A.1. [22] Consider the stochastic difference equation:
$$
\boldsymbol{x}_{i+1}=\boldsymbol{f}\left(\boldsymbol{x}_{i}, i\right)+g\left(\boldsymbol{x}_{i}, i\right) \boldsymbol{w}_{i}
$$
where $f(\cdot, i)$ is a $\mathbb{R}^{n} \mapsto \mathbb{R}^{n}$ function, $g(\cdot, i)$ is a $\mathbb{R}^{n} \times$ $\mathbb{N} \mapsto \mathbb{R}$ function for each $i \in \mathbb{N}$, and $\left\{\boldsymbol{w}_{i}, i=1,2, \cdots\right\}$ is a sequence of independent $n$-dimensional zero mean unit variance Gaussian noise vectors. Assume that the system satisfies the following two hypothesis:
(H1) The function $f(\cdot, i)$ is contracting with factor $\lambda$ in the sense of $(20)$ for all $i \in \mathbb{N}$.
(H2) $\operatorname{Tr}(g(\boldsymbol{x}, i) \boldsymbol{I} g(\boldsymbol{x}, i)) \leq C, \quad \forall \boldsymbol{x}, i$
Then, for two sample trajectory $\boldsymbol{x}_{i}$ and $\tilde{\boldsymbol{x}}_{i}$ that satisfies (21), we have
$$
\mathbb{E}\left\|\boldsymbol{x}_{i}-\tilde{\boldsymbol{x}}_{i}\right\|^{2} \leq \frac{2 C}{1-\lambda^{2}}+\lambda^{2 i} \mathbb{E}\left\|\boldsymbol{x}_{0}-\tilde{\boldsymbol{x}}_{0}\right\|^{2}
$$
我没有深究这个定理，看起来是比较合理的。而且，我觉得证明起来应该不难。

由于在 inverse problem 的问题上，还增加了一个 DC 的过程，所以，作者首先把 DC 也考虑进来，对上述定理进行了一个推广。这里就用到了 A 是 non-expansive mapping 的性质。推出的结论就是
$$
\mathbb{E}\left\|\boldsymbol{x}_{i}-\tilde{\boldsymbol{x}}_{i}\right\|^{2} \leq \frac{2 C \tau}{1-\lambda^{2}}+(\lambda)^{2 i} \mathbb{E}\left\|\boldsymbol{x}_{0}-\tilde{\boldsymbol{x}}_{0}\right\|^{2}
$$
where $\tau=\frac{\operatorname{Tr}\left(\boldsymbol{A}^{T} \boldsymbol{A}\right)}{n}$。

对于 reverse process，如果可以证明 $f$ 和 $g$ 满足假设就可以了。所以作者接下来给了证明。

DDPM 的 reverse process 是：
$$
\boldsymbol{x}_{i-1}=\frac{1}{\sqrt{\alpha_{i}}}\left(\boldsymbol{x}_{i}+\left(1-\alpha_{i}\right) \boldsymbol{s}_{\theta}\left(\boldsymbol{x}_{i}, i\right)\right)+\sqrt{\sigma_{i}} \boldsymbol{z}
$$
这里作者以为训练好的 $s_{\theta}$ 满足：
$$
s_{\theta}\left(\boldsymbol{x}_{i}, t\right)=\frac{\partial}{\partial \boldsymbol{x}_{i}} \log p_{0 i}\left(\boldsymbol{x}_{i} \mid \boldsymbol{x}_{0}\right)
$$
因为损失函数是看起来是在拟合噪声。但实际上，我们知道训练好的 $s_{\theta}$ 是 $\frac{\partial}{\partial \boldsymbol{x}_{i}} \log p_{0 i}\left(\boldsymbol{x}_{i}\right)$

这个式子 $\frac{\partial}{\partial \boldsymbol{x}_{i}} \log p_{0 i}\left(\boldsymbol{x}_{i} \mid \boldsymbol{x}_{0}\right)$ 很好计算 jacobian 矩阵，所以作者很快推出（在这个错误的认识下）DDPM 是一个压缩映射，且：
$$
\lambda=\max _{i \in\left[N^{\prime}\right]} \sqrt{\alpha_{i}}\left(\frac{1-\bar{\alpha}_{i-1}}{1-\bar{\alpha}_{i}}\right)<1
$$
$C$ 的证明很简单。略。有了这两个结果，那么就可以用 11 式作为结论了。也就是定理一。这里我觉得，虽然 $s_\theta$ 有问题，但是这个结论应该不差。因为 $\frac{\partial}{\partial \boldsymbol{x}_{i}} \log p_{0 i}\left(\boldsymbol{x}_{i} \mid \boldsymbol{x}_{0}\right)$ 和 $\frac{\partial}{\partial \boldsymbol{x}_{i}} \log p_{0 i}\left(\boldsymbol{x}_{i}\right)$ 的关系是已知的，应该可以推出类似的结果，只是形式上可能没有办法计算出 $\lambda$，因为这里和图像的分布有关系。

在定理二的证明中，作者首先将定理一的一个更强的结果给出来了。

For some of the proofs, we borrow more tight inequality to obtain the result. In fact, the inequality of stochastic contraction
$$
\bar{\varepsilon}_{0, r} \leq \frac{2 C_{\tau}}{1-\lambda^{2}}+\lambda^{2 N^{\prime}} \bar{\varepsilon}_{N^{\prime}}
$$
is a rough estimation of recursive inequality [22]
$$
\bar{\varepsilon}_{j-1, r} \leq \lambda_{j}^{2} \bar{\varepsilon}_{j, r}+2 C_{j} \tau
$$
where $\bar{\varepsilon}_{j, r}$ denotes the estimation error between reverse conditional diffusion path down to $j$. Accordingly, we have
$$
\bar{\varepsilon}_{0, r} \leq \bar{\varepsilon}_{N, r} \prod_{j=0}^{N} \lambda_{j}^{2}+\sum_{j=1}^{N}\left(2 C_{j} \tau \prod_{i=1}^{j-1} \lambda_{i}^{2}\right)
$$
实际上这个结论是定理一的本质，定理一只是进行了某种放缩。$\lambda_j, C_j$.

在定理二的证明中，作者的想法是从 17 式出发证明。首先假设了 $\boldsymbol{x}, \overline{\boldsymbol{x}} \in[0,1]^{n}$. Then, we have
$$
\varepsilon_{0}=\|\boldsymbol{x}-\tilde{\boldsymbol{x}}\|^{2} \leq n
$$
然后，作者设，存在 $N^{\prime}$ such that
$$
\begin{aligned}
&N^{\prime} \beta_{N^{\prime}} \geq 2 \log \left(\frac{4 n}{\mu \varepsilon_{0}}\right) \\
&N^{\prime} \beta_{N^{\prime}} \leq \frac{\mu \varepsilon_{0}}{4 n \tau}
\end{aligned}
$$
在这样的假设下，将 17式的两项进行放缩，推出 $\bar{\varepsilon}_{0, r} \leq \mu \varepsilon_0$. 但是 19 的假设明显有问题。因为当 $\varepsilon_0$ 越小，$2 \log \left(\frac{4 n}{\mu \varepsilon_{0}}\right) $ 越大，而 $\frac{\mu \varepsilon_{0}}{4 n \tau}$ 越小，给定 $\mu$, 必然存在 $\varepsilon_0$ 使得 19 式的假设不成立，特别是当 $\varepsilon_0$ 非常小的时候（而这也和刚才说得极端例子吻合）。显然后续的推导也就不正确了。当然，如果 $\varepsilon_0$ 比较大，19 式是可能成立的。

我个人的猜测是，定理二的形式会更复杂，$\bar{\varepsilon}_{0, r}$ 应该是同时和 $N^{\prime}, \varepsilon_0$ 都有关系。如果 $\varepsilon_0$ 很小，那么 $\bar{\varepsilon}_{0, r}$ 应该是随着 $N^{\prime}$ 的增加而增加，如果 $\varepsilon_0$ 很大，那么 $\bar{\varepsilon}_{0, r}$ 应该是随着 $N^{\prime}$ 的增加而减小。总之应该是一个更复杂的结论。

虽然定理一和定理二的证明都存在问题，但我认为这篇文章的基本想法是有一定的道理，只是需要进一步地修正。



## 实验

关于实验的内容和结果。

作者的实验分为三个部分：超分辨、inpainting 和 MRI 重建。除了 MRI 重建，其他两个任务都用 FID 来评价。MRI 是 PSNR。从实验结果来看，我认为是没有问题的，效果很不错，确实提高了 sampling 效率。

首先，如果没有特殊说明，都是使用神经网络得到的初始值进行加速采样。

MRI 用的数据集是 fastMRI，另外两个实验用的是 face image，FFHQ 和 AFHQ

超分辨：x4，x8，x16 三种 factor。VP-SDE 的 DDPM，用的是 IDDPM。先训练一个生成模型。对于初始值网络，作者使用 ESRGAN，每个 factor 训练一个网络（网络结构同 score function 的训练）。比较对象是 ESRGAN，ILVR，SR3. 作者使用的采样方法和 ILVR 一样，用的是这个策略的加速采样。这个加速采样的策略可以表示为
$$
\boldsymbol{P} \boldsymbol{x}^{\prime}:=\boldsymbol{h}_{D} * \boldsymbol{x}^{\prime} \\
\boldsymbol{x}_{i}=(\boldsymbol{I}-\boldsymbol{P}) \boldsymbol{x}_{i}^{\prime}+\hat{\boldsymbol{x}}_{i}, \\\hat{\boldsymbol{x}}_{i}=\sqrt{\bar{\alpha}_{i}} \hat{\boldsymbol{x}}_{0}+\sqrt{1-\bar{\alpha}_{i}} \boldsymbol{z}
$$
$\boldsymbol{h}_D$ 实际上是下采样 + 上采样算子。$\hat{\boldsymbol{x}}_0$ 是神经网络的初始值。这里其实有点奇怪。结论是加速采样甚至 FID 指标更好。

inpaiting：用的是和超分辨一样的 DDPM 生成模型，初始值网络用的其他方法，比较对象是 score-SDE 中的 inpaiting 方法。inpaiting 和超分辨的差别仅仅是 $\boldsymbol{P}$，inpaiting 就是已知的部分的像素对应的对角矩阵。

MRI：生成模型用的是 SMLD，比较对象是作者自己之前的 score based 重建方法。两者是相同的 score function 模型。初始值网络是 U-Net。加速采样策略是
$$
\boldsymbol{x}_{i}=\left(\boldsymbol{I}-\boldsymbol{F}^{-1} \boldsymbol{D} \boldsymbol{F}\right) \boldsymbol{x}_{i}^{\prime}+\boldsymbol{F}^{-1} \boldsymbol{D} \boldsymbol{y}
$$
$\boldsymbol{y}$ 是测量值。这里作者用的是自己之前提出的那种重建 MRI 的方法，生成模型是用 magnitude image 训练，但是用在 complex image 上。而且完整的采样方法用了 corrector。在 MRI 实验中，需要的步数相比之前会更少，仅仅 20 步就够了。

作者还尝试了 imageNet 上训练的 DDPM，用的是 guided DDPM 训练好的模型。但是这个实验没有放在正文里，我估计并不是特别好。



## 反思

修正理论，再提出新的想法。



## 参考文献

Chin-Wei Huang, Jae Hyun Lim, and Aaron Courville. A variational perspective on diffusion-based generative models and score matching. In Advances in Neural Information Processing Systems, 2021. 2, 3

Hiroshi Sasaki, Chris G Willcocks, and Toby P Breckon. UNIT-DDPM: Unpaired image translation with denoising diffusion probabilistic models. arXiv preprint arXiv:2104.05358, 2021. 2

Daniel Watson, Jonathan Ho, Mohammad Norouzi, and William Chan. Learning to efﬁciently sample from diffusion probabilistic models. arXiv preprint arXiv:2106.03802, 2021. 2

Eric Luhman and Troy Luhman. Knowledge distillation in iterative generative models for improved sampling speed. arXiv preprint arXiv:2101.02388, 2021. 2



## 其他事项

come-closer-diffuse-faster-accelerating-conditional-diffusion-models-for inverse-problems-through-stochastic-contraction-220602.md

- [ ] source git_note.sh [a/m/n/d] filename <new_filename>
- [ ] source git_note.sh a come-closer-diffuse-faster-accelerating-conditional-diffusion-models-for-inverse-problems-through-stochastic-contraction-220602.md
- [ ] /Users/xieyutong/Documents/Research/PaperReading/Notes/come-closer-diffuse-faster-accelerating-conditional-diffusion-models-for-inverse-problems-through-stochastic-contraction-220602.md
- [ ] https://github.com/Theodore-PKU/paper-notes/blob/master/come-closer-diffuse-faster-accelerating-conditional-diffusion-models-for-inverse-problems-through-stochastic-contraction-220602.md
- [ ] 将论文添加到 Mendeley 中，修改 detail 信息（特别是 Tags），将笔记中概括的内容复制到 note 部分。 detail 中在 URL 部分增加笔记链接
- [ ] file:///Users/xieyutong/Documents/Research/PaperReading/Notes/come-closer-diffuse-faster-accelerating-conditional-diffusion-models-for-inverse-problems-through-stochastic-contraction-220602.md
- [ ] 放入 Mendeley 中正确的文件夹

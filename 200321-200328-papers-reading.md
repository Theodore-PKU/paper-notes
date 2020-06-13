# 2003021-200328 论文阅读总结

[TOC]

本周一共读了 45 篇文章。

## 论文列表

1. [A U-Nets Cascade for Sparse View Computed Tomography](https://github.com/Theodore-PKU/paper-notes/blob/master/a-u-net-cascade-for-sparse-view-computed-tomography-2018-200321.md): CT, unroll network, dc layer, compare index
2. [APIR-Net: Autocalibrated Parallel Imaging Reconstruction using a Neural Network](https://github.com/Theodore-PKU/paper-notes/blob/master/apir-net-autocalibrated-parallel-imaging-reconstruction-using-a-neural-network-2019-200321.md): MRI, parallel imaging, kspace, CNN, unsupervised, training strategy
3. Application of a k-Space Interpolating Artificial Neural Network to In-Plane Accelerated Simultaneous Multislice Imaging: MRI, RAKI, kspace (x)
4. [Approximate k-space models and Deep Learning for fast photoacoustic reconstruction](https://github.com/Theodore-PKU/paper-notes/blob/master/approximate-k-space-models-and-deep-learning-for-fast-photoacoustic-reconstruction-2018-200321.md): PAT, special network architecture and DC layer.
5. Artificial Intelligence in Musculoskeletal Imaging: Current Status and Future Directions: MRI, review (x)
6. Artificial intelligence in pediatric and adult congenital cardiac MRI : an unmet clinical need: MRI, review (x)
7. Assessment of the generalization of learned image reconstruction and the potential for transfer learning: MRI, assessment, generalization, transfer learning
8. Compressed sensing MRI : a review from signal processing perspective: MRI, review (x)
9. [Conditional generative adversarial network for 3D rigid‐body motion correction in MRI](https://github.com/Theodore-PKU/paper-notes/blob/master/conditional-generative-adversarial-network-for-3d-rigid-body-motion-correction-in-MRI-2019-200323.md): MRI, GAN, motion correction
10. Deep Learning in MR Image Processing: MRI, review (x)
11. [Deep residual network for off‐resonance artifact correction with application to pediatric body MRA with 3D cones](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-residual-network-for-off-resonance-artifact-correction-with-application-to-pediatric-body-mra-with-3d-cones-2019-200323.md): MRI, CNN, ResNet
12. [Denoising Auto-encoding Priors in Undecimated Wavelet Domain for MR Image Reconstruction](https://github.com/Theodore-PKU/paper-notes/blob/master/denoising-auto-encoding-priors-in-undecimated-wavelet-domain-for-mr-image-reconstruction-2019-200323.md): MRI, DAE, PGM, wavelet transform
13. [Efficient Fine-tuning of Neural Networks for Artifact Removal In Deep Learning for Inverse Imaging Problems](https://github.com/Theodore-PKU/paper-notes/blob/master/effcient-fine-tuning-of-neural-networks-for-artifact-removal-in-deep-learning-for-inverse-imaging-problems-2019-200323.md): inverse problem, DIP, post-process, fine-tuning network
14. High-resolution 3D MR Fingerprinting using parallel imaging and deep learning: MRF, CNN
15. Learning-based computational MRI reconstruction without big data: From linear interpolation and structured low-rank matrices to recurrent neural networks: MRI, LORAKI
16. [MANTIS: Model‐Augmented Neural neTwork with Incoherent k‐space Sampling for efficient MR parameter mapping](https://github.com/Theodore-PKU/paper-notes/blob/master/mantis-model-augmented-neural-network-with-incoherent-k-space-sampling-for-efficient-mr-parameter-mapping-2019-200324.md): MRI, CNN, mapping parameter, cyclic loss
17. Next generation research applications for hybrid PET/MR and PET/CT imaging using deep learning: PET, review (x)
18. MoCoNet: Motion Correction in 3D MPRAGE images using a Convolutional Neural Network approach: MRI, motion correction, U-net, post processing
19. [Model Based Image Reconstruction Using Deep Learned Priors (MoDL)](https://github.com/Theodore-PKU/paper-notes/blob/master/model-based-image-reconstruction-using-deep-learned-priors-modl-2018-200324.md): MRI, multi-coil, unroll, CNN
20. [Recurrent inference machines for reconstructing heterogeneous MRI data](https://github.com/Theodore-PKU/paper-notes/blob/master/recurrent-inference-machines-for-reconstructing-heterogeneous-mri-data-2019-200325.md): MRI, multi-coil, RNN, gradient as input
21. Real‐time cardiovascular MR with spatio‐temporal artifact suppression using deep learning–proof of concept in congenital heart disease: MRI, CNN, U-net, residual learning, 3D, congenital heart disease
22. Reconstruction of Undersampled 3D Non-Cartesian Image-Based Navigators for Coronary MRA Using an Unrolled Deep Learning Model: MRI, unroll network, proximal gradient descent, ResNet
23. Regularization by architecture: A deep prior approach for inverse problems: inverse problem, DIP
24. Self-attention convolutional neural network for improved MR image reconstruction: MRI, CNN, U-net, data consistency, dense shortcut, self-attention layer
25. [sRAKI-RNN: Accelerated MRI with Scan-Speciﬁc Recurrent Neural Networks using Densely Connected Blocks](https://github.com/Theodore-PKU/paper-notes/blob/master/sraki-rnn-accelerated-mri-with-scan-specific-recurrent-neural-networks-using-densely-connected-blocks-2019-200325.md): MRI, kspace, RAKI, unroll network, dense shortcut
26. [Deep reconstruction model for dynamic PET images](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-reconstruction-model-for-dynamic-pet-images-2017-200326.md): PET, SAE, MEML, dynamic PET
27. [Penalized PET reconstruction using deep learning prior and local linear fitting](https://github.com/Theodore-PKU/paper-notes/blob/master/penalized-pet-reconstruction-using-deep-learning-prior-and-local-linear-fitting-2018-200326.md): PET, ADMM, LLF, DnCNN, unroll
28. [Iterative PET Image Reconstruction Using Convolutional Neural Network Representation](https://github.com/Theodore-PKU/paper-notes/blob/master/iterative-pet-image-reconstruction-using-convolutional-neural-network-representation-2018-200326.md): PET, generative model, ADMM, U-net
29. [DeepPET: A deep encoder–decoder network for directly solving the PET image reconstruction inverse problem](https://github.com/Theodore-PKU/paper-notes/blob/master/deeppet-a-deep-encoder-decoder-network-for-directly-solving-the-pet-image-reconstruction-inverse-problem-2019-200326.md): PET, CNN, AE
30. [Artificial Neural Network Enhanced Bayesian PET Image Reconstruction](https://github.com/Theodore-PKU/paper-notes/blob/master/artificial-neural-network-enhanced-bayesian-pet-image-reconstruction-2018-200327.md): PET, CNN, MLP, MAP, post process
31. [3D conditional generative adversarial networks for high-quality PET image estimation at low dose](https://github.com/Theodore-PKU/paper-notes/blob/master/3d-conditional-generative-adversarial-networks-for-high-quality-pet-image-estimation-at-low-dose-2018-200327.md): PET, CNN, GAN-loss, L-1 loss, U-net,  patch
32. Joint Reconstruction of PET + Parallel-MRI in a Bayesian Coupled-Dictionary MRF Framework: PET, MRI, joint reconstruction, dictionary learning, Bayesian framework
33. A Novel Loss Function Incorporating Imaging Acquisition Physics for PET Attenuation Map Generation using Deep Learning: PET/CT, attenuation map
34. [Super-Resolution PET Imaging Using Convolutional Neural Networks](https://github.com/Theodore-PKU/paper-notes/blob/master/super-resolution-pet-imaging-using-convolutional-neural-networks-2020-200327.md): PET, MRI, SR, CNN
35. [Deep learning MR imaging–based attenuation correction for PET/MR imaging](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-learning-mr-imaging-based-attenuation-correction-for-pet-mr-imaging-2018-200327.md): PET, MRI, attenuation map
36. [Reconstruction of PET Images Using Cross-Entropy and Field of Experts](https://github.com/Theodore-PKU/paper-notes/blob/master/reconstruction-of-pet-images-using-cross-entropy-and-field-of-experst-2019-200328.md): PET, Field of Experts, cross-entropy, optimization, Bayesian framework
37. [Parameter Constrained Transfer Learning for Low Dose PET Image Denoising](https://github.com/Theodore-PKU/paper-notes/blob/master/parameter-constrained-transfer-learning-for-low-dose-pet-image-denoising-2019-200328.md): PET, GAN, CNN, U-net, perceptual loss, training strategy
38. [PET image denoising using unsupervised deep learning](https://github.com/Theodore-PKU/paper-notes/blob/master/pet-image-denoising-using-unsupervised-deep-learning-2019-200328.md): PET, unsupervised, DIP, U-net, MRI/CT
39. [An investigation of quantitative accuracy for deep learning based denoising in oncological PET](https://github.com/Theodore-PKU/paper-notes/blob/master/an-investigation-of-quantitative-accuracy-for-deep-learning-based-denoising-in-oncological-pet-2019-200328.md): PET, CNN, GAN, U-net, experiment, 3D
40. [Machine Learning in Quantitative PET Imaging](https://github.com/Theodore-PKU/paper-notes/blob/master/machine-learning-in-quantitative-pet-imaging-2020-200328.md): PET, review
41. [Deep Auto-context Convolutional Neural Networks for Standard-Dose PET Image Estimation from Low-Dose PET/MRI](https://github.com/Theodore-PKU/paper-notes/blob/master/deep-auto-context-convolutional-neural-networks-for-standard-dose-pet-image-estimation-from-low-dose-pet-mri-2017-200328.md): PET, CNN, stack, MRI, slice by slice, patch
42. [3D auto-context-based locality adaptive multi-modality GANs for PET synthesis](https://github.com/Theodore-PKU/paper-notes/blob/master/3d-auto-context-based-locality-adpative-multi-modality-gans-for-pet-synthesis-2018-200328.md): PET, CNN, GAN, U-net, L1, stack, multi-modality, 3D, patch, fusion image
43. Medical Image Synthesis with Deep Convolutional Adversarial Networks: medical image synthesis, GAN, CNN
44. PET Image Denoising Using a Deep Neural Network Through Fine Tuning: PET, denoising, fine-tune, residual network, perceptual loss
45. 200x Low-dose PET Reconstruction using Deep Learning: PET, U-net, CNN, multi-slice, residual learning, L1 loss



综述和思考并行

中秋夜的 hankel matrix 流形划分，

两个数学问题：函数超平面逼近的近似理论、鄂老师说的动态系统，流形上的优化。有理论的文章受欢迎。

户将 email 联系


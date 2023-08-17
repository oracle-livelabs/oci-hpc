# 简介

实施图像分类培训是计算机视觉的“Hello World”。尽管有关该主题的博客文章很多，但大多数博客都提供了解决方案，没有充分解释库 API、底层主体或两者。来自 PyTorch 的[培训分类器](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)指南是一个很好的起点，但对于新到该字段的读者而言，代码的一些细节解释得太少。本研习会以该教程为基础，逐行解释代码，以促进更清晰的学习路径。本指南将介绍如何使用 CPU 训练模型，然后说明如何更新代码以利用 GPU 资源。请注意，GPU 实例在自由层中不可用。

估计实验室时间：60 分钟

### 关于 Tensors

为了更好或更糟，可视化表示有助于将计算机科学术语、数据结构和算法情景化。对于传感器，[KDnuggets 提供了明确的解释](https://www.kdnuggets.com/2018/05/wtf-tensor.html)。在联机搜索定义时，您可能会在 tensor 和 colloquial 用法的技术定义之间遇到一些混乱。从技术上讲，矢量是 1 维的传感器，矩阵是 2 维的传感器，但最常是 tensor 引用一个大于矢量和矩阵的 n 维容器。

![不同尺寸的笔数的视觉表现](images/tensor.png)

这个术语对于理解很重要，因为数十人对于构建人工智能算法至关重要，他们支持有关优化计算资源的讨论。训练模型时需要关注的一个常见问题是 GPU 利用率，因为 NVIDIA [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) 能够实现重要的计算加速，但可以进行一些优化。幸运的是，PyTorch 包含负责将传感器传输到 GPU 的数据加载程序。

### 目标

本练习中涵盖的主题：

*   传感器
*   DataLoaders
*   卷积神经网络 (CNN)
*   损失功能和优化程序
*   模型培训
*   GPU 加速；

### 前提条件

本练习假定您具有：

*   Oracle 免费层级或付费云账户

## 确认

*   **作者** - Big Compute 高级解决方案架构师 Justin Blau
*   **上次更新者/日期** - Justin Blau，大计算，2020 年 10 月
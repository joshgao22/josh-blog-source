---
title: 'Josh''s Notes: 最优阵列处理（Part 1.1 —— 频率-波数响应和波束方向图）'
mathjax: true
comments: true
copyright: true
toc:
  enable: false
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 阵列和空域滤波器
description: >-
  假设在空时场的某个区域内有一个或多个信号，同时也假设在空时场的某个区域内存在噪声和/或干扰。在我们所感兴趣的一些应用中，这些区域是部分重叠的。阵列可以利用信号的空域特征，对空时场域（space-time
  field）内的信号进行滤波。这种滤波可以用一种与角度或波数的相关性（in terms of a dependence upon angle or
  wavenumber）进行描述。在频域看，这种滤波是通过使用复增益对阵列输出进行加权，根据信号的空域相关性对信号进行增强或抑制来实现的。通常我们对空时场进行空域滤波的目的是：使得从一个（或一组）特定角度到来的信号通过有效的组合得到增强，抑制从其他角度到来的噪声或干扰。
abbrlink: 8b61f5a7
date: 2023-04-18 23:57:47
tags:
  - 阵列信号处理
  - 频率-波数响应
  - 波束方向图
---

<a id="fig.1-1-1"></a>

![图 1-1-1 球坐标系](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-010-SphericalCoordinareSystem.png!sign){width=600px}

首先需要明确采用的坐标系，如[图 1-1-1](#fig.1-1-1) 所示，直角坐标系和球坐标系的关系也在其中标明：

$$\begin{equation}
\begin{aligned}
x &= r \sin\theta\cos\varphi \\
y &= r \sin\theta\sin\varphi \\
z &= r \cos\theta
\end{aligned}
\end{equation}$$

本文分析一个阵列对外部信号场的响应。阵列由一组全向阵元组成，阵元的位置为 $\vec{p}_n$,
如[图 1-1-2](#fig.1-1-2) 所示。阵元在位置 $\vec{p}_n : n = 0, 1, \cdots, N — 1$ 上对信号场进行空域采样。这产生了一组信号，表示为矢量 $\vec{f}(t, \vec{p})$

$$\begin{equation}
\vec{f}(t, \vec{p}) = \left[
  \begin{array}{c}
    f(t, \vec{p}_0) \\
    f(t, \vec{p}_1) \\
    \vdots \\
    f(t, \vec{p}_{N-1}) \\
  \end{array}
\right]
\end{equation}$$

<a id="fig.1-1-2"></a>

![图 1-1-2  $N$ 阵元阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-020-NElementArray.png!sign){width=600px}

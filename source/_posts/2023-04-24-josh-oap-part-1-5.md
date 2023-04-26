---
title: 'Josh''s Notes: 最优阵列处理（Part 1.5 —— 阵列性能度量）'
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - 阵列信号处理
  - 阵列性能
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 阵列和空域滤波器
abbrlink: 6a7100b0
date: 2023-04-24 11:50:37
---

&emsp;有很多性能度量方法都可以用于评估一个阵列的能力，每一中度量方法都试图量化阵列的某一个重要特性，或者是阵列对信号环境的响应，或者是阵列设计的敏感度。我们已经采用了第一零点间距作为波束宽度，或频率-波数响应的分辨率的度量，也利用了均匀加权线阵的主要旁瓣水平作为一种度量。本节将讨论三个常用的阵列性能度量方法：

1. 方向性（Directivity）
2. 对空域自噪声的阵列增益 (Array gain versus spatially white noise，$A_w$)
3. 敏感度（Sensitivity）和容错因子（Tolerance Factor）

<!-- more -->

# 方向性

&emsp;&emsp;阵列和孔径的一个常用性能度量是方向性。我们将在一般情况下给出定义，并讨论线性阵列的特殊情况。

## 方向性的定义

&emsp;&emsp;定义**{% label primary @功率方向图（power pattern） %}** $P(\theta,\varphi)$ 为波束方向图 $B(\omega : \theta, \varphi)$ 幅度的平方，即

$$\begin{equation}
  P(\theta,\varphi) = \left| B(\omega : \theta, \varphi) \right|^2
\end{equation}$$

其中 $P(\theta,\varphi)$ 和频率的关系在符号表示中省略掉了。则方向性 $D$ 定义为

$$\begin{equation} \label{DefinitionOfDirectivity}
  D = \frac{P(\theta_\mathrm{T},\varphi_\mathrm{T})}{ \displaystyle\frac{1}{4\pi} \int_0^\pi \int_0^{2\pi} \sin \theta \cdot P(\theta,\varphi) \,\mathrm{d} \varphi \,\mathrm{d} \theta}
\end{equation}$$

其中 $(\theta_\mathrm{T},\varphi_\mathrm{T})$ 是主响应轴（main response axis，MRA）的指向。

&emsp;&emsp;对于一个发射阵列或孔径， $D$ 代表最大发射密度（每单位立体角对应的发射功率）和平均发射密度（在球面上平均）的比值。

&emsp;&emsp;对于一个接收天线，分母代表的是阵列（或孔径）对全向噪声（在一个球内均匀分布的噪声）的输出噪声功率。分子代表的是对应从 $(\theta_\mathrm{T},\varphi_\mathrm{T})$ 方向入射的
信号的功率。所以，$D$ 可以解释为是对全向噪声的阵列增益。

&emsp;&emsp;如果假设权值做了归一化，使得 $P_n(\theta_\mathrm{T},\varphi_\mathrm{T}) = 1$，则式 $\eqref{DefinitionOfDirectivity}$ 可以写成

$$\begin{equation} \label{NormalizedDefinitionOfDirectivity}
  D = \left\{ \frac{1}{4\pi} \int_0^\pi \int_0^{2\pi} \sin \theta \cdot P(\theta,\varphi) \,\mathrm{d} \varphi \,\mathrm{d} \theta  \right\}^{-1}
\end{equation}$$

对于线性阵列，有

$$\begin{equation}
  B(\theta,\varphi) = B(\theta)
\end{equation}$$

则式 $\eqref{NormalizedDefinitionOfDirectivity}$ 可写为

$$\begin{equation}
  D = \left\{ \frac{1}{2} \int_0^\pi \left| B(\theta) \right|^2 \sin \theta \,\mathrm{d} \theta  \right\}^{-1}
\end{equation}$$

在 $u$ 空间可以表示为

$$\begin{equation} \label{NormalizedDefinitionOfDirectivityInDirectionCosineSpaceOfLA}
  D = \left\{ \frac{1}{2} \int_{-1}^1 \left| B(u) \right|^2 \,\mathrm{d} u  \right\}^{-1}
\end{equation}$$

通常，这个表达式必须用数值的方法计算。

&emsp;&emsp;利用 [$u$ 空间的波束方向图的表达式][]，式 $\eqref{NormalizedDefinitionOfDirectivityInDirectionCosineSpaceOfLA}$ 中的表达式可以利用阵列的权值写成

$$\begin{equation}
  D = \left\{ \frac{1}{2} \int_{-1}^1 \sum_{n=0}^{N-1}w_n^\ast e^{jn\left( \frac{2\pi d}{\lambda} \right) \left( u - u_\mathrm{T} \right)} \sum_{m=0}^{N-1} w_m e^{-jm\left( \frac{2\pi d}{\lambda} \right) \left( u - u_\mathrm{T} \right)} \,\mathrm{d} u  \right\}^{-1}
\end{equation}$$

其中 $u_\mathrm{T}$ 是 $u$ 空间内的阵列的所调方向。交换积分和求和的顺序，并积分，得到

$$\begin{equation} \label{ResultOfDirectivityInDirectionCosineSpaceOfLA}
  D = \left\{ \sum_{n=0}^{N-1} \sum_{m=0}^{N-1} w_m w_n^\ast e^{j \left( \frac{2\pi d}{\lambda} \right) (m-n) u_\mathrm{T} } \mathrm{sinc} \left( \frac{2\pi d}{\lambda} (n-m)\right)  \right\}^{-1}
\end{equation}$$

定义几个矩阵以得到一个简洁的表达式。定义 $\overrightarrow{\mathrm{sinc}}$ 矩阵的第 $n,m$ 个元素为

$$\begin{equation}
  \left[ \overrightarrow{\mathrm{sinc}} \right]_{nm} \triangleq \mathrm{sinc} \left( \frac{2\pi d}{\lambda} (n-m)\right)
\end{equation}$$

[对角调向矩阵][] 在 $u$ 空间的表示形式为

$$\begin{equation}
  \vec{I}_{\mathrm{s}u} = \mathrm{diag}\left( 1, e^{j \frac{2\pi d}{\lambda}u_\mathrm{T}} , e^{j \frac{2\pi d}{\lambda} 2u_\mathrm{T}}, \cdots, , e^{j \frac{2\pi d}{\lambda}(N-1)u_\mathrm{T}} \right)
\end{equation}$$

则

$$\begin{equation}
  D = \vec{I}_{\mathrm{s}u} \vec{w}^\mathrm{H} \left[ \overrightarrow{\mathrm{sinc}} \right] \vec{w} \vec{I}_{\mathrm{s}u}^\mathrm{H}
\end{equation}$$

通常，调向运算在权值矢量中包含[^1]

$$\begin{equation}
  \vec{w}_\mathrm{s} = \vec{w}\vec{I}_{\mathrm{s}u}^\mathrm{H}
\end{equation}$$

则

$$\begin{equation}
  D = \vec{w}_\mathrm{s}^\mathrm{H} \left[ \overrightarrow{\mathrm{sinc}} \right] \vec{w}_\mathrm{s}
\end{equation}$$

## 标准线阵的情况

&emsp;&emsp;标准线阵（standard linear array）是我们所感兴趣的一个特殊情况。如果 $d = \lambda/2$，则式 $\eqref{ResultOfDirectivityInDirectionCosineSpaceOfLA}$ 简化为

$$\begin{equation} \label{SimplifiedResultOfDirectivityInDirectionCosineSpaceOfLA}
  D = \left\{ \sum_{n=0}^{N-1} \sum_{m=0}^{N-1} w_m w_n^\ast e^{j \pi (m-n) u_\mathrm{T} } \mathrm{sinc} \left( \pi (n-m)\right)  \right\}^{-1}
\end{equation}$$

当 $m = n$ 时，$\mathrm{sinc}$ 函数等于 1，当 $m \ne n$ 时，$\mathrm{sinc}$ 函数等于 0。所以式 $\eqref{SimplifiedResultOfDirectivityInDirectionCosineSpaceOfLA}$ 简化为

$$\begin{equation}
  D = \left\{ \sum_{n=0}^{N-1} \left| w_n \right|^2 \right\} = \left( \vec{w}^\mathrm{H}\vec{w} \right)^{-2} = \left\{ \left\| \vec{w} \right \|^2 \right\}^{-1}
\end{equation}$$

其中

$$\begin{equation}
  \left\| \vec{w} \right \| = \left( \vec{w}^\mathrm{H}\vec{w} \right)^{\frac{1}{2}}
\end{equation}$$

是矢量 $\vec{w}$ 的 2-模。

&emsp;&emsp;所以一个标准线阵的方向性是权值矢量幅度平方的倒数。方向性不依赖于阵列的指向。当阵列指向从阵列的正侧向移开时，波束变宽，但是 $\varphi$ 的积分范围减小了

&emsp;&emsp;对于 $d \ne \lambda/2$，利用式 $\eqref{ResultOfDirectivityInDirectionCosineSpaceOfLA}$，则方向性将和阵列的指向有关。

&emsp;&emsp;对于均匀加权的标准线阵（UWLA），$w_n = 1/N$，所以

$$\begin{equation}
  \sum_{n=0}^{N-1} \left| w_n \right|^2 = \frac{1}{N}
\end{equation}$$

所以

$$\begin{equation}
  D = N
\end{equation}$$

&emsp;&emsp;均匀加权使得标准线阵的方向性最大。为了说明这一点，施加约束条件

$$\begin{equation} \label{ConstraintOfLagrangeMultiplier}
  \sum_{n=0}^{N-1} w_n = 1
\end{equation}$$

这保证了波束方向图在 $u_\mathrm{T}$ 的值为 1，并求 $\displaystyle\sum_{n=0}^{N-1} \left| w_n \right|^2$ 的最小值，这里利用 Lagrange 乘子法，另

$$\begin{equation}
  F = \sum_{n=0}^{N-1} \left| w_n \right|^2 + \lambda \left( \sum_{n=0}^{N-1} w_m - 1 \right)
\end{equation}$$

其中 $\lambda$ 是 Lagrange 乘子。对 $w_n$ 求导，并令结果等于零，得到

$$\begin{equation}
  w_n^\ast = -\lambda
\end{equation}$$

也即

$$\begin{equation} \label{ResultOfLagrangeMultiplier}
  w_n = -\lambda^\ast
\end{equation}$$

把式 $\eqref{ResultOfLagrangeMultiplier}$ 代入到式 $\eqref{ConstraintOfLagrangeMultiplier}$，得到 $\lambda = -\frac{1}{N}$，则有

$$\begin{equation}
  w_n = \frac{1}{N}
\end{equation}$$

此即为我们想要得到的结果。

[$u$ 空间的波束方向图的表达式]: https://josh-gao.top/posts/de20fd09.html#BeamPatternInDirectionCosineDomain
[对角调向矩阵]: https://josh-gao.top/posts/c2604c43.html#DiagonalSteeringMatrix

[^1]: 在大多数情况下，我们去掉下标 “$s$”，因为假设 $\vec{w}$ 是包含调向的。

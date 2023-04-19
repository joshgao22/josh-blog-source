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
abbrlink: 8b61f5a7
date: 2023-04-18 23:57:47
tags:
  - 阵列信号处理
  - 频率-波数响应
  - 波束方向图
---

&emsp;&emsp;假设在空时场的某个区域内有一个或多个信号，同时在某些区域内存在噪声和/或干扰。在一些阵列信号处理的应用场景中，这些区域是部分重叠的。阵列可以利用信号的空域特征，对空时场域（space-time field）内的信号进行滤波。这种滤波可以用一种与角度或波数的相关性（in terms of a dependence upon angle or wavenumber）进行描述。在频域看，这种滤波是通过使用复增益对阵列输出进行加权，根据信号的空域相关性对信号进行增强或抑制来实现的。通常我们对空时场进行空域滤波的目的是：使得从一个（或一组）特定角度到来的信号通过有效的组合得到增强，抑制从其他角度到来的噪声或干扰。

<a id="fig.1-1-1"></a>

![图 1-1-1 球坐标系](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-010-SphericalCoordinareSystem.png!sign){width=600px}

&emsp;&emsp;首先需要明确采用的坐标系，如[图 1-1-1](#fig.1-1-1) 所示，直角坐标系和球坐标系的关系也在其中标明：

$$\begin{equation}
\begin{aligned}
x &= r \sin\theta\cos\varphi \\
y &= r \sin\theta\sin\varphi \\
z &= r \cos\theta
\end{aligned}
\end{equation}$$

<!-- more -->

&emsp;&emsp;本文分析一个阵列对外部信号场的响应。阵列由一组全向阵元组成，阵元的位置为 $\vec{p}_n$，如[图 1-1-2](#fig.1-1-2) 所示。阵元在位置 $\vec{p}_n : n = 0, 1, \cdots, N — 1$ 上对信号场进行空域采样。这产生了一组信号，表示为矢量 $\vec{f}\left(t, \vec{p}\right)$

$$\begin{equation}
\vec{f}(t, \vec{p}) = \left[
  \begin{array}{c}
    f\left(t, \vec{p}_0\right) \\
    f\left(t, \vec{p}_1\right) \\
    \vdots \\
    f\left(t, \vec{p}_{N-1}\right) \\
  \end{array}
\right]
\end{equation}$$

<a id="fig.1-1-2"></a>

![图 1-1-2 $N$ 阵元阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-020-NElementArray.png!sign){width=600px}

&emsp;&emsp;对每个阵元的输出用一个线性时不变滤波器进行处理，该滤波器的冲激响应为 $h_n(\tau)$
并对所有输出求和，得到阵列的输出 $y(t)$，如[图 1-1-3](#fig.1-1-3) 所示。假设观察间隔足够长（可以考虑为无限长），则输出 $y(t)$ 可以写成一个卷积积分的形式：

$$\begin{equation}
y(t) = \sum_{n=0}^{N-1} \int_{-\infty}^{\infty} h_n (t-\tau)f_n\left(\tau,\vec{p}_n\right) \mathrm{d} \tau
\end{equation}$$

<a id="fig.1-1-3"></a>

![图 1-1-3 线性处理阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-030-ArrayWithLinearProcessing.png!sign){width=600px}

这个结果可以用矢量符号表示为（即将标量结果向矢量模型的进行直接扩展，a straightforward extension of scalar results to the vector model）

$$\begin{equation} \label{VectorFormOfLinearArrayOutputInTimeDomain}
y(t) = \int_{-\infty}^{\infty} \vec{h}^\mathrm{T} (t-\tau) \vec{f}\left(\tau,\vec{p}\right) \mathrm{d} \tau
\end{equation}$$

其中

$$\begin{equation}
\vec{h}(\tau) = \left[ \begin{array}{c}
  h_0(\tau) \\
  h_1(\tau) \\
  \vdots \\
  h_{N-1}(\tau)
\end{array}
\right]
\end{equation}$$

也可以把式 $\eqref{VectorFormOfLinearArrayOutputInTimeDomain}$ 在变换域表示为

$$\begin{equation}
\begin{aligned}
  Y(\omega) &= \int_{-\infty}^{\infty} y(t) e^{-j\omega t} \mathrm{d} t \\
  &= \vec{H}^\mathrm{T}(\omega) \vec{F}(\omega)
\end{aligned}
\end{equation}$$

其中

$$\begin{equation}
\vec{H} (\omega) = \int_{-\infty}^{\infty} \vec{h}(t)  e^{-j\omega t} \mathrm{d} t
\end{equation}$$

$$\begin{equation} \label{SampleVectorInFreqDomain}
\vec{F} \left(\omega,\vec{p}\right) = \int_{-\infty}^{\infty} \vec{f}\left(\tau,\vec{p}\right)  e^{-j\omega t} \mathrm{d} t
\end{equation}$$

在大多数情况下，去掉 $\eqref{SampleVectorInFreqDomain}$ 中左边的 $\vec{p}$, 直接使用 $\vec{F}(\omega)$ 。

&emsp;&emsp;下面考虑如[图 1-1-4](#fig.1-1-4) 所示的简单的波束形成操作。外部信号输入为传播方向为 $\vec{a}$, 时域频率（弧度）为 $\omega$ 的平面波，则在时域上每个阵元的输入信号可以用两种等效的方式进行表示。

<a id="fig.1-1-4"></a>

![图 1-1-4 具有平面波输入的阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-040-ArrayWithPlaneWaveInput.png!sign){width=600px}

&emsp;&emsp;由于信号到达不同阵元的时间不同，因此第一种方式强调信号到达不同阵元的时间延时。设 $f(t)$ 是**在坐标系原点接收到的信号**，其在变换域表示为 $F (\omega)$，则

$$\begin{equation} \label{DelayedSignal}
  \vec{f}(t, \vec{p}) = \left[
    \begin{array}{c}
      f\left(t - \tau_0\right) \\
      f\left(t - \tau_1\right) \\
      \vdots \\
      f\left(t - \tau_{N-1}\right) \\
    \end{array}
  \right]
\end{equation}$$

其中

$$\begin{equation} \label{ExpressionOfDelayByDirection}
  \tau_n = \frac{\vec{a}^\mathrm{T}\vec{p}_n}{c}
\end{equation}$$

其中 $c$ 是信号在介质中的传播速度，$\vec{a}$ 是一个单位矢量，可以表示为

$$\begin{equation}
  \vec{a} = \left[ \begin{array}{c}
    -\sin\theta\cos\varphi \\
    -\sin\theta\sin\varphi \\
    -\cos\theta
  \end{array} \right]
\end{equation}$$

上式中的负号是考虑了 $\vec{a}$ 的方向。则 $\tau_n$ 可以进一步表示为：

$$\begin{equation} \label{RelativeDelay}
  \tau_n = -\frac{1}{c}\left(\sin\theta\cos\varphi \cot p_{x_n} + \sin\theta\sin\varphi \cdot p_{y_n} + \cos\theta \cdot p_{z_n} \right)
\end{equation}$$

如果定义对于每个轴的方向余弦为

$$\begin{align}
  u_x &= \sin\theta\cos\varphi \\
  u_y &= \sin\theta\sin\varphi \\
  u_z &= \cos\theta
\end{align}$$

则有

$$\begin{equation}
  \vec{u} = -\vec{a}
\end{equation}$$

则 $\eqref{RelativeDelay}$ 可以写成

$$\begin{equation}
   \tau_n = -\frac{1}{c}\left(u_x p_{x_n} + u_y p_{y_n} + u_z p_{z_n} \right) = -\frac{\vec{u}^\mathrm{T}\vec{p}_n}{c}
\end{equation}$$

根据 $\eqref{DelayedSignal}$, $\vec{F}(\omega)$ 的第 $n$ 个分量为（利用 Fourier 变换的时移性质）

$$\begin{equation}
  F_n(\omega) = \int_{-\infty}^{\infty} e^{j \omega t} f(t - \tau_n) \mathrm{d} t = e^{j \omega \tau_n} F (\omega)
\end{equation}$$

其中

$$\begin{equation}
  \omega \tau_n = \frac{\omega}{c} \vec{a}^\mathrm{T}\vec{p}_n = -\frac{\omega}{c} \vec{u}^\mathrm{T}\vec{p}_n
\end{equation}$$

对于在局部均匀的介质（locally homogeneous medium）里传播的平面波，定义波数 $\vec{k}$ （在波传播的方向上单位长度内的周期数目）为

$$\begin{equation} \lebel{DefinitionOfWavenumber}
  \vec{k} = \frac{\omega}{c} \vec{a} = \frac{2\pi}{\lambda} \vec{a}
\end{equation}$$

其中，$\lambda$ 是对应于频率 $\omega$ 的波长。等效地，

$$\begin{equation}
  \vec{k} = -\frac{2\pi}{\lambda} \left[
    \begin{array}{c}
    \sin\theta\cos\varphi \\
    \sin\theta\sin\varphi \\
    \cos\theta
    \end{array}
  \right]
  = -\frac{2\pi}{\lambda} \vec{u}
\end{equation}$$

且波数的幅度由波动方程限定

$$\begin{equation}
  \left| \vec{k} \right| = \frac{\omega}{c} = \frac{2\pi}{\lambda}
\end{equation}$$

所以，仅 $\vec{k}$ 的方向是变化的。比较 $\eqref{ExpressionOfDelayByDirection}$ 和 $\eqref{DefinitionOfWavenumber}$，可以看到

$$\begin{equation}
  \omega \tau_n = \vec{k}^\mathrm{T}\vec{p}_n
\end{equation}$$

定义

$$\begin{equation}
  \vec{v}_{\vec{k}}(\vec{k}) = \left[ \begin{array}{c}
    e^{-j \vec{k}^\mathrm{T}\vec{p}_0} \\
    e^{-j \vec{k}^\mathrm{T}\vec{p}_1} \\
    \vdots \\
    e^{-j \vec{k}^\mathrm{T}\vec{p}_{N-1}} \\
  \end{array} \right]
\end{equation}$$

则 $\vec{F}(\omega)$ 可写为

$$\begin{equation}
  \vec{F}(\omega) = F(\omega) \vec{v}_{\vec{k}}(\vec{k})
\end{equation}$$

矢量 $\vec{v}_{\vec{k}}(\vec{k})$ 包含了阵列的所有空间特征，称为**{% label primary @阵列流形矢量（array manifold vector） %}**。在我们的讨论中，阵列流形矢量具有非常核心的作用。下标 $\vec{k}$ 表示参数属于 $\vec{k}$ 空间。这个下标的作用是把它和以后将在阵列流形矢量中使用的其他变量相区分。

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

&emsp;&emsp;假设在空时场（space-time field）的某个区域内有一个或多个信号，同时在某些区域内存在噪声和/或干扰。在一些阵列信号处理的应用场景中，这些区域是部分重叠的。阵列可以利用信号的空域特征，对空时场域内的信号进行滤波。这种滤波可以用一种与角度或波数的相关性（in terms of a dependence upon angle or wavenumber）进行描述。从频域看，这种滤波是通过使用复增益对阵列输出进行加权，根据信号的空域相关性对信号进行增强或抑制来实现的。通常我们对空时场进行空域滤波的目的是：使得从一个（或一组）特定角度到来的信号通过有效的组合得到增强，抑制从其他角度到来的噪声或干扰。

<a id="fig.1-1-1"></a>

![图 1-1-1 球坐标系](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-010-SphericalCoordinareSystem.png!sign){width=600px}

&emsp;&emsp;在分析之前首先需要明确采用的坐标系，如[图 1-1-1](#fig.1-1-1) 所示，直角坐标系和球坐标系的关系也在其中标明：

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

{% note warning %}

请注意，这里并没有指定阵列的几何排布，因此下面的分析是适用于所有阵列的。

{% endnote %}

<a id="fig.1-1-2"></a>

![图 1-1-2 $N$ 阵元阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-020-NElementArray.png!sign){width=600px}

&emsp;&emsp;对每个阵元的输出用一个线性时不变滤波器进行处理，该滤波器的冲激响应为 $h_n(\tau)$，并对所有输出求和，得到阵列的输出 $y(t)$，如[图 1-1-3](#fig.1-1-3) 所示。假设观察间隔足够长（可以考虑为无限长），则输出 $y(t)$ 可以写成卷积积分的形式：

$$\begin{equation}
y(t) = \sum_{n=0}^{N-1} \int_{-\infty}^{\infty} h_n (t-\tau)f_n\left(\tau,\vec{p}_n\right) \mathrm{d} \tau
\end{equation}$$

<a id="fig.1-1-3"></a>

![图 1-1-3 线性处理阵列](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-030-ArrayWithLinearProcessing.png!sign){width=600px}

这个结果可以用矢量符号表示为（即将标量结果向矢量模型进行直接扩展，a straightforward extension of scalar results to the vector model）

$$\begin{equation} \label{VectorFormOfLinearArrayOutputInTimeDomain}
y(t) = \int_{-\infty}^{\infty} \vec{h}^\mathrm{T} (t-\tau) \vec{f}\left(\tau,\vec{p}\right) \mathrm{d} \tau
\end{equation}$$

其中

$$\begin{equation} \label{ImpulseResponse}
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

在大多数情况下，可以去掉式 $\eqref{SampleVectorInFreqDomain}$ 中左边的 $\vec{p}$，直接使用 $\vec{F}(\omega)$ 。

&emsp;&emsp;下面考虑如[图 1-1-4](#fig.1-1-4) 所示的简单的波束形成操作。外部信号输入为传播方向为 $\vec{a}$，时域频率（弧度）为 $\omega$ 的平面波，则在时域上每个阵元的输入信号可以用两种等效的方式进行表示。

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

其中时延可表示为（距离除以速度得到时间）

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

如果定义对于每个轴的方向余弦（direction cosine）为

$$\begin{align}
  u_x &= \sin\theta\cos\varphi \\
  u_y &= \sin\theta\sin\varphi \\
  u_z &= \cos\theta
\end{align}$$

则有

$$\begin{equation}
  \vec{u} = -\vec{a}
\end{equation}$$

则式 $\eqref{RelativeDelay}$ 可以写成

$$\begin{equation} \label{RelativeDelayWithDirectionCosine}
   \tau_n = -\frac{1}{c}\left(u_x p_{x_n} + u_y p_{y_n} + u_z p_{z_n} \right) = -\frac{\vec{u}^\mathrm{T}\vec{p}_n}{c}
\end{equation}$$

根据式 $\eqref{DelayedSignal}$，$\vec{F}(\omega)$ 的第 $n$ 个分量为（利用 Fourier 变换的时移性质）

$$\begin{equation}
  F_n(\omega) = \int_{-\infty}^{\infty} e^{j \omega t} f(t - \tau_n) \mathrm{d} t = e^{j \omega \tau_n} F (\omega)
\end{equation}$$

其中

$$\begin{equation}
  \omega \tau_n = \frac{\omega}{c} \vec{a}^\mathrm{T}\vec{p}_n = -\frac{\omega}{c} \vec{u}^\mathrm{T}\vec{p}_n
\end{equation}$$

对于在局部均匀的介质（locally homogeneous medium）里传播的平面波，定义波数 $\vec{k}$ （在波传播的方向上单位长度内的周期数目）为

$$\begin{equation} \label{DefinitionOfWavenumber}
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

$$\begin{equation} \label{WaveEquation}
  \left| \vec{k} \right| = \frac{\omega}{c} = \frac{2\pi}{\lambda}
\end{equation}$$

所以，仅 $\vec{k}$ 的方向是变化的。比较式 $\eqref{ExpressionOfDelayByDirection}$ 和式 $\eqref{DefinitionOfWavenumber}$，可以看到

$$\begin{equation}
  \omega \tau_n = \vec{k}^\mathrm{T}\vec{p}_n
\end{equation}$$

定义

<a id="DefinitionOfArrayManifoldVector"></a>

$$\begin{equation} \label{DefinitionOfArrayManifoldVector}
  \vec{v}_{\vec{k}}\left(\vec{k}\right) = \left[ \begin{array}{c}
    e^{-j \vec{k}^\mathrm{T}\vec{p}_0} \\
    e^{-j \vec{k}^\mathrm{T}\vec{p}_1} \\
    \vdots \\
    e^{-j \vec{k}^\mathrm{T}\vec{p}_{N-1}} \\
  \end{array} \right]
\end{equation}$$

则 $\vec{F}(\omega)$ 可写为

$$\begin{equation}
  \vec{F}(\omega) = F(\omega) \vec{v}_{\vec{k}}\left(\vec{k}\right)
\end{equation}$$

矢量 $\vec{v}_{\vec{k}}\left(\vec{k}\right)$ 包含了阵列的所有空间特征，称为**{% label primary @阵列流形矢量（array manifold vector） %}**。在我们的讨论中，阵列流形矢量具有非常核心的作用。下标 $\vec{k}$ 表示参数属于 $\vec{k}$ 空间。这个下标的作用是把它和以后将在阵列流形矢量中使用的其他变量相区分。

&emsp;&emsp;在这种情况下，把每个阵元的输入信号平移，使之在时间上对齐，然后把它们相加。这个操作过程如[图 1-1-5](#fig.1-1-5) 所示，其中包含了归一化因子 $1/N$，使得输出为 $f(t)$。在这里，

$$\begin{equation} \label{DelayAndSumBeamformer}
  h_n(\tau) = \frac{1}{N} \delta(\tau + \tau_n)
\end{equation}$$

<a id="fig.1-1-5"></a>

![图 1-1-5 延时-求和波束形成器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-050-DelayAndSumBeamformer.png!sign){width=600px}

且

$$\begin{equation}
  y(t) = f(t)
\end{equation}$$

&emsp;&emsp;这个处理器称为**{% label primary @延时求和波束形成器（delay-and-sum beamformer） %}**或**{% label primary @常规波束形成器（conventional beamformer） %}**。在实际中会在每个通道中加上一个共同的延时，使得[图 1-1-5](#fig.1-1-5) 中的操作是物理可实现的。

&emsp;&emsp;此外，还可以把式 $\eqref{DelayAndSumBeamformer}$ 在频域内写成简洁的矩阵形式。如果 $\vec{k}_\mathrm{s}$ 是我们所感兴趣的平面波信号的波数，则

$$\begin{equation}
  \vec{H}^\mathrm{T} (\omega) = \frac{1}{N} \vec{v}_{\vec{k}}^\mathrm{H} \left( \vec{k}_\mathrm{s} \right)
\end{equation}$$

其中 $\vec{v}_{\vec{k}}\left(\vec{k}\right)$ 在式 $\eqref{DefinitionOfArrayManifoldVector}$ 中定义。

&emsp;&emsp;现在回到一般的问题，我们想要确定阵列对一个输入场 $\vec{f}(t, \vec{p})$ 的响应，这可以通过之前给出的卷积求和操作来实现，但更有用的方法是首先确定阵列对单位平面波（unit plane wave）的响应，响应为单位平面波时域（弧度）频率 $\omega$ 和波数 $\vec{k}$ 的函数。（利用叠加的复指数基函数对一个线性时不变系统进行分析的系统理论方法可以扩展到空时信号的情况。）

这里的基函数形式为

$$\begin{equation}
  f_n\left( t, \vec{p}_n \right) = \exp \left[ j \left( \omega t - \vec{k}^\mathrm{T} \vec{p}_n\right) \right], \, n = 0, 1, \cdots, N-1
\end{equation}$$

或

$$\begin{equation}
  \vec{f} \left( t, \vec{p} \right) = e^{j\omega t} \vec{v}_{\vec{k}}\left(\vec{k}\right)
\end{equation}$$

其中 $\vec{v}_{\vec{k}}\left(\vec{k}\right)$ 在式 $\eqref{DefinitionOfArrayManifoldVector}$ 中定义。

&emsp;&emsp;式 $\eqref{VectorFormOfLinearArrayOutputInTimeDomain}$ 中的阵列处理器对于一个平面波的响应为

$$\begin{equation} \label{ArrayResponseToUnitPlaneWaveInTimeDomain}
  y \left( t, \vec{k} \right) = \vec{H}^\mathrm{T} (\omega) \vec{v}_{\vec{k}}\left(\vec{k}\right) e^{j\omega t}
 \end{equation}$$

其中 $\vec{H} (\omega)$ 是式 $\eqref{ImpulseResponse}$ 中 $\vec{h}(\tau)$ 的傅里叶变换。

&emsp;&emsp;这里用了符号 $y \left( t, \vec{k} \right)$，以强调输出和输入波数 $\vec{k}$ 的关系。时域上的相关性体现在输出是一个复指数，和输入平面波具有相同的频率。在频域内，$\eqref{ArrayResponseToUnitPlaneWaveInTimeDomain}$ 可以写成下面的形式：

$$\begin{equation} \label{ArrayResponseToUnitPlaneWaveInFreqDomain}
  Y \left( \omega, \vec{k} \right) = \vec{H}^\mathrm{T} (\omega) \vec{v}_{\vec{k}}\left(\vec{k}\right)
 \end{equation}$$

&emsp;&emsp;注意，$\omega$ 是对应输入频率的单一频率。阵列的空时处理完全可以由式 $\eqref{ArrayResponseToUnitPlaneWaveInFreqDomain}$ 的右端描述。把这一项定义为

$$\begin{equation} \label{FrequencyWavenumberResponse}
  \boxed{\vec{\varUpsilon} \left( \omega, \vec{k} \right) \triangleq \vec{H}^\mathrm{T} (\omega) \vec{v}_{\vec{k}}\left(\vec{k}\right)}
\end{equation}$$

称之为阵列的**{% label primary @频率-波数响应函数（frequency-wavenumber response function） %}**。它描述了一个阵列对于波数为 $\vec{k}$、时域频率为 $\omega$ 的输入平面波的复增益，具有与线性、时不变系统的传递函数相同的解释（这里我们用了 $\vec{\varUpsilon} \left( \omega, \vec{k} \right)$, 是因为 $Y \left( \omega, \vec{k} \right)$ 在后面将要用于描述对任意输入的输出）。 $\vec{\varUpsilon} \left( \omega, \vec{k} \right)$ 是定义在整个 $\vec{k}$ 空间上的。$\eqref{FrequencyWavenumberResponse}$ 中的第二项 $\vec{v}_{\vec{k}}\left(\vec{k}\right)$ 是在式 $\eqref{DefinitionOfArrayManifoldVector}$ 中定义的阵列流形矢量。

&emsp;&emsp;频率-波数响应函数描述了阵列对任意一个平面波的响应。在大多数的实际物理应用中，时域频率 $\omega$ 和空域波数 $\vec{k}$ 通过平面波传播的波动方程形成了耦合关系。有时这是一个非常简单的关系，例如当一个平而波在均匀（且无限）的空间中传播时。在其他的情况下，可能是非常复杂的耦合关系，例如在水下声学和地震学中的分层介质模型中的情况。

&emsp;&emsp;一个阵列的**{% label primary @波束方向图（beam pattern） %}**定义的背景是平面波在局部均匀的介质中传播，约束的波动方程见式 $\eqref{WaveEquation}$。如式 $\eqref{WaveEquation}$ 所示，这约束了波数 $\vec{k}$ 的幅度。波束方向图是用入射方向表示的频率-波数响应函数，或者可以写成

$$\begin{equation}
  B(\omega:\theta,\varphi) = \left. \vec{\varUpsilon} \left( \omega, \vec{k} \right) \right|_{\vec{k}=\frac{2\pi}{\lambda}\vec{a}(\theta,\varphi)}
\end{equation}$$

其中 $\vec{a}(\theta,\varphi)$ 是一个单位矢量，在球坐标系中对应的角度为 $\theta$ 和 $\varphi$。可以看到，**波束方向图是频率-波数响应在一个半径为 $2\pi /\lambda$ 的球上的值**。

&emsp;&emsp;阵列的波束方向图是确定阵列性能的关键要素。后续将会研究均匀加权线阵（uniformly weighted linear array）的波束方向图。

&emsp;&emsp;在这里中，我们强调 $f\left( t,\vec{p}_n \right)$ 是带通信号的情况，即

$$\begin{equation} \label{BandpassConstraint}
  f\left( t,\vec{p}_n \right) = \sqrt{2} \Re\left\{ \tilde{f} \left( t,\vec{p}_n \right)  e^{j \omega_c t}\right\}, \, n = 0, \cdots, N-1
\end{equation}$$

其中，$\omega_c$ 是载波频率，$\tilde{f} \left( t,\vec{p}_n \right)$ 是复包络。假设复包络的带限区域为

$$\begin{equation}
  \left| \omega_L \right| \leqslant 2\pi B_s /2
\end{equation}$$

其中

$$\begin{equation}
  \omega_L \triangleq \omega - \omega_c
\end{equation}$$

且 $\pi B_s$ 是一个常数，确定了复包络的最大带宽。

&emsp;&emsp;对于式 $\eqref{DelayedSignal}$ 中的平面波，式 $\eqref{BandpassConstraint}$ 成为

$$\begin{equation} \label{BandpassConstraintApproximation}
  f\left( t,\vec{p}_n \right) = \sqrt{2} \Re\left\{ \tilde{f} \left( t - \tau_n \right)  e^{j \omega_c \left( t - \tau_n \right)}\right\}, \, n = 0, \cdots, N-1
\end{equation}$$

其中，$\tau_n$ 由式 $\eqref{RelativeDelayWithDirectionCosine}$ 给出。

&emsp;&emsp;现在考虑信号跨越阵列传播的时间（the travel time across the array）。定义 $\Delta T_{nm} \left( \vec{u} \right)$ 为一个方向余弦为 $\vec{u}$ 的平面波在第 $n$ 个阵元和第 $m$ 个阵元之间的传播时间。则，

$$\begin{equation}
  \Delta T_{\max} \triangleq \max_{n,m=0,\cdots,N-1;\vec{u}} \{\Delta T_{nm} \left( \vec{u} \right)\}
\end{equation}$$

是阵列的任意两个阵元之间的最大传播时间。对于一个线阵，这个最大值是当一个信号沿着阵列的轴线传播时（endfire），阵列两端阵元之间的传播时间。

&emsp;&emsp;假设原点位于阵列的重心，即

$$\begin{equation}
  \sum_{n=0}^{N-1} \vec{p}_n = 0
\end{equation}$$

则式 $\eqref{RelativeDelayWithDirectionCosine}$ 给出的所有 $\tau_n$ 均满足

$$\begin{equation}
  \tau_n \leqslant \Delta T_{\max},\, n = 0, \cdots, N-1
\end{equation}$$

&emsp;&emsp;在我们感兴趣的大多数情况下，复包络的带宽非常小，满足

$$\begin{equation}
  \tilde{f}(t - \tau_n) \simeq \tilde{f}(t),\, n = 0, \cdots, N-1
\end{equation}$$

为了使上述近似有效，需要有

$$\begin{equation} \label{NarrowBandConstraint}
  B_\mathrm{s}\cdot \Delta T_{\max} \ll 1
\end{equation}$$

定义复包络满足式 $\eqref{NarrowBandConstraint}$ 的带通信号为**{% label primary @窄带信号 %}**。后面我们会在有关最优处理器的内容中重新研究这个定义，并给出一个更好的量化的讨论。现在，直接采用式 $\eqref{NarrowBandConstraint}$ 。则式 $\eqref{BandpassConstraintApproximation}$ 简化为

$$\begin{equation}
  f\left( t,\vec{p}_n \right) = \sqrt{2} \Re\left\{ \tilde{f} \left( t \right)  e^{-j \omega_c \tau_n} e^{j \omega_c t } \right\}
\end{equation}$$

可以看到，在窄带的情况下，信号的延时可以用一个相移来近似。所以延时-求和波束形成器可以用一组移相器来代替延时线。结果得到的波束形成器在[图 1-1-6](#fig.1-1-6) 中给出。这种实现方式通常称为**{% label primary @相控阵 %}**，并在实际中得到了广泛的应用。

<a id="fig.1-1-6"></a>

![图 1-1-6 利用移相器实现的窄带波束形成器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-060-NarrowbandBeamformerImplementedUsingPhaseShifters.png!sign){width=600px}

&emsp;&emsp;可以发现，在很多的应用中，我们都想要调整每个传感器输出的增益和相位，以得到一个理想的波束方向图。这就产生了在[图 1-1-6(a)](#fig.1-1-6) 中给出的窄带模型。$w_n^\ast$ 是复权值，可以用一个增益和移相器的级联来实现，如[图 1-1-6(b)](#fig.1-1-6) 所示。

<a id="fig.1-1-7"></a>

![图 1-1-7 一般窄带波束形成器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-18-josh-oap-part-1-1/2023-04-18-josh-oap-part-1-1-070-GeneralNarrowbandBeamformer.png!sign){width=600px}

&emsp;&emsp;另外一种实现的结构见[图 1-1-6(c)](#fig.1-1-6)。在一些情况下，我们利用正交解调，并在对基带信号进行复加权来实现波束形成器，实际是在一个正交分量上使用 $\Re[w_n^\ast]$ 进行加权，在另外一个正交分量上使用 $\Im[w_n^\ast]$ 进行加权，结果是相等的。

&emsp;&emsp;定义一个复数权矢量为

<a id="ComplexWeightVector"></a>

$$\begin{equation} \label{ComplexWeightVector}
  \vec{w}^\mathrm{H} = [w_0^\ast, w_1^\ast, \cdots, w_{N-1}^\ast]
\end{equation}$$

则式 $\eqref{ArrayResponseToUnitPlaneWaveInTimeDomain}$ 成为

<a id="FrequencyWavenumberResponseWithComplexWeigh"></a>

$$\begin{equation}
  y\left( t,\vec{k} \right) = \vec{w}^\mathrm{H} \vec{v}_{\vec{k}}\left( \vec{k} \right) e^{j\omega t}
\end{equation}$$

且

$$\begin{equation}
  \varUpsilon \left(\omega, \vec{k} \right) = \vec{w}^\mathrm{H} \vec{v}_{\vec{k}}\left( \vec{k} \right)
\end{equation}$$

式 $\eqref{ComplexWeightVector}$ 中的定义等价于

$$\begin{equation}
  \vec{w}^\mathrm{H} = \vec{H}^\mathrm{T}(\omega_c)
\end{equation}$$

这里主要关注的是窄带模型，对于宽带信号，一种处理方法是利用离散傅里叶变换（DFT）把信号分解到更窄的频率柜 （frequency bin），每个频率柜内的信号是满足窄带条件的，因此所有的窄带结果可以直接得到应用。

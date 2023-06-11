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

$$\begin{equation} \label{ElementOfSincMatrix}
  \left[ \overrightarrow{\mathrm{sinc}} \right]_{nm} \triangleq \mathrm{sinc} \left( \frac{2\pi d}{\lambda} (n-m)\right)
\end{equation}$$

[对角调向矩阵][] 在 $u$ 空间的表示形式为

$$\begin{equation}
  \boldsymbol{I}_{\mathrm{s}u} = \mathrm{diag}\left( 1, e^{j \frac{2\pi d}{\lambda}u_\mathrm{T}} , e^{j \frac{2\pi d}{\lambda} 2u_\mathrm{T}}, \cdots, , e^{j \frac{2\pi d}{\lambda}(N-1)u_\mathrm{T}} \right)
\end{equation}$$

则

$$\begin{equation}
  D = \boldsymbol{I}_{\mathrm{s}u} \boldsymbol{w}^\mathrm{H} \left[ \overrightarrow{\mathrm{sinc}} \right] \boldsymbol{w} \boldsymbol{I}_{\mathrm{s}u}^\mathrm{H}
\end{equation}$$

通常，调向运算在权值矢量中包含[^1]

$$\begin{equation}
  \boldsymbol{w}_\mathrm{s} = \boldsymbol{w}\boldsymbol{I}_{\mathrm{s}u}^\mathrm{H}
\end{equation}$$

则

$$\begin{equation}
  D = \boldsymbol{w}_\mathrm{s}^\mathrm{H} \left[ \overrightarrow{\mathrm{sinc}} \right] \boldsymbol{w}_\mathrm{s}
\end{equation}$$

## 标准线阵的情况

&emsp;&emsp;标准线阵（standard linear array）是我们所感兴趣的一个特殊情况。如果 $d = \lambda/2$，则式 $\eqref{ResultOfDirectivityInDirectionCosineSpaceOfLA}$ 简化为

$$\begin{equation} \label{IntermediateResultOfDirectivityInDirectionCosineSpaceOfLA}
  D = \left\{ \sum_{n=0}^{N-1} \sum_{m=0}^{N-1} w_m w_n^\ast e^{j \pi (m-n) u_\mathrm{T} } \mathrm{sinc} \left( \pi (n-m)\right)  \right\}^{-1}
\end{equation}$$

当 $m = n$ 时，$\mathrm{sinc}$ 函数等于 1，当 $m \ne n$ 时，$\mathrm{sinc}$ 函数等于 0。所以式 $\eqref{IntermediateResultOfDirectivityInDirectionCosineSpaceOfLA}$ 简化为

$$\begin{equation}
  D = \left\{ \sum_{n=0}^{N-1} \left| w_n \right|^2 \right\} = \left( \boldsymbol{w}^\mathrm{H}\boldsymbol{w} \right)^{-2} = \left\{ \left\| \boldsymbol{w} \right \|^2 \right\}^{-1}
\end{equation}$$

其中

$$\begin{equation} \label{SimplifiedResultOfDirectivityInDirectionCosineSpaceOfLA}
  \left\| \boldsymbol{w} \right \| = \left( \boldsymbol{w}^\mathrm{H}\boldsymbol{w} \right)^{\frac{1}{2}}
\end{equation}$$

是矢量 $\boldsymbol{w}$ 的 2-模。

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

{% note info %}

对于非均匀加权的情况，阵列的方向性总是减小的。它是以半波长度量的阵列长度的函数，加上一个由于非均匀加权而产生的部分。

{% endnote %}

&emsp;&emsp;一个标准均匀加权线阵的方向性可以和 HPBW，或者和第一零点波束宽度相联系起来。根据[各个空间的第一零点波束宽度表][], 有

$$\begin{equation} \label{DirectivityOfStandardUWLA}
  D = \frac{4}{BW_{NN}}
\end{equation}$$

其中，$BW_{NN}$ 是在 $u$ 空间的值。注意式 $\eqref{DirectivityOfStandardUWLA}$ 适用于一个均匀加权的阵列。对于其他的加权形式，式 $\eqref{DirectivityOfStandardUWLA}$ 可能是不成立的。

&emsp;&emsp;我们经常用 dB 来表示方向性，并称之为**{% label primary @方向性指数（directivity index） %}**：

$$\begin{equation}
  DI = 10 \log_{10} D
\end{equation}$$

可以把 $DI$ 写成

$$\begin{equation}
  DI = 10 \log_{10} N + 10 \log_{10}(g(\boldsymbol{w}))
\end{equation}$$

其中的第二项是权值的函数。对于任意的非均匀加权（$w_n \ne N^{-1}$），$DI$ 将会减小。

# 对空域白噪声的阵列增益（$A_w$）

&emsp;&emsp;阵列处理的一个目标是通过相干地累加信号和不相干地累加噪声来改善信**{% label primary @信号噪声比（signal-to-noise ratio，SNR） %}**。改善的程度是用阵列增益来度量的。这是阵列性能的一个重要度量，我们将在后面深入地讨论这个问题。直到介绍了谱协方差矩阵（这是一个用于描述噪声过程的空域特性的统计性概念），我们才给出阵列增益的一般性定义。但在这里，我们给出阵列增益的一个应用范围受限的定义。

&emsp;&emsp;假设每个阵元的输入由一个从主响应轴入射的平面波加上一个噪声过程组成，各阵元的噪声过程是不相关的（空域白噪声）。所以，有

$$\begin{equation}
  x_n (t) = f(t - \tau_n) + n_n(t), \quad n = 0,\cdots,N-1
\end{equation}$$

在每个阵元，在频率 $w$ 上信号谱和噪声谱的比值为

$$\begin{equation} \label{InputSNRAtCertainFreq}
  \mathrm{SNR}_\mathrm{in} (\omega) = \frac{S_f(\omega)}{S_n (\omega)}
\end{equation}$$

其中的下标 “$\mathrm{in}$” 表示输入，假设每个传感器的噪声谱是相等的。

&emsp;&emsp;为了确定由信号和噪声产生的输出，我们需要一个用于描述输出谱的表达式，该式用波束形成器的权值和输入谱矩阵表示。根据[阵列输出的向量形式][]的表达式, 有

$$\begin{equation} \label{ArrayOutput}
y(t) = \int_{-\infty}^{\infty} \boldsymbol{h}^\mathrm{T} (\tau) \boldsymbol{x}\left(t - \tau\right) \mathrm{d} \tau
\end{equation}$$

假设 $x(t)$ 是零均值宽平稳的。输出 $y(t)$ 的相关函数为

$$\begin{equation} \label{CorrelationOfArrayOutput}
  R_{y}(\tau) = E[y(t)y^\ast(t-\tau)]
\end{equation}$$

$y(t)$ 的谱为

$$\begin{equation} \label{SpectrumOfArrayOutput}
  S_y (\omega) = \int_{-\infty}^{\infty} e^{-j\omega \tau} R_{y}(\tau) \, \mathrm{d} \tau
\end{equation}$$

根据式 $\eqref{ArrayOutput}$、$\eqref{CorrelationOfArrayOutput}$ 和 $\eqref{SpectrumOfArrayOutput}$，有

$$\begin{equation}
  S_y (\omega) = \int_{-\infty}^{\infty} e^{-j\omega\tau} \, \mathrm{d} \tau \int_{-\infty}^{\infty} \boldsymbol{h}^\mathrm{T} (\alpha) \, \mathrm{d} \alpha \int_{-\infty}^{\infty} E \left[  \boldsymbol{x} (t-\alpha) \boldsymbol{x}^\mathrm{H} (t-\tau-\beta)\right] \boldsymbol{h}^\ast(\beta) \, \mathrm{d} \beta
\end{equation}$$

也即

$$\begin{equation}
  S_y (\omega) = \int_{-\infty}^{\infty} \boldsymbol{h}^\mathrm{T} (\alpha) e^{-j\omega\alpha}  \, \mathrm{d} \alpha \int_{-\infty}^{\infty} e^{-jk\omega z} \boldsymbol{R}_x (z) \, \mathrm{d} z \int_{-\infty}^{\infty} e^{j\omega \beta} \boldsymbol{h}^\ast(\beta) \, \mathrm{d} \beta
\end{equation}$$

可以进一步简化为

$$\begin{equation} \label{SimplifiedSpectrumOfArrayOutput}
  S_y (\omega) = \boldsymbol{H}^\mathrm{T} (\omega) \boldsymbol{S}_x(\omega) \boldsymbol{H} ^\ast (\omega)
\end{equation}$$

对于窄带波束形成器，在式 $\eqref{SimplifiedSpectrumOfArrayOutput}$ 中利用[窄带假设下的复加权的表达式][]，可得到想要的结果：

$$\begin{equation}
  S_y (\omega) = \boldsymbol{w}^\mathrm{H} \boldsymbol{S}_x(\omega) \boldsymbol{w}
\end{equation}$$

为了计算由于信号而产生的输出，对 $\boldsymbol{w}$ 施加约束条件，

$$\begin{equation} \label{DistortionlessConstraint}
  \boldsymbol{w}^\mathrm{H} \boldsymbol{v}_{\boldsymbol{k}} \left( \boldsymbol{k}_\mathrm{s} \right) = 1
\end{equation}$$

在式 $\eqref{DistortionlessConstraint}$ 中的约束条件意味着从 $\boldsymbol{k}_\mathrm{s}$ 入射的任意信号将无畸变地通过波束形成器。我们把式 $\eqref{DistortionlessConstraint}$ 中的约束条件称为**{% label primary @无畸变约束（ distortionless constraint） %}**。在后续的讨论中这个条件经常使用。

&emsp;&emsp;可以把输入信号谱写成

$$\begin{equation} \label{SpectrumOfArrayInput}
  \boldsymbol{S}_{\boldsymbol{f}}(\omega) = \boldsymbol{v}_{\boldsymbol{k}} \left( \boldsymbol{k}_\mathrm{s} \right) S_f(\omega) \boldsymbol{v}_{\boldsymbol{k}}^\mathrm{H} \left( \boldsymbol{k}_\mathrm{s} \right)
\end{equation}$$

将式 $\eqref{SpectrumOfArrayInput}$ 代入利用式 $\eqref{SimplifiedSpectrumOfArrayOutput}$，得到的输出信号谱为

$$\begin{equation}
\begin{aligned}
  S_{y_\mathrm{s}} &= \boldsymbol{w}^\mathrm{H} \boldsymbol{v}_{\boldsymbol{k}} \left( \boldsymbol{k}_\mathrm{s} \right) S_f (\omega) \boldsymbol{v}_{\boldsymbol{k}}^\mathrm{H} \left( \boldsymbol{k}_\mathrm{s} \right) \boldsymbol{w} \\
  &= S_f(\omega)
\end{aligned}
\end{equation}$$

由于噪声而产生的输出谱为

$$\begin{equation}
  S_{y_\mathrm{n}} = \boldsymbol{w}^\mathrm{H} \boldsymbol{S_{\boldsymbol{n}}} (\omega) \boldsymbol{w}
\end{equation}$$

其中 $\boldsymbol{S_{\boldsymbol{n}}} (\omega)$ 是输入噪声过程的谱矩阵。对于空域白噪声，且各传感器的噪声谱相同的特殊情况，有

$$\begin{equation}
  S_{y_\mathrm{n}} = S_n (\omega) \boldsymbol{I}
\end{equation}$$

和

$$\begin{equation}
  S_{y_n} = \left\| \boldsymbol{w} \right\|^2 S_n(w) = \sum_{n=0}^{N-1} \left| \omega_n \right|^2 S_n(\omega)
\end{equation}$$

所以

$$\begin{equation} \label{OutputSNRAtCertainFreq}
  \mathrm{SNR_o}(\omega) = \frac{1}{\displaystyle\sum_{n=0}^{N-1} \left| \omega_n \right|^2} \frac{S_f(\omega)}{S_n(\omega)}
\end{equation}$$

其中，下标“$\mathrm{o}$”代表输出。

&emsp;&emsp;阵列增益 $A_w$ 反映了由于采用阵列而带来的 SNR 的改进。其定义为阵列的输出 SNR 和一个阵元上的输入 SNR 的比值。下标“$w$”表示空域不相关的噪声输入。噪声的时域频谱不一定是平坦的。利用式 $\eqref{InputSNRAtCertainFreq}$ 和式 $\eqref{OutputSNRAtCertainFreq}$，有

$$\begin{equation}
  A_w = \frac{\mathrm{SNR_o}(\omega)}{\mathrm{SNR_{in}}(\omega)} = \frac{1}{\displaystyle \sum_{n=0}^{N-1} \left| \omega_n \right|^2}
\end{equation}$$

或

$$\begin{equation} \label{DefinitionOfArrayGain}
  A_w = \left( \sum_{n=0}^{N-1} \frac{1}{\left| \omega_n \right|^2}  \right) ^{-1} = \left\| \boldsymbol{w} \right\| ^{-2}
\end{equation}$$

对于式 $\eqref{DefinitionOfArrayGain}$，可以观察到下面三个有用的结果：

1. 只要满足

   $$\begin{equation}
    \left| \boldsymbol{w}^\mathrm{H}\boldsymbol{v}_{\boldsymbol{k}} \left( \boldsymbol{k}_\mathrm{s} \right) \right|^2 = 1
   \end{equation}$$

   这个结果就适用于任意的阵列结构。

2. 对于一个标准线阵（阵元间距为 $d=\lambda/2$），白噪声阵列增益等于式 $\eqref{SimplifiedResultOfDirectivityInDirectionCosineSpaceOfLA}$ 中的阵列方向性。

3. 对于一个非均匀线阵 $d \ne \lambda/2$，$D$ 将不等于 $A_w$。一个全向噪声输入的噪声谱矩阵由式 $\eqref{ElementOfSincMatrix}$ 给出，则 $D$ 是对于一个全向噪声输入的阵列增益 $A_\mathrm{iso}$ 。

[$u$ 空间的波束方向图的表达式]: https://josh-gao.top/posts/de20fd09.html#BeamPatternInDirectionCosineDomain
[对角调向矩阵]: https://josh-gao.top/posts/c2604c43.html#DiagonalSteeringMatrix
[各个空间的第一零点波束宽度表]: https://josh-gao.top/posts/aa32ec76.html#table.1-3-2
[阵列输出的向量形式]: https://josh-gao.top/posts/8b61f5a7.html#VectorFormOfLinearArrayOutputInTimeDomain
[窄带假设下的复加权的表达式]: https://josh-gao.top/posts/8b61f5a7.html#NarrowBandComplexWeightVector

[^1]: 在大多数情况下，我们去掉下标 “$s$”，因为假设 $\boldsymbol{w}$ 是包含调向的。

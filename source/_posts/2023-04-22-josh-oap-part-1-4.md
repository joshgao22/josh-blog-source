---
title: 'Josh''s Notes: 最优阵列处理（Part 1.4 —— 阵列调向）'
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
  - 阵列调向
  - 主响应轴
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 阵列和空域滤波器
abbrlink: c2604c43
date: 2023-04-22 18:17:52
---

&emsp;&emsp;[1.1 节][]、[1.2 节][]和 [1.3 节][]已经考虑了最大响应在正侧向（broadside），即 $k_z=0$ 的阵列。在大多数应用中，我们希望能够把最大响应放置到，或者说调向（steer）到任意一个波数位置或方向上。有两种方法可以实现这个目标。直接的方法是改变传感器的位置，使得阵列的轴线和目标方向之间是垂直的，称为机械调向（mechanical steering）。当一个天线在物理上是可旋转的时候，我们可以采取这种方法，如雷达系统中抛物面孔径。通常机械调向的实现有很大的困难，原因可能是当处理长波长信号时所需阵列的物理维度较大，也可能是当传感器被移动时需要进行重新校准。

&emsp;&emsp;另外一个方法是引入时间延时（或在窄带的情况下，引入相移）来实现对一个阵列的**{% label primary @主响应轴 (main response axis，MRA)  %}**进行调向。随着高速信号处理器的发展，电子调向（electronic steering）技术正在被更广泛地应用在阵列处理中，其不仅仅突破了机械调向的限制，还具有迅速改变响应函数的灵活能力。此外，在有些阵列中，在一个方向上采用机械调向，在其他的方向上采用电子调向。

&emsp;&emsp;首先考虑任意结构阵列，然后讨论均匀加权阵列的结果。

<!-- more -->

# 任意结构阵列的调向

<a id="fig.1-4-1"></a>

![图 1-4-1 利用延时实现阵列调向](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-22-josh-oap-part-1-4/2023-04-22-josh-oap-part-1-4-010-ArraySteeringWithDelays.png!sign){width=800px}

&emsp;&emsp;[1.2 节][]开头的简单例子说明了把阵列调向某个特定方向的主要思想，调向在波数空间的影响是很直接的。考虑[图 1-4-1](#fig.1-4-1) 中的处理器，输入到调向环节的基函数为

$$\begin{equation}
  f\left( t, \vec{p}\right) = e^{j\omega t} \vec{v}_{\vec{k}}\left(\vec{k}\right)
\end{equation}$$

我们希望当波数等于“目标”的波数时，即

$$\begin{equation}
  \vec{k} = \vec{k}_\mathrm{T}
\end{equation}$$

输出是对齐的。我们把 $\vec{k}_\mathrm{T}$ 称为**{% label primary @所调方向（steering direction） %}**或在 $k$ 空间的**{% label primary @主响应轴（main response axis） %}**。可以使用一个 $N \times N$ 的对角调向矩阵来完成这个操作

$$\begin{equation}
  \vec{I}_\mathrm{s}\left(\vec{k}_\mathrm{T}\right) = \left[ \begin{matrix}
    e^{j \vec{k}^\mathrm{T}_\mathrm{T}\vec{p}_1} & 0 & \cdots & 0 \\
    0 & e^{j \vec{k}^\mathrm{T}_\mathrm{T}\vec{p}_2} & \cdots & 0 \\
    \vdots & \vdots &  & \vdots \\
    0 & 0 & \cdots & e^{j \vec{k}^\mathrm{T}_\mathrm{T}\vec{p}_N}
  \end{matrix} \right]
\end{equation}$$

得到的输出为

$$\begin{equation}
  f_\mathrm{s}\left( t, \vec{p}\right) = e^{j\omega t} \vec{v}_{\vec{k}}\left(\vec{k} - \vec{k}_\mathrm{T}\right)
\end{equation}$$

总的频率-波数响应为

$$\begin{equation}
  \vec{\varUpsilon} \left(\left. \omega, \vec{k} \right| \vec{k}_\mathrm{T} \right) = \vec{\varUpsilon} \left( \omega, \vec{k} - \vec{k}_\mathrm{T} \right)
\end{equation}$$

&emsp;&emsp;阵列的响应函数仅是简单地平移到 $\vec{k}_\mathrm{T}$ 的位置，这是利用波数空间来解释阵列响应函数的好处之一。如果在波数空间考虑波束方向图，也会得到一个简单的平移关系。

# 均匀幅度加权的情形

&emsp;&emsp;当采用均匀幅度加权时，在[图 1-4-1](#fig.1-4-1) 中的两步的过程是不必要的。令

$$\begin{equation}
  \vec{w} = \frac{1}{N} \vec{v}_{\vec{k}}\left(\vec{k}_\mathrm{T}\right)
\end{equation}$$

和

$$\begin{equation}
  B_\mathrm{c} \left( \vec{k}, \vec{k}_\mathrm{T} \right) = \frac{1}{N} \vec{v}_{\vec{k}}^\mathrm{H}  \left(\vec{k}_\mathrm{T}\right) \vec{v}_{\vec{k}}\left(\vec{k}\right)
\end{equation}$$

我们称 $B_c \left( \vec{k}, \vec{k}_\mathrm{T} \right)$ 为**{% label primary @常规波束方向图（conventional beam pattern） %}**。我们将发现常规波束方向图是后面要讨论的很多最优处理内容的基础。

# 线性阵列的调向

&emsp;&emsp;线性阵列的常规波束方向图为

$$\begin{align}
  &\psi 空间：&&\quad B_{\psi\mathrm{c}}\left( \psi : \psi_\mathrm{T} \right) = \frac{1}{N} \vec{v}_\psi^\mathrm{H}  \left(\psi_\mathrm{T}\right) \vec{v}_\psi\left(\psi\right) \\
  &u 空间：&&\quad B_{u\mathrm{c}}\left( u : u_\mathrm{T} \right) = \frac{1}{N} \vec{v}_u^\mathrm{H}  \left(u_\mathrm{T}\right) \vec{v}_u\left(u\right) \\
\end{align}$$

对于均匀线性阵列，有

$$\begin{align}
  &\psi 空间：&&\quad B_{\psi\mathrm{c}}\left( \psi : \psi_\mathrm{T} \right) = \frac{1}{N} \frac{\sin\left( N \frac{\psi-\psi_\mathrm{T}}{2} \right)}{\sin\left(\frac{\psi-\psi_\mathrm{T}}{2} \right)} \\
  &u 空间：&&\quad B_{u\mathrm{c}}\left( u : u_\mathrm{T} \right) = \frac{1}{N} \vec{v}_u^\mathrm{H}  \left(u_\mathrm{T}\right) \vec{v}_u\left(u\right) \\
\end{align}$$

[1.1 节]: https://josh-gao.top/posts/8b61f5a7.html
[1.2 节]: https://josh-gao.top/posts/de20fd09.html
[1.3 节]: https://josh-gao.top/posts/aa32ec76.html

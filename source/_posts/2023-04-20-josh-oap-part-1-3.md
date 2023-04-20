---
title: 'Josh''s Notes: 最优阵列处理（Part 1.3 —— 均匀加权线阵）'
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
  - 均匀加权线阵
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 阵列和空域滤波器
abbrlink: aa32ec76
date: 2023-04-20 19:34:47
---

&emsp;&emsp;现在把注意力集中到均匀加权的情况

$$\begin{equation}
  w_n = \frac{1}{N}, \, n = 0,1,\cdots,N-1
\end{equation}$$

$$\begin{equation}
  \vec{w} = \frac{1}{N} \vec{1}
\end{equation}$$

其中 1 是 $N \times 1$ 维的单位矢量

则在 $\psi$ 空间的频率-波数函数可以写成（利用等比级数的求和公式）

$$\begin{equation}
  \begin{aligned}
    \vec{\varUpsilon}_\psi(\psi) &= \frac{1}{N} \sum_{n=0}^{N-1}  e^{j \left(n - \frac{N-1}{2}\right)\psi} \\
    &= \frac{1}{N} e^{-j \left(\frac{N-1}{2}\right)\psi} \sum_{n=0}^{N-1} e^{j n \psi} \\
    &= \frac{1}{N} e^{-j \left(\frac{N-1}{2}\right)\psi} \left[ \frac{1-e^{jN\psi}}{1-e^{j\psi}} \right]
  \end{aligned}
\end{equation}$$

或（利用欧拉公式）

$$\begin{equation}
  \vec{\varUpsilon}_\psi(\psi) = \frac{1}{N} \frac{\sin\left( N \frac{\psi}{2} \right)}{\sin\frac{\psi}{2}}
\end{equation}$$

<!-- more -->

观察到，当 $N$ 是奇数时，$\vec{\varUpsilon}_\psi(\psi)$ 是周期函数，周期为 $2\pi$。如果 $N$ 为偶数，波瓣在 $\pm 2 \pi$、$\pm 6 \pi$ 处的值为负值，周期为 $4 \pi$。对任意 $N$ 值， $\left|\vec{\varUpsilon}_\psi(\psi)\right|$ 的周期为 $2 \pi$。当 $N = 11$  时， $\vec{\varUpsilon}_\psi(\psi)$ 和 $\psi$ 的关系在[图 1-3-1](#fig.1-3-1) 中给出。[图 1-3-2](#fig.1-3-2) 给出了 $\left|\vec{\varUpsilon}_\psi(\psi)\right|$，单位为 dB, 其中

$$\begin{equation}
  \vec{\varUpsilon}_\mathrm{dB}(\psi) = 10 \log_{10} \left|\vec{\varUpsilon}_\psi(\psi)\right|^2
\end{equation}$$

<a id="fig.1-3-1"></a>

![图 1-3-1 $\vec{\varUpsilon}(\psi): \psi = \frac{2\pi}{\lambda} d \cos\theta, N=11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-20-josh-oap-part-1-3/2023-04-20-josh-oap-part-1-3-010-FrequencyWavenumberResponseFunction.svg){width=700px}

<a id="fig.1-3-2"></a>

![图 1-3-1 用 dB 表示 $\left|\vec{\varUpsilon}(\psi)\right|$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-20-josh-oap-part-1-3/2023-04-20-josh-oap-part-1-3-020-FrequencyWavenumberResponseFunctionInDB.svg){width=700px}

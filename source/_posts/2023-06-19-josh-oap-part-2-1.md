---
title: 'Josh''s Notes: 最优阵列处理（Part 2.1 —— 谱加权）'
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: false
  wrap: true
  expand_all: true
  max_depth: 4
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 线形阵列和孔径的合成
tags:
  - 阵列信号处理
  - 谱加权
  - 线阵
abbrlink: 61f0754c
date: 2023-06-19 16:25:26
---

&emsp;&emsp;下面主要研究对每个传感器输出进行加权的技术，以获得具有理想性质的频率-波数响应和波束方向图。加权的过程在一些文献中也称为锐化 (tapering) 或赋形 (shading) 。许多阵列加权技术等价于时间序列谱分析中的加窗或锐化 (Taper) 技术。

&emsp;&emsp;对于一个阵元间距小于或等于 $\lambda/2$ 的线阵，由于不需要考虑栅瓣和空域混迭，可以直接应用连续函数的傅里叶变换理论对权重和频率-波数响应之间存在的傅里叶变换关系进行分析。本文关注线性阵列，讨论如何在降低旁瓣的同时使主瓣宽度的增长最小（最小化方向性损失），将尝试不同的权值矢量，并分析它们的性能。

&emsp;&emsp;考虑傅里叶变换对

$$\begin{equation}
  \varUpsilon(\omega,k) = \int_{-\infty}^\infty w_a^\ast (z) e^{-jkz}\, \mathrm{d}z
\end{equation}$$

$$\begin{equation}
  w_a^\ast (z) = \frac{1}{2\pi} \int_{-\infty}^\infty \varUpsilon(\omega,k) e^{jkz}\, \mathrm{d}k
\end{equation}$$

其中省略了 $k_z$ 的下标 $z$。

<!-- more -->

# 1. 均匀加权

&emsp;&emsp;均匀权值为

$$\begin{equation}
  w_n = \frac{1}{N}, \quad n = 0,\cdots,N-1
\end{equation}$$

在 $u$ 空间的波束方向图为

$$\begin{equation}
  \begin{aligned}
    B_u(u) &= \frac{1}{N} \frac{\sin\left( \displaystyle\frac{\pi Nd}{\lambda} u \right)}{\sin\left( \displaystyle\frac{\pi d}{\lambda} u \right)} \\
    &= \boldsymbol{w}^\mathrm{H}\boldsymbol{v}_u(u) = e^{-j\left(\frac{N-1}{2} \right) \frac{2\pi d}{\lambda} u} \sum_{n=0}^{N-1} w_n^\ast e^{j n \frac{2\pi d}{\lambda} u}
  \end{aligned}
\end{equation}$$

对于**标准线阵**，有

$$\begin{equation}
  B_u(u) = \frac{1}{N} \frac{\sin\left( \displaystyle\frac{\pi Nu}{2} \right)}{\sin\left( \displaystyle\frac{\pi u}{2}  \right)}
\end{equation}$$

方向性

$$\begin{equation}
  D = N
\end{equation}$$

由于下面经考虑实对称的权值，因此将阵元的位置也对称地进行表示，第 $n$ 个阵元的位置表示为

$$\begin{equation}
  \tilde{n} = n - \frac{N-1}{2}, \quad n = 0,\cdots,N-1,\quad \tilde{n} = -\frac{N-1}{2}, \cdots, \frac{N-1}{2}
\end{equation}$$

# 2. Cosine 加权

&emsp;&emsp;当 $N$ 为奇数时，Cosine 权值为

$$\begin{equation}
  w(\tilde{n}) = \sin\left( \frac{\pi}{2N} \right)\cos\left( \pi\frac{\tilde{n}}{N} \right), \quad -\frac{N-1}{2} \leqslant \tilde{n} \leqslant \frac{N-1}{2}
\end{equation}$$

其中归一化因子 $\sin\left( \displaystyle\frac{\pi}{2N} \right)$ 是一个常数，使得 $B_u (0) = 1$。把 Cosine 写成指数形式，有

$$\begin{equation}
  w(\tilde{n}) = \sin\left( \frac{\pi}{2N} \right)\left[ \frac{e^{j\frac{\pi\tilde{n}}{N}} + e^{-j\frac{\pi\tilde{n}}{N}}}{2} \right], \quad -\frac{N-1}{2} \leqslant \tilde{n} \leqslant \frac{N-1}{2}
\end{equation}$$

利用指数形式的阵列流形矢量，可得波束方向图

$$\begin{equation}
  B_u(u) = \frac{1}{2} \sin\left( \frac{\pi}{2N} \right) \left\{ \sum_{\tilde{n} = -\frac{N-1}{2}}^{\frac{N-1}{2}} e^{j\frac{\pi \tilde{n}}{N}} e^{-j \tilde{n} \pi u} + \sum_{\tilde{n} = -\frac{N-1}{2}}^{\frac{N-1}{2}} e^{-j\frac{\pi \tilde{n}}{N}} e^{-j \tilde{n} \pi u} \right\}
\end{equation}$$

其中第一项和第二项分别对应指向 $u_s = 1/N$ 和 $u_s = -1/N$ 的常规波束方向图，因此

$$\begin{equation}
  B_u(u) = \frac{1}{2} \sin\left( \frac{\pi}{2N} \right) \left\{ \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u - \frac{1}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u - \frac{1}{N} \right) \right)} + \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u + \frac{1}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u + \frac{1}{N} \right) \right)} \right\}
\end{equation}$$

下面将要介绍的很多方向图都是常规波束的叠加。[图 2-1-1](#fig.2-1-1) 给出了一个 11 阵元阵列使用 Cosine 加权得到的波束方向图，同时还给出了常规波束方向图作为参考。可以看到，旁瓣得到了降低，但是主波束变得更宽。两个波束方向图的参数为

| 加权方式 | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$[^1] |
| :--: | :--: | :--: | :--: | :--: |
| Uniform | $0.89 \displaystyle\frac{2}{N}$ | $2.0 \displaystyle\frac{2}{N}$ | $-13.0 \text{dB}$ | $1$ |
| Cosine | $1.18 \displaystyle\frac{2}{N}$ | $3.0 \displaystyle\frac{2}{N}$ | $-23.5 \text{dB}$ | $0.816$ |

<a id="fig.2-1-1"></a>

![图 2-1-1 均匀加权和 Cosine 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-010-BeamPatternForUniformAndCosineWeighting.svg){width=700px}

# 3. 升 Cosine 加权

&emsp;&emsp;可以把矩形均匀加权和 Cosine 加权组合起来，获得两种加权方法一些理想性质的组合。对应的阵列加权为

$$\begin{equation}
  w(\tilde{n}) = c(p) \left( p + (1-p) \cos \left( \pi \frac{\tilde{n}}{N} \right)\right), \quad \tilde{n} = -\frac{N-1}{2}, \cdots, \frac{N-1}{2}
\end{equation}$$

其中归一化常数

$$\begin{equation}
  c(p) = \frac{p}{N} + \frac{1-p}{2} \sin\left( \frac{\pi}{2N} \right)
\end{equation}$$

使得 $B_u(0) = 1$。

&emsp;&emsp;在[图 2.1.2](#fig.2-1-2) 中给出了 $p = 0.31, 0.17, 0$ 时的升 Cosine 加权的波束方向图。当 $p$ 减小时，第一旁瓣的高度减小，主波束宽度增加。波束方向图参数如下：

| $p$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$[^1] |
| :--: | :--: | :--: | :--: | :--: |
| 0.31 | $1.03 \displaystyle\frac{2}{N}$ | $2.50 \displaystyle\frac{2}{N}$ | $-20.0 \text{dB}$ | $0.928$ |
| 0.17 | $1.09 \displaystyle\frac{2}{N}$ | $3.70 \displaystyle\frac{2}{N}$ | $-22.0 \text{dB}$ | $0.886$ |
| 0 | $1.18 \displaystyle\frac{2}{N}$ | $3.00 \displaystyle\frac{2}{N}$ | $-23.5 \text{dB}$ | $0.816$ |

<a id="fig.2-1-2"></a>

![图 2-1-2 升 Cosine 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-020-BeamPatternForRaisedCosineWeighting.svg){width=700px}

&emsp;&emsp;至此，我们已经可以使 HPBW 变窄，并使得第一旁瓣比均匀分布的情况要低得多。

# 4. Cosine$^m$ 加权

&emsp;&emsp;下面考虑一系列 Cosine 加权，形式为 $\cos^m(\pi \tilde{n} / N)$。阵列的权值为

$$\begin{equation}
  w_m(\tilde{n}) = \begin{cases}
    c_2 \cos^2 \left( \displaystyle \frac{\pi \tilde{n}}{N} \right),\quad m = 2 \\
    c_3 \cos^3 \left( \displaystyle \frac{\pi \tilde{n}}{N} \right),\quad m = 3 \\
    c_4 \cos^4 \left( \displaystyle \frac{\pi \tilde{n}}{N} \right),\quad m = 4 \\
  \end{cases}
\end{equation}$$

其中 $c_2, c_3, c_4$ 是归一化常数。

&emsp;&emsp;$m=2$ 时的加权有时称为是 Hann 加权[^2]。同样，波束方向图可以利用 Cosine 函数的指数形式来计算。波束方向图在[图 2.1.3](#fig.2-1-3) 中给出。当 $m$ 增加时，旁瓣减小，但是主波束变宽。波束方向图的参数为:

| $m$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$[^1] |
| :--: | :--: | :--: | :--: | :--: |
| 2 | $1.44 \displaystyle\frac{2}{N}$ | $4 \displaystyle\frac{2}{N}$ | $-31.4 \text{dB}$ | $0.667$ |
| 3 | $1.66 \displaystyle\frac{2}{N}$ | $5 \displaystyle\frac{2}{N}$ | $-39.4 \text{dB}$ | $0.576$ |
| 4 | $1.85 \displaystyle\frac{2}{N}$ | $6 \displaystyle\frac{2}{N}$ | $-46.7 \text{dB}$ | $0.514$ |

<a id="fig.2-1-3"></a>

![图 2-1-3 Cosine$^m$ 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-030-BeamPatternForCosineMWeighting.svg){width=700px}

# 5. 升 Cosine 平方加权

&emsp;&emsp;升 Cosine 平方系列的加权为

$$\begin{equation}
  \begin{aligned}
    w(\tilde{n}) &= c_2 (p) \left\{ p + (1-p) \cos^2 \left( \frac{\pi \tilde{n}}{N} \right) \right\} \\
    &= \frac{c_2(p)}{2} \left\{ (1+p) + (1-p) \cos \left( \frac{2 \pi \tilde{n}}{N} \right) \right\}, \quad \tilde{n} = -\frac{N-1}{2}, \cdots, \frac{N-1}{2}
  \end{aligned}
\end{equation}$$

其中 $c_2 (p)$ 是归一化常数。下面考虑一个特别的加权，称为 Hamming 加权。

# 6. Hamming 加权

&emsp;&emsp;Hamming 加权利用了矩形方向图和 Cosine 平方方向图的特点，在第一旁瓣的峰值上放置了一个零点。加权函数为

$$\begin{equation}
  w(\tilde{n}) = g_0 + g_1 \cos \left( \frac{2 \pi \tilde{n}}{N} \right), \quad \tilde{n} = -\frac{N-1}{2}, \cdots, \frac{N-1}{2}
\end{equation}$$

选择系数 $g_0$ 和 $g_1$ 的使 $u = 3/N$ 的位置上有一个零点，并将正侧向的响应归一化，有

$$\begin{equation}
  w(\tilde{n}) = 0.54 + 0.46 \cos \left( \frac{2 \pi \tilde{n}}{N} \right), \quad -\frac{N-1}{2} \leqslant \tilde{n} \leqslant \frac{N-1}{2}
\end{equation}$$

对应升 Cosine 平方加权中 $p = 0.08$ 的情况，得到的波束方向图是三个常规波束方向图的和

$$\begin{equation}
  B_u(u) = 0.54 \frac{\sin\left(\displaystyle\frac{N\pi u}{2}\right)}{\sin\left(\displaystyle\frac{\pi u}{2}\right)} + 0.23 \left[ \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u - \frac{2}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u - \frac{2}{N} \right) \right)} + \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u + \frac{2}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u + \frac{2}{N} \right) \right)} \right]
\end{equation}$$

&emsp;&emsp;[图 2.1.4](#fig.2-1-4) 给出了 Hamming 加权、Hann 加权和 Blackman-Harris 加权的波束方向图。第一个零点的位置在 $u = 4/N$，第一个非零旁瓣的高度为 $-39.5\text{dB}$。加权有步进不连续性，使得旁瓣的衰减渐近为 $O(1/n)$。但是，第一个旁瓣被完全对消掉了，剩余的旁瓣都很低，尽管它们的衰减比较慢。而且，注意到 Hamming 窗的波束宽度小于 Hann 加权的波束宽度，且第一旁瓣更低。这是一般变化趋势的一个例外情况。

<a id="fig.2-1-4"></a>

![图 2-1-4 Hann 加权、Hamming 加权和 Blackman-Harris 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-040-BeamPatternForHannHammingAndBlackmanHarrisWeighting.svg){width=700px}


[^1]: 参数 $D_N$ 是阵列的归一化方向性。它是相对于一个均匀加权阵列的方向性进行归一化的。对于标准线阵, $D_N$ 也是归一化的白噪声增益。

[^2]: 这个权值是奥地利气象学家 Von Hann 提出的，有时也称为 Hanning 加权。

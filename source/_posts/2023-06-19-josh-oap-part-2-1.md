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

&emsp;&emsp;谱加权（Spectral Weighting）技术主要研究对如何每个传感器输出进行加权，以获得具有理想性质的频率-波数响应和波束方向图。加权的过程在一些文献中也称为锐化 (tapering) 或赋形 (shading) 。许多阵列加权技术等价于时间序列谱分析中的加窗或锐化 (Taper) 技术。

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

{% note primary MATLAB Code %}

``` matlab fig3_01.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.1
% Cosine weighting: Array beam patterns
% Kristine Bell
% Modified by Xin Zhang 3/17/99
% Lillian 04/16/2001, K. Bell 7/20/01, K. Bell 9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all
close all

M=11;
d=0.5;                        % sensor spacing wrt wavelength
D = (-(M-1)/2:1:(M-1)/2)*d;   % sensor positions in wavelengths
% weights, normalized so that w(0)=1
W_unf = ones(1,M);
W_cos = cos(pi*D*2/M);

% Beampatterns
u = 0:0.001:1;
A = exp(-1i*2*pi*D.'*u);
G_unf = W_unf*A;
G_unf = G_unf/(max(abs(G_unf)));
G_cos = W_cos*A;
G_cos = G_cos/(max(abs(G_cos)));
figure
% array only
clf
plot(u,20*log10(abs(G_unf)),'--')
hold on
plot(u,20*log10(abs(G_cos)),'-')
hold off
axis([0 1 -80 0])
%title([int2str(M) ' element array'])
h=legend('Uniform','Cosine');
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

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

| $p$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$ |
| :--: | :--: | :--: | :--: | :--: |
| 0.31 | $1.03 \displaystyle\frac{2}{N}$ | $2.50 \displaystyle\frac{2}{N}$ | $-20.0 \text{dB}$ | $0.928$ |
| 0.17 | $1.09 \displaystyle\frac{2}{N}$ | $3.70 \displaystyle\frac{2}{N}$ | $-22.0 \text{dB}$ | $0.886$ |
| 0 | $1.18 \displaystyle\frac{2}{N}$ | $3.00 \displaystyle\frac{2}{N}$ | $-23.5 \text{dB}$ | $0.816$ |

<a id="fig.2-1-2"></a>

![图 2-1-2 升 Cosine 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-020-BeamPatternForRaisedCosineWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_02.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.2
% Raised cosine weighting function
% Kristine Bell
% Modified by Xin Zhang 3/17/99
% Lillian Xu 04/16/2001, K. Bell 7/20/01, 9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all
close all

M=11;
d=0.5;                        % sensor spacing wrt wavelength
D = (-(M-1)/2:1:(M-1)/2)*d;   % sensor positions in wavelengths

% weights, normalized so that w(0)=1
p=[0;0.17;0.31;1];
W_rcos = p*ones(1,M)+(ones(4,1)-p)*cos(pi*D*2/M);

% Beampatterns
u = 0:0.001:1;
c=p+(ones(4,1)-p)*2/pi;
A = exp(-1i*2*pi*D.'*u);
G_rcos = W_rcos*A;
for m=1:4
 G_rcos(m,:) = G_rcos(m,:)/(max(abs(G_rcos(m,:))));
end

figure
clf
plot(u,20*log10(abs(G_rcos(3,:))),'-.')
hold on
plot(u,20*log10(abs(G_rcos(2,:))),'--')
plot(u,20*log10(abs(G_rcos(1,:))),'-')
hold off
axis([0 1 -80 0])
h=legend({'$p=0.31$','$p=0.17$','$p=0$'},'Interpreter','latex');
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

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

| $m$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$ |
| :--: | :--: | :--: | :--: | :--: |
| 2 | $1.44 \displaystyle\frac{2}{N}$ | $4 \displaystyle\frac{2}{N}$ | $-31.4 \text{dB}$ | $0.667$ |
| 3 | $1.66 \displaystyle\frac{2}{N}$ | $5 \displaystyle\frac{2}{N}$ | $-39.4 \text{dB}$ | $0.576$ |
| 4 | $1.85 \displaystyle\frac{2}{N}$ | $6 \displaystyle\frac{2}{N}$ | $-46.7 \text{dB}$ | $0.514$ |

<a id="fig.2-1-3"></a>

![图 2-1-3 Cosine$^m$ 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-030-BeamPatternForCosineMWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_03.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.3
% Cosine^m weighting: m = 2,3,and 4
% Kristine Bell
% Modified by Xin Zhang 3/17/99
% Lillian Xu 04/16/2001, K. Bell 7/20/01, 9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all
close all

M=11;
d=0.5;                        % sensor spacing wrt wavelength
D = (-(M-1)/2:1:(M-1)/2)*d;   % sensor positions in wavelengths

% weights, normalized so that w(0)=1
W_cos2 = cos(pi*D*2/M).^2;
W_cos3 = cos(pi*D*2/M).^3;
W_cos4 = cos(pi*D*2/M).^4;

% Beampatterns

u = 0:0.001:1;
A = exp(-1i*2*pi*D.'*u);
G_cos2 = W_cos2*A;
G_cos2 = G_cos2/(max(abs(G_cos2)));
G_cos3 = W_cos3*A;
G_cos3 = G_cos3/(max(abs(G_cos3)));
G_cos4 = W_cos4*A;
G_cos4 = G_cos4/(max(abs(G_cos4)));

figure
clf
plot(u,20*log10(abs(G_cos2)),'--')
hold on
plot(u,20*log10(abs(G_cos3)),'-')
plot(u,20*log10(abs(G_cos4)),'-.')
hold off
axis([0 1 -80 0])
h=legend({'$m=2$','$m=3$','$m=4$'},'Interpreter','latex');
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

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

&emsp;&emsp;[图 2.1.4](#fig.2-1-4) 给出了 Hamming 加权、Hann 加权和 Blackman-Harris 加权的波束方向图。第一个零点的位置在 $u = 4/N$，第一个非零旁瓣的高度为 $-39.5\text{dB}$。由于加权具有步进不连续性（step discontinuity），旁瓣的衰减渐近为 $O(1/n)$。但是，第一个旁瓣被完全对消掉了，尽管剩余的旁瓣衰减得比较慢，它们的幅度都不是很高。而且注意到 Hamming 窗的波束宽度小于 Hann 加权的波束宽度，且第一旁瓣更低。

<a id="fig.2-1-4"></a>

![图 2-1-4 Hann 加权、Hamming 加权和 Blackman-Harris 加权的波束方向图: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-040-BeamPatternForHannHammingAndBlackmanHarrisWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_04.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.4
% Beam patterns for Hann, Hamming, and Blackman-Harris
% Kristine Bell
% Modified by Xin Zhang 3/17/99
% Lillian Xu 04/16/2001, K. Bell 7/20/01, 9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% Hann, Hamming, Blackman-Harris
% M-element array, d=lambda/2

clear all
close all

M=11;
d=0.5;                        % sensor spacing wrt wavelength
D = (-(M-1)/2:1:(M-1)/2)*d;   % sensor positions in wavelengths

% weights, normalized so that w(0)=1
W_Hann = 0.5*(ones(1,M)+cos(2*pi*D*2/M));
W_Hamm = 0.54*ones(1,M)+0.46*cos(2*pi*D*2/M);
W_BH   = 0.42*ones(1,M)+0.5*cos(2*pi*D*2/M)+0.08*cos(4*pi*D*2/M);

% Beampatterns

u = 0:0.001:1;
A = exp(-1i*2*pi*D.'*u);
G_Hann = W_Hann*A;
G_Hann = G_Hann/(max(abs(G_Hann)));
G_Hamm = W_Hamm*A;
G_Hamm = G_Hamm/(max(abs(G_Hamm)));
G_BH = W_BH*A;
G_BH = G_BH/(max(abs(G_BH)));

figure
clf
plot(u,20*log10(abs(G_Hann)),'--')
hold on
plot(u,20*log10(abs(G_Hamm)),'-')
plot(u,20*log10(abs(G_BH)),'-.')
hold off
axis([0 1 -80 0])
h=legend('Hann','Hamming','Blackman-Harris');
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

# 7. Blackman-Harris 加权

&emsp;&emsp;Blackman-Harris 加权把加权过程扩展到了更高阶谐波的情况，并在前两个旁瓣的峰值位置上放置了零点。加权函数为

$$\begin{equation}
  w(\tilde{n}) = 0.42 + 0.5 \cos\left( \frac{2\pi \tilde{n}}{N} \right) + 0.08 \cos\left( \frac{4\pi \tilde{n}}{N} \right), \quad \tilde{n} = -\frac{N-1}{2}, \cdots, \frac{N-1}{2}
\end{equation}$$

对应的波束方向图是多个常规波束方向图的和，已在[图 2.1.4](#fig.2-1-4) 中给出

$$\begin{equation}
  \begin{aligned}
    B_u(u) =& 0.42 \frac{\sin\left( \displaystyle\frac{N\pi u}{2} \right)}{\sin\left( \displaystyle\frac{\pi u}{2} \right)} + 0.25 \left[ \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u - \frac{2}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u - \frac{2}{N} \right) \right)} + \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u + \frac{2}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u + \frac{2}{N} \right) \right)} \right] \\
    &+ 0.04 \left[ \frac{\sin\left( \displaystyle\frac{N\pi}{2} \left( u - \frac{2}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{2} \left( u - \frac{2}{N} \right) \right)} + \frac{\sin\left( \displaystyle\frac{N\pi}{4} \left( u + \frac{4}{N} \right) \right)}{\sin\left( \displaystyle\frac{\pi}{4} \left( u + \frac{4}{N} \right) \right)} \right]
  \end{aligned}
\end{equation}$$

波束方向图的参数为

| 加权方式 | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$ |
| :--: | :--: | :--: | :--: | :--: |
| Hann | $1.44 \displaystyle\frac{2}{N}$ | $4.0 \displaystyle\frac{2}{N}$ | $-31.4 \text{dB}$ | $0.667$ |
| Hamming | $1.31 \displaystyle\frac{2}{N}$ | $4.0 \displaystyle\frac{2}{N}$ | $-39.5 \text{dB}$ | $0.730$ |
| Blackman-Harris | $1.65 \displaystyle\frac{2}{N}$ | $6.0 \displaystyle\frac{2}{N}$ | $-56.6 \text{dB}$ | $0.577$ |

至此，所有的加权方法都是基于各种正弦函数进行的。现在看看其他类型的阵列加权技术，试试能否更好在波束宽度和旁瓣之间的获得折中。

# 8. 长球函数 (Prolate Spheroidal Functions)

&emsp;&emsp;在设计加权方案时，我们的目标是使得一个给定角度范围内的能量占总能量的比例最大，也即最大化如下比值

$$\begin{equation}
  \alpha = \frac{\displaystyle\iint_{\varOmega_1}\left| B(\theta,\varphi) \right|^2 \sin\theta\,\mathrm{d}\theta\,\mathrm{d}\varphi}{\displaystyle\int_0^\pi \int_0^{2\pi}\left| B(\theta,\varphi) \right|^2 \sin\theta\,\mathrm{d}\varphi\,\mathrm{d}\theta}
\end{equation}$$

其中 $\varOmega_1$ 是主波束周围的一个区域。对于标准线性阵列，可以写成

$$\begin{equation}
  \alpha = \frac{\displaystyle\int_{-\psi_0}^{\psi_0}\left| B(\psi) \right|^2\,\mathrm{d}\psi}{\displaystyle\int_{-\pi}^{\pi}\left| B(\psi) \right|^2\,\mathrm{d}\psi}
\end{equation}$$

其分子和分母部分可分别写为

$$\begin{align}
  &\alpha_N &&\xlongequal{B(\psi) = \boldsymbol{w}^\mathrm{H}\boldsymbol{v}_\psi(\psi)} \int_{-\psi_0}^{\psi_0}\boldsymbol{w}^\mathrm{H}\boldsymbol{v}_\psi(\psi) \boldsymbol{v}_\psi^\mathrm{H}(\psi)\boldsymbol{w}\,\mathrm{d}\psi \notag \\
  & &&= \boldsymbol{w}^\mathrm{H} \left[ \int_{-\psi_0}^{\psi_0} \boldsymbol{v}_\psi(\psi) \boldsymbol{v}_\psi^\mathrm{H}(\psi) \,\mathrm{d}\psi \right]\boldsymbol{w} \notag \\
  & &&= \boldsymbol{w}^\mathrm{H}\boldsymbol{A}\boldsymbol{w} \\

  &\alpha_D &&\xlongequal{B(\psi) = \boldsymbol{w}^\mathrm{H}\boldsymbol{v}_\psi(\psi)}  \int_{-\pi}^{\pi}\boldsymbol{w}^\mathrm{H}\boldsymbol{v}_\psi(\psi) \boldsymbol{v} \notag \\
  & &&= \boldsymbol{w}^\mathrm{H}\boldsymbol{A}\boldsymbol{w}
\end{align}$$

其中

$$\begin{align}
  \boldsymbol{A} &\triangleq \int_{-\psi_0}^{\psi_0} \boldsymbol{v}_\psi(\psi) \boldsymbol{v}_\psi^\mathrm{H}(\psi) \,\mathrm{d}\psi \\
  \boldsymbol{B} &\triangleq \int_{-\pi}^{\pi} \boldsymbol{v}_\psi(\psi) \boldsymbol{v}_\psi^\mathrm{H}(\psi) \,\mathrm{d}\psi = 2\pi \boldsymbol{I}
\end{align}$$

$$\begin{equation}
  [\boldsymbol{A}]_{mn} = \int_{-\psi_0}^{\psi_0} e^{jm\psi} e^{-jn\psi} \,\mathrm{d}\psi = \frac{2\sin ((m-n)\psi_0)}{m-n} = 2\psi_0 \,\mathrm{sinc}((m-n)\psi_0)
\end{equation}$$

因此

$$\begin{equation}
  \alpha = \frac{\boldsymbol{w}^\mathrm{H}\boldsymbol{A}\boldsymbol{w}}{2\pi \boldsymbol{w}^\mathrm{H}\boldsymbol{w}}
\end{equation}$$

为了使 $\alpha$ 最大，求取矩阵 $\boldsymbol{A}$ 的特征值和特征矢量

$$\begin{equation}
  2\pi \lambda \boldsymbol{w} = \boldsymbol{A} \boldsymbol{w}
\end{equation}$$

并选择对应于最大特征值 $\lambda$ 的特征矢量，将 $[\boldsymbol{A}]_{mn}$ 代入得到

$$\begin{equation}
  \label{DPSS}
  \sum_{n=1}^N \frac{\sin ((m-n)\psi_0)}{m-n} w_n = \pi \lambda \omega_m,\quad m = 1,2,\cdots,N
\end{equation}$$

利用式 $\eqref{DPSS}$ 计算出的权值（或序列）称为**{% label primary @离散长球序列（Discrete Prolate Spheroidal Sequences, DPSS %}**，对应的波束方向图称为**{% label primary @离散长球函数（Discrete Prolate Spheroidal Functions）%}**。离散长球序列也称为**{% label primary @Slepian 序列 %}**。

&emsp;&emsp;[图 2.1.5](#fig.2-1-5) 给出了几个代表性的离散长球序列（归一化到 $w_6$），[图 2.1.6](#fig.2-1-6) 给出了对应的波束方向图：

- 当 $\psi_0$ 接近于零的时候，$w_0$ 趋近于均匀加权，因为此时使方向性最大；
- 对于 $\psi_0 = 0.2\pi$，大多数的能量集中在主波束，波束宽度略增大，旁瓣在 $-20\text{dB}$ 以下；
- 对于$\psi_0 = 0.4\pi$，旁瓣为 $-53\text{dB}$，但波束更宽。

DPSS 加权的波束方向图参数为:

| $\psi_0$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$ |
| :--: | :--: | :--: | :--: | :--: |
| $0.1\pi$ | $\displaystyle\frac{0.02}{N}$ | $\displaystyle\frac{1.40\pi}{N}$ | $-15.6 \text{dB}$ | $0.981$ |
| $0.2\pi$ | $\displaystyle\frac{2.20}{N}$ | $\displaystyle\frac{1.79\pi}{N}$ | $-24.7 \text{dB}$ | $0.869$ |
| $0.4\pi$ | $\displaystyle\frac{2.86}{N}$ | $\displaystyle\frac{2.97\pi}{N}$ | $-52.2 \text{dB}$ | $0.665$ |

<a id="fig.2-1-5"></a>

![图 2-1-5 离散长球序列: $\psi_0=0.1\pi,0.2\pi,0.4\pi$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-050-DiscreteProlateSpheroidalSequences.svg){width=700px}

<a id="fig.2-1-6"></a>

![图 2-1-6 DPSS 加权的波束方向图和 $\psi_0$ 的关系: $N = 11$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-060-BeamPatternForDPSSWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_05_06_13.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.5
% DPSS psi0=0.1pi,0.2pi, and 0.4pi
% Figure 3.6
% Beampattern of a standard 11-element linear array as a function
% Figure 3.13
% Zero plots for DPSS(0.1pi,0.2pi,0.4pi) weightings
%
% Lillian Xiaolan Xu
% Updated 4/14/99
% Lillian Xu 04/16/2001, K. Bell 7/22/01, 9/30/01
% Functions called: DPSS
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

close all
clear all
N=11;

us=0;                           % should change us between 0 and 0.2
u=-1:0.001:1;
n=conj(-(N-1)/2:(N-1)/2)';
vs=exp(1i*n.*pi.*us);

%------------------------ DPSS Weighting
psi0=[0.1 0.2 0.4]*pi;
w1=DPSS(psi0(1),N);
w2=DPSS(psi0(2),N);
w3=DPSS(psi0(3),N);

z1=roots(w1);
z2=roots(w2);
z3=roots(w3);

w1=w1/max(w1);
w2=w2/max(w2);
w3=w3/max(w3);

m=-5:5;
figure
h1 = plot(m,w1);
hold on;
h2 = plot(m,w1,'o');
h3 = plot(m,w2,'--');
h4 = plot(m,w2,'o');
h5 = plot(m,w3,'-.');
h6 = plot(m,w3,'o');
hold off
h=legend([h1 h3 h5],{'$\psi_0=0.1\pi$','$\psi_0=0.2\pi$', ...
  '$\psi_0=0.4\pi$'},Interpreter="latex");
%title('DPSS for 11 elements');
xticks(-5:5);
xlabel('Sensors');
ylabel('Weighting');
grid on

%-----------------------------------------------------------%
% ---------------- Zero Plot.
figure
%%%%%%%%%%%%      plot 1

subplot(1,2,1);
plot(real(z1),imag(z1),'o');
axis([-1.2 1.2 -1.2 1.2])
axis('square')
grid on;
title('$\psi_0=0.1\pi$','Interpreter','latex');
xlabel('Real')
ylabel('Imaginary')
theta=2*pi*(0:0.01:1);
hold on
plot(cos(theta),sin(theta),'--');
hold off

%%%%%%%%%%%%  plot 2


subplot(1,2,2);
plot(real(z3),imag(z3),'o');
grid on;
axis([-1.2 1.2 -1.2 1.2])
axis('square')
title('$\psi_0=0.4\pi$','Interpreter','latex');
xlabel('Real')
ylabel('Imaginary')
theta=2*pi*(0:0.01:1);
hold on
plot(cos(theta),sin(theta),'--');
hold off

w1=w1./sum(w1);
w2=w2./sum(w2);
w3=w3./sum(w3);

b=0;
for m=1:N
  b=b+w1(m)*exp(1i*(-(N+1)/2+m)*pi*u);
end
bdb1=20*log10(abs(b));
b1=real(b);
b=0;
for m=1:N
  b=b+w2(m)*exp(1i*(-(N+1)/2+m)*pi*u);
end
bdb2=20*log10(abs(b));
b2=real(b);
b=0;
for m=1:N
  b=b+w3(m)*exp(1i*(-(N+1)/2+m)*pi*u);
end
bdb3=20*log10(abs(b));
b3=real(b);

figure
plot(u,bdb1,u,bdb2,'--',u,bdb3,'-.');
hold on
h=legend({'$\psi_0=0.1\pi$','$\psi_0=0.2\pi$','$\psi_0=0.4\pi$'}, ...
  Interpreter="latex");
axis([-1 1 -80 0])
grid on
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

# 9. Kaiser 加权

&emsp;&emsp;Kaiser 利用 Bessel 函数提出了一个相对简单的长球序列的近似方法，在谱分析、滤波器设计和其他领域中,这种加权得到了广泛的应用。Kaiser 加权函数为

$$\begin{equation}
  w(\tilde{n}) = I_0 \left( \beta \sqrt{1 - \left[ \frac{2\tilde{n}}{N} \right] ^2 } \right), \quad -\frac{N-1}{2} \leqslant \tilde{n} \leqslant \frac{N-1}{2}
\end{equation}$$

其中 $I_0(x)$ 是零阶修正 Bessel 函数，参数 $\beta$ 决定了波束方向图在旁瓣峰值高度和主波束宽度之间的性能折中程度。

&emsp;&emsp;[图 2.1.7(a)](#fig.2-1-7) 给出了 $N=11$ 情况下 $\beta=3$ 和 $\beta=6$ 时的权值（归一化到 $w_6$），[图 2.1.7(b)](#fig.2-1-7) 给出了对应的波束方向图。注意到当 $\beta$ 减小时,权函数接近于均匀加权。当 $N = 11$ 时 Kaiser 加权的波束方向图参数为：

| $\beta$ | HPBW | $BW_{NN}$ | 第一旁瓣高度 | $D_N$ |
| :--: | :--: | :--: | :--: | :--: |
| $3$ | $\displaystyle\frac{2.18}{N}$ | $\displaystyle\frac{1.75\pi}{N}$ | $-23.7 \text{dB}$ | $0.882$ |
| $6$ | $\displaystyle\frac{2.80}{N}$ | $\displaystyle\frac{2.76\pi}{N}$ | $-44.4 \text{dB}$ | $0.683$ |

<a id="fig.2-1-7"></a>

![图 2-1-7 Kaiser 加权: (a) $\beta=3$ 和 $6$ 时的权值；(b) $\beta=3$ 和 $6$ 时的波束方向图](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-070-KaiserWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_07.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.7
% Kaiser weighting
% (a)weighting for beta = 3 and 6
% (b)Beam patterns for beta = 3 and 6
% Xin Zhang updated 3/17/99
% Lillian Xiaolan Xu updated 09/20/2000
% Lillian Xiaolan Xu updated 04/16/2001, K. Bell 7/20/01, 9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all;
close all;

N = 11;
Zl = (N-1)/2;
beta1 = 3;
beta2 = 6;

for n = -(N-1)/2:(N-1)/2
  x = 1-(2*n/N)^2;
  wn1(n+1+(N-1)/2,1) = besseli(0,beta1*sqrt(x));
end

for n = -(N-1)/2:(N-1)/2
  x = 1-(2*n/N)^2;
  wn2(n+1+(N-1)/2,1) = besseli(0,beta2*sqrt(x));
end

nn = -Zl/2:1/2:Zl/2;

u=(-1:0.001:1);
amf=(-(N-1)/2:(N-1)/2)';
Vo=exp(1i*amf*pi*u);
w1=wn1/sum(wn1);
w2=wn2/sum(wn2);
Beam1=20*log10(abs(w1'*Vo));
Beam2=20*log10(abs(w2'*Vo));

bes1 = besseli(0,beta1);
bes2 = besseli(0,beta2);
wn1 = wn1/bes1;
wn2 = wn2/bes2;

figure
subplot(2,1,1)
plot(nn,wn1,'-',nn,wn2,'--')
xlabel('$z/\lambda$','Interpreter','latex')
ylabel('Weights')
%title('Kaiser Window for 11 elements')
hold on;
plot(nn,wn1,'o')
plot(nn,wn2,'o')
axis([-2.5 2.5 -0.02 1.02])
hold off;
legend({'$\beta = 3$','$\beta = 6$'},Interpreter="latex");
text(-0.0625,-0.28,'(a)')
subplot(2,1,2)
plot(u,Beam1,'-',u,Beam2,'--')
h=legend({'$\beta = 3$','$\beta = 6$'},Interpreter="latex");
axis([-1 1 -60 0])
text(-0.025,-75,'(b)')
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')

set(gcf,'Position',[0 0 600 600])
```

{% endnote %}

&emsp;&emsp;[图 2.1.8](#fig.2-1-8) 给出了固定 $\beta=3$，$N=11,21$ 和 $41$ 时的波束方向图，三种情况具有相同的最高旁瓣。当 $N$ 改变时，最高旁瓣对应的 $u$ 值发生改变。

<a id="fig.2-1-8"></a>

![图 2-1-8 Kaiser 加权的波束方向图：$\beta = 3$，$N=11,21$ 和 $41$](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-06-19-josh-oap-part-2-1/2023-06-19-josh-oap-part-2-1-080-BeamPatternsForKaiserWeighting.svg){width=700px}

{% note primary MATLAB Code %}

``` matlab fig3_08.m
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% Figure 3.8
% Beam patterns for Kaiser weighting
% beta = 3, N = 11, 21, and 41
% Xin Zhang
% Last updated 04/16/2001 Lillian Xu, K. Bell 7/20/01,9/30/01
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

clear all;
close all;

N1 = 11;
N2 = 21;
N3 = 41;
beta = 3;

bes = besseli(0,beta);
for n = -(N1-1)/2:(N1-1)/2
  x = 1-(2*n/N1)^2;
  wn1(n+1+(N1-1)/2) = besseli(0,beta*sqrt(x));
end
wn1 = wn1'/bes;

for n = -(N2-1)/2:(N2-1)/2
  x = 1-(2*n/N2)^2;
  wn2(n+1+(N2-1)/2) = besseli(0,beta*sqrt(x));
end
wn2 = wn2'/bes;

for n = -(N3-1)/2:(N3-1)/2
  x = 1-(2*n/N3)^2;
  wn3(n+1+(N3-1)/2) = besseli(0,beta*sqrt(x));
end
wn3 = wn3'/bes;

u=(-1:0.001:1);
amf1=(-(N1-1)/2:(N1-1)/2)';
Vo1=exp(1i*amf1*pi*u);
amf2=(-(N2-1)/2:(N2-1)/2)';
Vo2=exp(1i*amf2*pi*u);
amf3=(-(N3-1)/2:(N3-1)/2)';
Vo3=exp(1i*amf3*pi*u);
wn1=wn1/sum(wn1);
wn2=wn2/sum(wn2);
wn3=wn3/sum(wn3);
Beam1=20*log10(abs(wn1'*Vo1));
Beam2=20*log10(abs(wn2'*Vo2));
Beam3=20*log10(abs(wn3'*Vo3));
plot(u,Beam3,'-',u,Beam2,'--',u,Beam1,'-.')
h=legend({'$N = 41$','$N = 21$','$N = 11$'},Interpreter="latex");
axis([-1 1 -50 0])
xlabel('$u$','Interpreter','latex')
ylabel('Beam pattern (dB)')
```

{% endnote %}

# 参考文献

1. Van Trees, Harry L. _Optimum array processing: Part IV of detection, estimation, and modulation theory._ John Wiley & Sons, 2002.
2. Van Trees, Harry L, 汤俊. _最优阵列处理技术._ 清华大学出版社. 2008.

[^1]: 参数 $D_N$ 是阵列的归一化方向性。它是相对于一个均匀加权阵列的方向性进行归一化的。对于标准线阵, $D_N$ 也是归一化的白噪声增益。

[^2]: 这个权值是奥地利气象学家 Von Hann 提出的，有时也称为 Hanning 加权。

---
title: Josh 的学习笔记之数字信号处理（Part 2——离散傅里叶级数 DFS）
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: false
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - 数字信号和处理
  - DSP
  - 离散傅里叶级数
  - DFS
  - 连续时间傅里叶变换
  - 连续傅里叶级数
  - 离散时间傅里叶变换
  - DTFT
  - 圆周卷积
  - 周期卷积
  - 共轭对称
categories:
  - - Josh 的学习笔记
    - 数字信号处理
abbrlink: 3345523e
date: 2023-04-14 18:43:17
---

# 1. 傅里叶变换的几种形式

## 1.1 非周期连续时间信号的傅里叶变换

&emsp;&emsp;非周期连续时间信号的傅里叶变换（连续时间傅里叶变换，Continuous-Time Fourier Transform, CTFT）的形式为：

$$\begin{equation}
X_a\left(j\Omega\right)=\int_{-\infty}^{\infty}{x_a\left(t\right)e^{-j\Omega t}\mathrm{d}t}
\end{equation}$$

$$\begin{equation}
x_a\left(t\right)=\frac{1}{2\pi}\int_{-\infty}^{\infty}{X_a\left(j\Omega\right)e^{j\Omega t}\mathrm{d}\Omega}
\end{equation}$$

<!-- more -->

## 1.2 周期连续时间信号的傅里叶变换

&emsp;&emsp;周期连续时间信号的傅里叶变换（连续傅里叶级数，Continous Fourier Series, CFS）的形式为：

$$\begin{equation}
X\left(m\Omega\right)=\frac{1}{t_p}\int_{-\frac{t_p}{2}}^{\frac{t_p}{2}}{x_a\left(t\right)e^{-jm\Omega t}\mathrm{d}t}
\end{equation}$$

$$\begin{equation}
x_a\left(t\right)=\sum_{m=-\infty}^{\infty}{X\left(m\Omega\right)e^{jm\Omega t}}
\end{equation}$$

其中 $t_p$ 为信号的周期，频域相邻谱线间角频率增量 $\Omega=\dfrac{2\pi}{t_p}$。

## 1.3 非周期离散时间信号的傅里叶变换

&emsp;&emsp;非周期离散时间信号的傅里叶变换（离散时间傅里叶变换，Discrete-Time Fourier Transform, DTFT）的形式为：

$$\begin{equation}
X\left(e^{j\omega}\right)=\sum_{n=-\infty}^{\infty}{x\left(n\right)e^{-j\omega n}}
\end{equation}$$

$$\begin{equation}
x\left(n\right)=\frac{1}{2\pi}\int_{-\pi}^{\pi}{X\left(e^{j\omega}\right)e^{j\omega n}\mathrm{d}\omega}
\end{equation}$$

取样频率 $f_s=\dfrac{1}{T}$，取样角频率 $\Omega_s=\dfrac{2\pi}{T}$，取样数字频率$\omega_s=2\pi$。

## 1.4 周期离散时间信号的傅里叶变换

&emsp;&emsp;周期离散时间信号的傅里叶变换（离散傅里叶级数，Discrete Fourier Series, DFS）的形式为：

$$\begin{equation}
\widetilde{X}\left(k\right)=\left.\widetilde{X}\left(e^{j\omega}\right)\right|_{\omega=k\frac{2\pi}{N}}=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j\frac{2\pi}{N}kn}}=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)W_N^{kn}}
\end{equation}$$

$$\begin{equation}
\widetilde{x}\left(n\right)=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}kn}}=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)W_N^{-kn}}
\end{equation}$$

其中 $N$ 为一个周期内的点数，时域相邻谱线间隔为 $T$ ，频域相邻谱线间隔为 $\dfrac{1}{NT}$，以数字频率表征时为 $\dfrac{2\pi}{N}$。

> **由以上，在一个域中对函数进行取样，两取样点间增量的倒数，必是另一个域中的周期。**

# 2. 离散傅里叶级数（DFS）的推导

## 2.1 正变换

&emsp;&emsp;令 $x\left(n\right)$ 为 $N$ 点长的离散序列，则其 DTFT（上标“~”用来表征周期性）

$$\begin{equation}
\widetilde{X}\left(e^{j\omega}\right)=\sum_{n=-\infty}^{\infty}{x\left(n\right)e^{-j\omega n}}=\sum_{n=0}^{N-1}{x\left(n\right)e^{-j\omega n}}
\end{equation}$$

对 $\widetilde{X}\left(e^{j\omega}\right)$ 取样，使其称为周期离散频率函数，由此时域序列 $x\left(n\right)$ 周期化为 $\widetilde{x}\left(n\right)$，令其谱线间隔为 $T$，则时域序列的周期为 $NT$，所以对频谱取样的谱间距为 $\dfrac{1}{NT}$，以数字频率表征时，谱间距

$$\begin{equation}
\omega_\mathrm{I}=\frac{2\pi}{N}
\end{equation}$$

因此，以数字频率 $\omega$ 为变量的 $X\left(e^{j\omega}\right)$ 被离散化时，其变量 $\omega$ 变为

$$\begin{equation}
\omega=k\omega_\mathrm{I}=k\frac{2\pi}{N}
\end{equation}$$

因此离散周期序列 $\widetilde{x}\left(n\right)$ 的傅里叶级数可写为

$$\begin{equation}
\widetilde{X}\left(k\right)=\left.\widetilde{X}\left(e^{j\omega}\right)\right|_{\omega=k\frac{2\pi}{N}}=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j\frac{2\pi}{N}kn}}=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)W_N^{kn}}
\end{equation}$$

并将数字域频率用 $k$ 简化表示。且

$$\begin{equation}
\widetilde{X}\left(k+N\right)=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j\frac{2\pi}{N}\left(k+N\right)n}}=\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j2\pi n}e^{-j\frac{2\pi}{N}kn}=}\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j\frac{2\pi}{N}kn}=}\widetilde{X}\left(k\right)
\end{equation}$$

即 $\widetilde{X}\left(k\right)$ 是以 $N$ 为周期的周期函数。

## 2.2 逆变换

&emsp;&emsp;将正变换两边乘以 $e^{j\frac{2\pi}{N}kr}$，并在一个周期内求和，有

$$\begin{equation}
\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}kr}}=\sum_{k=0}^{N-1}{\left(\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)e^{-j\frac{2\pi}{N}kn}}\right)e^{j\frac{2\pi}{N}kr}}=N\left[\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)\left(\frac{1}{N}\sum_{k=0}^{N-1}e^{j\frac{2\pi}{N}k\left(r-n\right)}\right)}\right]
\end{equation}$$

由正交定理

$$\begin{equation}
\frac{1}{N}\sum_{k=0}^{N-1}e^{j\frac{2\pi}{N}k\left(r-n\right)}=
\begin{aligned}
\begin{cases}
1,&r=n\\
0,&r≠n
\end{cases}
\end{aligned}
\end{equation}$$

则

$$\begin{equation}
\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}kr}}=N\left.\left[\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)}\right]\right|_{n=r}=N\widetilde{x}\left(r\right)
\end{equation}$$

可得逆离散傅里叶变换

$$\begin{equation}
\widetilde{x}\left(n\right)=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}kn}}=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)W_N^{-kn}}
\end{equation}$$

且

$$\begin{equation}
\widetilde{x}\left(n+mN\right)=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}k\left(n+mN\right)}}=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)e^{j\frac{2\pi}{N}kn}e^{j2\pi km}}=\widetilde{x}\left(n\right)
\end{equation}$$

即 $\widetilde{x}\left(n\right)$ 是以 $N$ 为周期的周期函数。

# 3. 离散傅里叶级数（DFS）的性质

## 3.1 线性性质

$$\begin{equation}
\mathrm{DFS}\left[a{\widetilde{x}}_1\left(n\right)+{b\widetilde{x}}_2\left(n\right)\right]=a{\widetilde{X}}_1\left(k\right)+b{\widetilde{X}}_2\left(k\right)
\end{equation}$$

## 3.2 时移性质

$$\begin{equation}
\mathrm{DFS}\left[\widetilde{x}\left(n+m\right)\right]=W_N^{-mk}\widetilde{X}\left(k\right)
\end{equation}$$

证明：

$$\begin{equation}
\begin{aligned}
\mathrm{DFS}\left[\widetilde{x}\left(n+m\right)\right]&=\sum_{n=0}^{N-1}{\widetilde{x}\left(n+m\right)W_N^{nk}}=\sum_{n=m}^{N-1+m}{\widetilde{x}\left(n\right)W_N^{\left(n-m\right)k}}=\sum_{n=m}^{N-1+m}{\widetilde{x}\left(n\right)W_N^{nk}W_N^{-mk}}
\\
&\xlongequal{利用\widetilde{x}\left(n\right)和W_N^{nk}的周期性}\left(\sum_{n=0}^{N-1}{\widetilde{x}\left(n\right)W_N^{nk}}\right)W_N^{-mk}=W_N^{-mk}\widetilde{X}\left(k\right)
\end{aligned}
\end{equation}$$

## 3.3 频移性质

$$\begin{equation}
\mathrm{IDFS}\left[\widetilde{X}\left(k+l\right)\right]=W_N^{nl}\widetilde{x}\left(n\right)
\end{equation}$$

证明：

$$\begin{equation}
\begin{aligned}
\mathrm{IDFS}\left[\widetilde{X}\left(k+l\right)\right]&=\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}\left(k+l\right)W_N^{-kn}}=\frac{1}{N}\sum_{k=l}^{N-1+l}{\widetilde{X}\left(k\right)W_N^{-\left(k-l\right)n}}=\frac{1}{N}\sum_{k=l}^{N-1+l}{\widetilde{X}\left(k\right)W_N^{-kn}W_N^{nl}}
\\
&\xlongequal{利用\widetilde{X}\left(n\right)和W_N^{-nk}的周期性}\frac{1}{N}\left(\sum_{k=0}^{N-1}{\widetilde{X}\left(k\right)W_N^{-kn}}\right)W_N^{nl}=W_N^{nl}\widetilde{x}\left(n\right)
\end{aligned}
\end{equation}$$

## 3.4 时域周期卷积

$$\begin{equation}
{\widetilde{x}}_1\left(n\right)\odot{\widetilde{x}}_2\left(n\right)=\mathrm{IDFS}\left[{\widetilde{X}}_1\left(k\right)\cdot{\widetilde{X}}_2\left(k\right)\right]
\end{equation}$$

证明：

$$\begin{equation}
\begin{aligned}
\mathrm{IDFS}\left[{\widetilde{X}}_1\left(k\right)\cdot{\widetilde{X}}_2\left(k\right)\right]&=\frac{1}{N}\sum_{k=0}^{N-1}{\left[{\widetilde{X}}_1\left(k\right)\cdot{\widetilde{X}}_2\left(k\right)\right]W_N^{-nk}}\\
&=\frac{1}{N}\sum_{k=0}^{N-1}{\left[\sum_{m=0}^{N-1}{\widetilde{x}}_1\left(m\right)W_N^{mk}\right]{\widetilde{X}}_2\left(k\right)W_N^{-nk}}\\
&=\frac{1}{N}\sum_{k=0}^{N-1}\left[\sum_{m=0}^{N-1}{\widetilde{x}}_1\left(m\right){\widetilde{X}_2\left(k\right)W}_N^{-\left(n-m\right)k}\right]\\
&=\sum_{m=0}^{N-1}{\widetilde{x}}_1\left(m\right)\left[\frac{1}{N}\sum_{k=0}^{N-1}{\widetilde{X}_2\left(k\right)W}_N^{-\left(n-m\right)k}\right]\\
&=\sum_{m=0}^{N-1}{\widetilde{x}}_1\left(m\right){\widetilde{x}}_2\left(n-m\right)
\\
&={\widetilde{x}}_1\left(n\right)\odot{\widetilde{x}}_2\left(n\right)
\end{aligned}
\end{equation}$$

> 注：此处用 $\odot$ 表示圆周卷积，不同教材中圆周卷积的符号表述可能不同。

## 3.5 频域周期卷积

$$\begin{equation}
\mathrm{DFS}\left[{\widetilde{x}}_1\left(n\right){\widetilde{x}}_2\left(n\right)\right]=\frac{1}{N}\left[{\widetilde{X}}_1\left(k\right)\odot{\widetilde{X}}_2\left(k\right)\right]
\end{equation}$$

证明：

$$\begin{equation}
\begin{aligned}
\mathrm{DFS}\left[{\widetilde{x}}_1\left(n\right){\widetilde{x}}_2\left(n\right)\right]&=\sum_{n=0}^{N-1}{\widetilde{X}_1\left(n\right){\widetilde{x}}_2\left(n\right)W_N^{kn}}=\sum_{n=0}^{N-1}\left[\frac{1}{N}\sum_{r=0}^{N-1}{\widetilde{X}_1\left(r\right)W_N^{-rn}}\right]{\widetilde{x}}_2\left(n\right)W_N^{kn}\\
&=\frac{1}{N}\sum_{n=0}^{N-1}\left[\sum_{r=0}^{N-1}{\widetilde{X}_1\left(r\right){\widetilde{x}}_2\left(n\right)W_N^{\left(k-r\right)n}}\right]\\
&=\frac{1}{N}\sum_{r=0}^{N-1}{\widetilde{X}}_1\left(r\right)\left[\sum_{k=0}^{N-1}{\widetilde{X}_2\left(n\right)W_N^{\left(k-r\right)n}}\right]\\
&=\frac{1}{N}\sum_{r=0}^{N-1}{\widetilde{X}}_1\left(r\right){\widetilde{X}}_2\left(k-r\right)\\
&=\frac{1}{N}\left[{\widetilde{X}}_1\left(k\right)\odot{\widetilde{X}}_2\left(k\right)\right]
\end{aligned}
\end{equation}$$

> 注：此处用 $\odot$ 表示圆周卷积，不同教材中圆周卷积的符号表述可能不同。

# 参考文献

王世一. *数字信号处理, 修订版.* 北京理工大学出版社, 1997.

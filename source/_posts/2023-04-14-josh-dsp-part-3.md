---
title: "Josh's Review — 数字信号处理<br>Part 3 离散傅里叶变换 DFT"
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: false
  wrap: true
  expand_all: false
  max_depth: 4
abbrlink: fd9f9a29
date: 2023-04-14 22:17:40
tags:
  - 数字信号和处理
  - DSP
  - 离散傅里叶变换
  - DFT
  - 延长序列
  - 圆周卷积
  - 重叠相加
  - 重叠保留
  - 内插公式
  - 混叠
  - 频谱泄露
  - 栅栏效应
categories:
  - - Josh 的学习笔记
    - 数字信号处理
---

# 1. 离散傅里叶变换（Discrete Fourier Transform, DFT）的定义

&emsp;&emsp;取离散傅里叶级数的主值，得离散傅里叶变换

$$\begin{equation}
X\left(k\right)=\widetilde{X}\left(k\right)R_N\left(k\right)=\sum_{n=0}^{N-1}{x\left(n\right)W_N^{kn}},\ \ k=0,1,\cdots,N-1
\end{equation}$$

$$\begin{equation}
x\left(n\right)=\widetilde{x}\left(n\right)R_N\left(n\right)=\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)W_N^{-kn}},\ \ n=0,1,\cdots,N-1
\end{equation}$$

且 $\widetilde{X}\left(k\right)=X\left(\left(k\right)\right)_N,\widetilde{x}\left(n\right)=x\left(\left(n\right)\right)_N$。其中 $R_N\left(n\right)$ 表示矩形序列，表示取主值；$\left(\left(n\right)\right)_N$ 表示 $n$ 对 $N$ 取余。

> 注：不同教材中取主值、取余的符号表述可能不同。

<!-- more -->

# 2. DFT 的矩阵算法

&emsp;&emsp;若令 $X=\left[\begin{matrix}X\left(0\right),X\left(1\right),\cdots,X\left(N-1\right)\\\end{matrix}\right]^\mathrm{T}$，$x=\left[\begin{matrix}x\left(0\right),x\left(1\right),\cdots,x\left(N-1\right)\\\end{matrix}\right]^\mathrm{T}$，则 DFT 和 IDFT 可分别表示为

$$\begin{equation}
X=D_Nx,\ \ x=D_N^{-1}X
\end{equation}$$

其中 Vandermonde 矩阵

$$\begin{equation}
D_N=\left[\begin{matrix}1&1&1&\cdots&1\\1&W_N^1&W_N^2&\cdots&W_N^{\left(N-1\right)}\\1&W_N^2&W_N^4&\cdots&W_N^{2\left(N-1\right)}\\\vdots&\vdots&\vdots&&\vdots\\1&W_N^{\left(N-1\right)}&W_N^{2\left(N-1\right)}&\cdots&W_N^{\left(N-1\right)^2}\\\end{matrix}\right]
\end{equation}$$

$$\begin{equation}
D_N^{-1}=\frac{1}{N}\left[\begin{matrix}1&1&1&\cdots&1\\1&W_N^{-1}&W_N^{-2}&\cdots&W_N^{-\left(N-1\right)}\\1&W_N^{-2}&W_N^{-4}&\cdots&W_N^{-2\left(N-1\right)}\\\vdots&\vdots&\vdots&&\vdots\\1&W_N^{-\left(N-1\right)}&W_N^{-2\left(N-1\right)}&\cdots&W_N^{-\left(N-1\right)^2}\\\end{matrix}\right]
\end{equation}$$

且有 $D_N^{-1}=\dfrac{1}{N}D_N^\ast$。

# 3. 离散傅里叶变换（DFT）的性质

## 3.1 线性性质

$$\begin{equation}
\mathrm{DFT}\left[ax_1\left(n\right)+{bx}_2\left(n\right)\right]=aX_1\left(k\right)+bX_2\left(k\right)
\end{equation}$$

## 3.2 用正变换计算逆变换

$$\begin{equation}
x\left(n\right)=\frac{1}{N}\left[\sum_{k=0}^{N-1}{X^\ast\left(k\right)W_N^{nk}}\right]^\ast,\ \ n=0,1,\cdots,N-1
\end{equation}$$

证明：

$$\begin{equation}
\frac{1}{N}\left[\sum_{k=0}^{N-1}{X^\ast\left(k\right)W_N^{nk}}\right]^\ast=\frac{1}{N}\left[\sum_{k=0}^{N-1}{X\left(k\right)W_N^{-nk}}\right]=x\left(n\right)
\end{equation}$$

## 3.3 对称定理

$$\begin{equation}
\mathrm{DFT}\left[\frac{1}{N}X\left(n\right)\right]=x\left(-k\right)=x\left(N-k\right)
\end{equation}$$

证明：

$$\begin{align}
&x\left(-n\right)=x\left(N-n\right)=\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)W_N^{-\left(N-n\right)k}}=\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)W_N^{nk}}
\\
\Longrightarrow &x\left(-k\right)=x\left(N-k\right)=\sum_{n=0}^{N-1}{\left[\frac{X\left(n\right)}{N}\right]W_N^{kn}}=\mathrm{DFT}\left[\frac{X\left(n\right)}{N}\right]
\end{align}$$

## 3.4 反转定理

$$\begin{equation}
\mathrm{DFT}\left[x\left(-n\right)\right]=X\left(-k\right)
\end{equation}$$

证明：

$$\begin{equation}
\mathrm{DFT}\left[x\left(-n\right)\right]=\sum_{n=0}^{N-1}{x\left(-n\right)W_N^{nk}}\xlongequal{令m=-n}\sum_{m=-\left(N-1\right)}^{0}{x\left(m\right)W_N^{-mk}}=\sum_{m=0}^{N-1}{x\left(m\right)W_N^{m\cdot\left(-k\right)}}=X\left(-k\right)
\end{equation}$$

## 3.5 序列的总和

$$\begin{equation}
\sum_{n=0}^{N-1}x\left(n\right)=\left.\sum_{n=0}^{N-1}{x\left(n\right)W_N^{kn}}\right|_{k=0}\left.=X\left(k\right)\right|_{k=0}=X\left(0\right)
\end{equation}$$

## 3.6 序列的起始值

$$\begin{equation}
x\left(0\right)=\frac{1}{N}\left.\sum_{k=0}^{N-1}{X\left(k\right)W_N^{-kn}}\right|_{n=0}=\frac{1}{N}\sum_{k=0}^{N-1}X\left(k\right)
\end{equation}$$

## 3.7 延长序列的 DFT

&emsp;&emsp;将 $N$ 点长序列 $x\left(n\right)\left(n=1,2,\cdots,N-1\right)$ 补零延长 $rN$ 点得到序列 $g\left(n\right)$，即

$$\begin{equation}
g\left(n\right)=
\begin{cases}
x(n),&n=0,1,⋯,N-1\\
0,&n=N,N+1,⋯rN-1
\end{cases}
\end{equation}$$

则 $g\left(n\right)$ 的 DFT

$$\begin{align}
G\left(k\right)&=\mathrm{DFT}\left[g\left(n\right)\right]=\sum_{n=0}^{rN-1}{g\left(n\right)e^{-j\frac{2\pi nk}{rN}}}=\sum_{n=0}^{N-1}{g\left(n\right)e^{-j\frac{2\pi n\left(\frac{k}{r}\right)}{N}}}
\\
&=X\left(\frac{k}{r}\right),\ \ k=0,1,\cdots,rN-1\
\end{align}$$

由此，**$G\left(k\right)$ 与 $X\left(k\right )$具有相同的形状，但 $G\left(k\right)$ 的频谱间隔比 $X\left(k\right)$ 的小。即 {% label primary @通过补零，频谱变得更加细致。但是补零不能提高频谱分辨能力。%}**

## 3.8 有限长序列的圆周特性

### 3.8.1 圆周移位（周期化→移位→取主值）

$$\begin{equation}
x\left(\left(n-m\right)\right)_NR_N\left(n\right)=
\begin{cases}
x\left(n-m\right),&m\leqslant n\leqslant N-1
\\
x\left(N-m+n\right),&0\leqslant n\leqslant m
\end{cases}
\end{equation}$$

### 3.8.2 圆周反转（周期化→反转→取主值）

$$\begin{equation}
x\left(\left(-n\right)\right)_NR_N\left(n\right)=
\begin{cases}
x\left(0\right),&n=0
\\
x\left(N-n\right),&1\leqslant n\leqslant N-1
\end{cases}
\end{equation}$$

### 3.8.3 有限长序列的时间圆周移位定理（可以通过 DFS 证明）

$$\begin{equation}
\mathrm{DFT}\left[x\left(\left(n+m\right)\right)_NR_N\left(n\right)\right]=W_N^{-mk}X\left(k\right)
\end{equation}$$

由此，**有限长序列的圆周移位导致频谱线性相移，而对频谱幅度无影响**。

### 3.8.4 限长序列的频率圆周移位定理（调制特性，可以通过 DFS 证明）

$$\begin{equation}
\mathrm{IDFT}\left[X\left(\left(k+l\right)\right)_NR_N\left(k\right)\right]=W_N^{nl}x\left(n\right)=e^{-j\frac{2\pi}{N}nl}x\left(n\right)
\end{equation}$$

由此，**时域序列的调制等效于频域的圆周移位**，且可以推得

$$\begin{equation}
\mathrm{DFT}\left[x\left(n\right)\cos{\left(\frac{2\pi nl}{N}\right)}\right]=\frac{1}{2}\left[X\left(\left(k-l\right)\right)_N+X\left(\left(k+l\right)\right)_N\right]R_N\left(k\right)
\end{equation}$$

$$\begin{equation}
\mathrm{DFT}\left[x\left(n\right)\sin{\left(\frac{2\pi nl}{N}\right)}\right]=\frac{1}{2}\left[X\left(\left(k-l\right)\right)_N-X\left(\left(k+l\right)\right)_N\right]R_N\left(k\right)
\end{equation}$$

### 3.8.5 DFT的圆周对称性

#### 3.8.5.1 奇序列和偶序列的 DFT

奇序列的 DFT

$$\begin{equation}
x\left(n\right)=-x\left(-n\right)=-x\left(\left(-n\right)\right)_NR_N\left(n\right)\rightarrow X\left(k\right)=-X\left(-k\right)=-X\left(\left(N-k\right)\right)_NR_N\left(k\right)
\end{equation}$$

偶序列的 DFT

$$\begin{equation}
x\left(n\right)=x\left(-n\right)=x\left(\left(-n\right)\right)_NR_N\left(n\right)\rightarrow X\left(k\right)=X\left(-k\right)=X\left(\left(N-k\right)\right)_NR_N\left(k\right)
\end{equation}$$

#### 3.8.5.2 序列的对称分解

&emsp;&emsp;普通序列 $x\left(n\right)$ 的共轭对称分量和共轭反对称分量分别为

$$\begin{equation}
x_e\left(n\right)=\frac{1}{2}\left[x\left(n\right)+x^\ast\left(-n\right)\right],\ \ x_o\left(n\right)=\frac{1}{2}\left[x\left(n\right)-x^\ast\left(-n\right)\right]
\end{equation}$$

对有限长序列的 DFT 进行分析时，定义周期共轭对称分量和周期共轭反对称分量分别为

$$\begin{equation}
x_{ep}\left(n\right)=\frac{1}{2}\left[x\left(\left(n\right)\right)_N+x^\ast\left(\left(-n\right)\right)_N\right]R_N\left(n\right)=\frac{1}{2}\left[x\left(n\right)+x^\ast\left(N-n\right)\right]
\end{equation}$$

$$\begin{equation}
x_{op}\left(n\right)=\frac{1}{2}\left[x\left(\left(n\right)\right)_N-x^\ast\left(\left(-n\right)\right)_N\right]R_N\left(n\right)=\frac{1}{2}\left[x\left(n\right)-x^\ast\left(N-n\right)\right]
\end{equation}$$

二者关系为

$$\begin{equation}
x_{ep}\left(n\right)=\left[x_e\left(n\right)+x_e\left(n-N\right)\right]R_N\left(n\right),\ \ x_{op}\left(n\right)=\left[x_o\left(n\right)+x_o\left(n-N\right)\right]R_N\left(n\right)
\end{equation}$$

<a id=3.8.5.3></a>

#### 3.8.5.3 共轭复序列的 DFT

$$\begin{equation}
\mathrm{DFT}\left[x^\ast\left(n\right)\right]=X^\ast\left(\left(N-k\right)\right)_N
\end{equation}$$

$$\begin{equation}
\mathrm{DFT}\left[x^\ast\left(-n\right)\right]=X^\ast\left(k\right)
\end{equation}$$

证明：

$$\begin{equation}
\mathrm{DFT}\left[x^\ast\left(n\right)\right]=\sum_{n=0}^{N-1}{x^\ast\left(n\right)W_N^{kn}}=\left[\sum_{n=0}^{N-1}{x\left(n\right)W_N^{-kn}}\right]^\ast=X^\ast\left(-k\right)=X^\ast\left(\left(N-k\right)\right)_N
\end{equation}$$

$$\begin{align}
\mathrm{DFT}\left[x^\ast\left(-n\right)\right]&=\sum_{n=0}^{N-1}{x^\ast\left(-n\right)W_N^{kn}}=\left[\sum_{n=0}^{N-1}{x\left(-n\right)W_N^{-kn}}\right]^\ast
\\
&=\left[\sum_{m=-N+1}^{0}{x\left(m\right)W_N^{km}}\right]^\ast=\left[\sum_{n=0}^{N-1}{x\left(n\right)W_N^{kn}}\right]^\ast=X^\ast\left(k\right)
\end{align}$$

<a id=3.8.5.4></a>

#### 3.8.5.4 复数序列的 DFT

$$\begin{equation}
x\left(n\right)=x_r\left(n\right)+jx_i\left(n\right)
\end{equation}$$

$$\begin{equation}
\quad\quad\quad\updownarrow\qquad\quad \updownarrow
\end{equation}$$

$$\begin{equation}
X\left(k\right)=X_{ep}\left(k\right)+X_{op}\left(k\right)
\end{equation}$$

证明：

$$\begin{equation}
X_{ep}\left(k\right)=\mathrm{DFT}\left[x_r\left(n\right)\right]=\frac{1}{2}\mathrm{DFT}\left[x\left(n\right)+x^\ast\left(n\right)\right]=\frac{1}{2}\left[X\left(k\right)+X^\ast\left(N-k\right)\right]
\end{equation}$$

$$\begin{equation}
X_{op}\left(k\right)=\mathrm{DFT}\left[{jx}_i\left(n\right)\right]=\frac{1}{2}\mathrm{DFT}\left[x\left(n\right)-x^\ast\left(n\right)\right]=\frac{1}{2}\left[X\left(k\right)+X^\ast\left(N-k\right)\right]
\end{equation}$$

且由 [3.8.5.3 共轭复序列的 DFT](#3.8.5.3) 可推知

$$\begin{equation}
X_{ep}\left(k\right)=X_{ep}^\ast\left(N-k\right)\ \ \ (实部相等，虚部相反)
\end{equation}$$

$$\begin{equation}
X_{op}\left(k\right)=-X_{op}^\ast\left(N-k\right)(实部相反，虚部相等)
\end{equation}$$

#### 3.8.5.5 DFT 的奇偶虚实特性

| $x(n)$ |       $X(k)$       |
| :----: | :----------------: |
|   偶   |         偶         |
|   奇   |         奇         |
|   实   | 实部为偶，虚部为奇 |
|   虚   | 实部为奇，虚部为偶 |
|  实偶  |        实偶        |
|  实奇  |        虚奇        |
|  虚偶  |        虚偶        |
|  虚奇  |        实奇        |

### 3.8.6 圆周卷积定理

> 注：此处用 $\odot$ 表示圆周卷积，不同教材中圆周卷积的符号表述可能不同。

#### 3.8.6.1 时域圆周卷积定理

$$\begin{align}
x\left(n\right)=x_1\left(n\right)\odot x_2\left(n\right)&=\widetilde{y}\left(n\right)=\sum_{m=0}^{N-1}{x_1\left(m\right)\left[x_2\left(\left(n-m\right)\right)_NR_N\left(n\right)\right]}
\\
&=\mathrm{IDFT}\left[X_1\left(k\right)\cdot X_2\left(k\right)\right]
\end{align}$$

#### 3.8.6.2 频域圆周卷积定理

$$\begin{align}
X\left(k\right)=X_1\left(k\right)\odot X_2\left(k\right)&=\widetilde{X}\left(k\right)R_N\left(k\right)=\frac{1}{N}\sum_{l=0}^{N-1}{X_1\left(l\right)\left[X_2\left(\left(k-l\right)\right)_NR_N\left(k\right)\right]}
\\
&=\mathrm{DFT}\left[x_1\left(n\right)\cdot x_2\left(n\right)\right]
\end{align}$$

#### 3.8.6.3 圆周卷积和线性卷积的关系

&emsp;&emsp;长度为 $L$ 的序列 $x_1\left(n\right)$ 和长度为 $M$ 的序列 $x_2\left(n\right)$ 的周期卷积是二者的线性卷积的以 $N$ 为周期的周期延拓，若满足

$$\begin{equation}
N\geqslant L+M-1
\end{equation}$$

则周期卷积的主值序列即圆周卷积与线性卷积完全相同，即 $x_1\left(n\right)\odot x_2\left(n\right)=x_1\left(n\right)\ast x_2\left(n\right)$

#### 3.8.6.4 圆周卷积的矩阵算法

$$\begin{equation}
y=h\odot x=\left[\begin{matrix}h_1&0&\cdots&0&h_m&\cdots\\h_2&h_1&\cdots&0&0&\cdots\\\vdots&\vdots&&\vdots&\vdots&\cdots\\h_m&h_{m-1}&\cdots&0&0&\cdots\\0&h_m&\cdots&h_1&0&\cdots\\0&0&&\vdots&h_1&\cdots\\\vdots&\vdots&&h_{m-1}&\vdots&\cdots\\0&0&\cdots&h_m&h_{m-1}&\cdots\\\end{matrix}\right]_{N\times N}\cdot\left[\begin{matrix}x_1\\x_2\\\vdots\\x_n\\0\\\vdots\\0\\\end{matrix}\right]_{N\times1}
\end{equation}$$

也可先计算线性卷积以 $N$ 为周期进行周期化后取主值得到圆周卷积。注意周期化时 $N<m+n-1$ 的情况，此时会出现重叠，重叠部分相加后才是圆周卷积的结果。

### 3.8.7 重叠相加法和重叠保留法

**例**&emsp;已知序列

$$\begin{equation}x\left(n\right)=\left\{2,-3,4,5,-6,7,8,-9,-10,11,-12,-13,-14\right\}\left(0\leqslant n\leqslant12\right)
\end{equation}$$

$$\begin{equation}
h\left(n\right)=\left\{1,-2,-3\right\}(0\leqslant n\leqslant2)
\end{equation}$$

&emsp;&emsp;试分别用重叠相加法和重叠保留法计算线性卷积，取分段长度 $L=5$。

解：①重叠相加法

&emsp;&emsp;&emsp;将长序列 $x\left(n\right)$ 分段，每段长度为 $5$

$$\begin{align}
&x_1\left(n\right)=\left\{2,-3,4,5,-6\right\} \\
&x_2\left(n\right)=\left\{7,8,-9,-10,11\right\} \\
&x_3\left(n\right)=\left\{ -12,-13,-14\right\}
\end{align}$$

&emsp;&emsp;&emsp;用圆周卷积计算计算各段与 $h\left(n\right)$ 的线性卷积，下划线标注部分是需要重叠相加的部分

$$\begin{align}
&y_1\left(n\right)=\left\{2,-7,4,6,-28,\underline{-3,18}\right\}\\
&y_2\left(n\right)=\left\{\underline{7,-6},-46,-16,58,\underline{8,-33}\right\}\\
&y_3\left(n\right)=\left\{\underline{-12,11},48,67,42\right\}
\end{align}$$

&emsp;&emsp;&emsp;将各段结果重叠相加，连接成线性卷积结果

$$\begin{equation}
y\left(n\right)=\left\{2,-7,4,6,-28,\mathbf{4},\mathbf{12},-46,-16,58,-\mathbf{4},-\mathbf{22},48,67,42\right\}
\end{equation}$$

&emsp;&emsp;②重叠保留法

&emsp;&emsp;&emsp;将长序列 $x\left(n\right)$ 分段，每段长度为 $5$

$$\begin{align}
&x_1\left(n\right)=\left\{\underline{0,0,2},-3,4\right\}\\
&x_2\left(n\right)=\left\{\underline{-3,4},5,-6,7\right\}\\
&x_3\left(n\right)=\left\{\underline{-6,-7},8,-9,-10\right\}\\
&x_4\left(n\right)=\left\{\underline{-9,-10},11,-12,-13\right\}\\
&x_5\left(n\right)=\left\{\underline{-12,-13},-14,0,0\right\}
\end{align}$$

&emsp;&emsp;&emsp;用圆周卷积计算计算各段与 $h\left(n\right)$ 的线性卷积，下划线标注部分是需要舍去的部分

$$\begin{align}
&y_1\left(n\right)=\left\{\underline{1,-12},2,-7,4\right\}\\
&y_2\left(n\right)=\left\{\underline{1,-11},6,28,4\right\}\\
&y_3\left(n\right)=\left\{\underline{41,49},12,-46,-16\right\}\\
&y_4\left(n\right)=\left\{\underline{53,47},58,-4,-22\right\}\\
&y_5\left(n\right)=\left\{\underline{-12,11},48,67,42\right\}
\end{align}$$

&emsp;&emsp;&emsp;将各段重叠部分舍去，连接成线性卷积结果

$$\begin{equation}
y\left(n\right)=\left\{2,-7,4,6,-28,4,12,-46,-16,58,-4,-22,48,67,42\right\}
\end{equation}$$

## 3.9 Parseval 定理（能量守恒）

$$\begin{equation}
\sum_{n=0\ }^{N-1}{x^2\left(n\right)}=\frac{1}{N}\sum_{k=0}^{N-1}\left|X\left(k\right)\right|^2
\end{equation}$$

## 3.10 DFT 与 $z$ 变换

$$\begin{align}
X\left(k\right)&=\sum_{n=0}^{N-1}{x\left(n\right)W_N^{nk}}=\sum_{n=0}^{N-1}{x\left(n\right)e^{-j\frac{2\pi}{N}nk}}=\left.\sum_{n=0}^{N-1}{x\left(n\right)z^{-n}}\right|_{z=e^{j\frac{2\pi}{N}k}}
\\
&=\left.X\left(z\right)\right|_{z=e^{j\frac{2\pi}{N}k}}=\left.\sum_{n=0}^{N-1}{x\left(n\right)e^{-j\omega n}}\right|_{\omega=\frac{2\pi}{N}k}\left(k=0,1,\cdots,N-1\right)
\end{align}$$

因此 $x\left(n\right)$ 的 DFT 的 $N$ 个系数即为 $x\left(n\right)$ 的 $z$ 变换 $X\left(z\right)$ 在单位圆上 $N$ 等分的取样点。

# 4. 频域取样的点数限制

&emsp;&emsp;设 $M$ 点序列 $x\left(n\right)$ 的 $z$ 变换为 $X\left(z\right)$，DTFT 为 $X\left(e^{j\omega}\right)$，则有

> 在 $X\left(z\right)$ 的单位圆上均匀采样 $N$ 点，其 IDFT 是序列 $x\left(n\right)$ 以周期 $N$ 进行周期延拓后的主值序列；
>
> 在 $X\left(e^{j\omega}\right)$ 的一个周期内均匀采样 $N$ 点，其 IDFT 是序列 $x\left(n\right)$ 以周期 $N$ 进行周期延拓后的主值序列；
>
> 在 $X\left(e^{j\omega}\right)$ 的一个周期内均匀采样 $M$ 点，其 IDFT 是序列 $x\left(n\right)$ 本身。

由此，对于列长为M的有限长序列 $x\left(n\right)$，频域取样不失真的条件是取样点数 $N$ 满足

$$\begin{equation}
N\geqslant M
\end{equation}$$

并且对于无限长序列 $x\left(n\right)$，无论 $N$ 取值如何，都不可能消除混叠，只能随着取样点 $N$ 增加而接近 $x\left(n\right)$。

# 5. 内插公式

&emsp;&emsp;用 DFT 表示 ZT

$$\begin{align}
X\left(z\right)&=\sum_{n=0}^{N-1}{x\left(n\right)z^{-n}}=\sum_{n=0}^{N-1}{\left[\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)W_N^{-nk}}\right]z^{-n}}=\frac{1}{N}\sum_{k=0}^{N-1}\left[X\left(k\right)\sum_{n=0}^{N-1}\left(W_N^{-k}z^{-1}\right)^n\right]
\\
&=\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)\frac{1-W_N^{-kn}z^{-N}}{1-W_N^{-k}z^{-1}}}=\frac{1}{N}\sum_{k=0}^{N-1}{X\left(k\right)\frac{1-z^{-N}}{1-W_N^{-k}z^{-1}}}
\\
&=\frac{1-z^{-N}}{N}\sum_{k=0}^{N-1}\frac{X\left(k\right)}{1-W_N^{-k}z^{-1}}=\sum_{k=0}^{N-1}{X\left(k\right)\varphi_k\left(z\right)}
\end{align}$$

其中 $X\left(z\right)$ 的内插公式

$$\begin{equation}
\varphi_k\left(z\right)=\frac{1}{N}\cdot\frac{1-z^{-N}}{1-W_N^{-k}z^{-1}}
\end{equation}$$

&emsp;&emsp;用 DFT 表示 DTFT

$$\begin{equation}
X\left(e^{j\omega}\right)=\left.X\left(z\right)\right|_{z=e^{j\omega}}=\sum_{k=0}^{N-1}{X\left(k\right)\varphi_k\left(e^{j\omega}\right)}=\sum_{k=0}^{N-1}X\left(k\right)\varphi\left(\omega-k\frac{2\pi}{N}\right)
\end{equation}$$

其中内插函数

$$\begin{equation}
\varphi\left(\omega\right)=\frac{1}{N}\frac{\sin{\left(\frac{\omega N}{2}\right)}}{\sin{\left(\frac{\omega}{2}\right)}}e^{-j\omega\left(\frac{N-1}{2}\right)}
\end{equation}$$

**时域取样定理说明，对频域带限的信号，可对其进行时域取样而不丢失任何信息。频域取样理论说明，时间有限的信号（有限长序列），也可对其进行频域取样（DFT）而不丢失任何信息。**

# 6. 用 DFT 对连续时间信号逼近的问题

## 6.1 混叠现象

&emsp;&emsp;时域采样会引起频谱周期化，通过 $f_s\geqslant 2f_h$ 消除。

## 6.2 频谱泄露现象

&emsp;&emsp;取用有限个数据，即将信号截断的过程，等同于将信号乘以窗函数。如果窗函数是矩形窗，在时域内将信号截断相当于乘以矩形窗，而在频域中则相当于原信号的频谱与矩形窗的频谱卷积，使得原信号的频谱被展宽，造成频谱泄露。**通常通过选择频谱特性更接近于 $\delta\left(k\right)$ 的函数来减少频谱泄露。**

## 6.3 栅栏效应

&emsp;&emsp;用 DFT 计算频谱时，采样点为基频的整数倍，由此观察到的频谱特征时有限的，**若在两个取样点之间有剧烈的频谱变化，将无法检测出来**。通常通过在原序列的末端添加一些零值点来改变周期内的点数，从而在保持原有频谱连续形式不变的情况下，变更了谱线的位置，原来看不到的频谱分量就能移动到可见的位置上。

## 6.4 DFT 参数的选择

### 6.4.1 取样频率 $f_s$（由取样定理）

$$\begin{equation}
f_s\geqslant 2f_h
\end{equation}$$

### 6.4.2 取样周期 $T$

$$\begin{equation}
T=\frac{1}{f_s}\leqslant\frac{1}{2f_h}
\end{equation}$$

### 6.4.3 频率分辨率（频率分量间的增量）$F$

$$\begin{equation}
F=\frac{f_s}{N}
\end{equation}$$

$F$ 越小，频率分辨率越高。

### 6.4.4 最小记录长度（周期性函数的有效周期）$t_p$

$$\begin{equation}
t_p=\frac{1}{F}=NT
\end{equation}$$

### 6.4.5 一个记录长度内的点数 $N$

$$\begin{equation}
N\geqslant\frac{2f_h}{F}
\end{equation}$$

> 注：最高频率 $f_h$ 和频率分辨率 $F$ 存在矛盾，即
>
> $$\begin{equation}
> f_h\uparrow \xrightarrow{f_s\geqslant 2f_h}f_s\uparrow \xrightarrow{T=\frac{1}{f_s}}T\downarrow \xrightarrow{N\text{给定时}}t_p\downarrow \longrightarrow F\uparrow \left( \text{频率分辨率}\downarrow \right)
> \end{equation}$$

# 参考文献

王世一. *数字信号处理, 修订版.* 北京理工大学出版社, 1997.

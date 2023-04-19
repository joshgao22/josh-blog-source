---
title: 'Josh''s Notes: 最优阵列处理（Part 1.2 —— 均匀线阵模型）'
mathjax: true
comments: true
copyright: true
toc:
  enable: false
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - 阵列信号处理
  - 均匀线阵
  - ULA
categories:
  - - Josh 的学习笔记
    - 最优阵列处理
    - 阵列和空域滤波器
abbrlink: de20fd09
date: 2023-04-20 00:14:31
---

我们感兴趣的线阵如[图 1-2-1](#fig.1-2-1) 所示，其中有 $N$ 个位于 $z$ 轴上的阵元，阵元间距均匀，记为 $d$。我们把这种阵列称为**{% label primary @均匀线性阵列（uniform linear array, ULA） %}**。我们已经把阵列的中心放在了坐标系的原点上，这种阵列中心放置方法将在计算量上具有优点。

<a id="fig.1-2-1"></a>

![图 1-2-1 放置在 $z$ 轴上的线阵](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2023-04-20-josh-opa-part-1-2/2023-04-20-josh-oap-part-1-2-010-LinearArrayAlongZAaxis.png!sign){width=600px}

<!-- more -->

&emsp;&emsp;阵元的位置为

$$\begin{equation} \label{ElementLocation}
\begin{cases}
  p_{x_n} = 0 \\
  p_{y_n} = 0 \\
  p_{z_n} = \left( n - \displaystyle\frac{N-1}{2} \right) d
\end{cases}
  , \, n = 0,1,\cdots,N-1
\end{equation}$$

为了确定阵列流形矢量 $\vec{v}_{\vec{k}}\left(\vec{k}\right)$, 把式 $\eqref{ElementLocation}$ 代入到[阵列流形矢量的定义][], 得到

$$\begin{equation}
  \vec{v}_{\vec{k}}\left(k_z\right) = \left[\begin{array}{c}
    e^{j \left( \frac{N-1}{2} \right) k_z d} \\
    e^{j \left( \frac{N-1}{2} - 1\right) k_z d} \\
    \vdots \\
    e^{-j \left( \frac{N-1}{2} \right) k_z d} \\
  \end{array}\right]
\end{equation}$$

这里

$$\begin{equation}
  k_z = -\frac{2\pi}{\lambda}\cos\theta = -k_0 \cos\theta
\end{equation}$$

其中波数的幅度

$$\begin{equation}
  k_0 \triangleq \left| \vec{k} \right| = \frac{2\pi}{\lambda}
\end{equation}$$

[阵列流形矢量的定义]: https://josh-gao.top/posts/8b61f5a7.html#DefinitionOfArrayManifoldVector

---
title: "Josh's Note — 凸优化<br>Part 2.1 凸集—仿射集合和凸集"
mathjax: true
comments: true
copyright: true
sticky: false
toc:
  enable: true
  number: false
  wrap: true
  expand_all: true
  max_depth: 4
categories:
  - - Josh 的学习笔记
    - 凸优化
tags:
  - 凸优化
  - 仿射
  - 保凸运算
abbrlink: 43146ee9
date: 2022-03-29 11:07:04
---

$$
\newcommand{\bfR}{\mathbf{R}}
\def\aff{\mathop{\bf aff}}
\def\relint{\mathop{\bf relint}}
\def\cl{\mathop{\bf cl}}
$$

# 1. 直线与线段

&emsp;&emsp;设 $x_1 \ne x_2$ 是 $\bfR^n$ 空间中的两个点，则具有下列形式的点

$$
y = \theta x_1 + (1-\theta)x_2,\theta \in \bfR
$$

组成一条穿过 $x_1$ 和 $x_2$ 的**直线**（*line*）。参数 $\theta = 0$ 和 $\theta = 1$ 分别对应 $y=x_2$ 和 $y = x_1$。参数 $\theta$ 的值在 0 和 1 之间变动，构成了 $x_1$ 和 $x_2$ 之间的（闭）线段（(closed) *line segment*）。

&emsp;&emsp;另一种 $y$ 的表示形式

$$
y = x_2 + \theta (x_1 - x_2)
$$

可以解释为：$y$ 是**基点**（*base point*）$x_2$（对应 $\theta = 0$）和**方向**（*direction*）$x_1 - x_2$（由 $x_2$ 指向 $x_1$）乘以参数 $\theta$ 的和。因此，$\theta$ 给出了 $y$ 在由 $x_2$ 通向 $x_1$ 的路上的位置。当 $\theta$ 由 $0$ 增加到 $1$, 点 $y$ 相应地由 $x_2$ 移动到 $x_1$。如果 $\theta > 1$，点 $y$ 在超越了 $x_1$ 的直线上。[图 1](#图1) 给出了直观的解释。

<a id="图1"></a>

![图 1 通过 $x_1$ 和 $x_2$ 的直线可以参数化描述为 $\theta x_1 + (1-\theta)x_2$, 其中 $\theta$ 在 $\bfR$ 上变化。$x_1$ 和 $x_2$ 之间的线段由深色所示，对应处于 $0$ 和 $1$ 之间的 $\theta$。](../images/post/2022-03-29-josh-convex-optimization-1/2022-03-29-josh-convex-optimization-1-010-TheLinePassingThroughX1AndX2.png){width=800px}

<!-- more -->

# 2. 仿射集合和凸集

&emsp;&emsp;如果通过集合 $C \subseteq \bfR^n$ 中任意两个不同点的直线仍然在集合 $C$ 中，那么称集合 $C$ 是**仿射**（*affine*）的。也就是说，$C \subseteq \bfR^n$ 是仿射的等价于：对于任意 $x_1, x_2 \in C$ 及 $\theta \in \bfR$ 有 $\theta x_1 + (1-\theta)x_2 \in C$。换言之，$C$ 包含了 $C$ 中任意两点的系数之和为 $1$ 的线性组合。

&emsp;&emsp;这个概念可以扩展到多个点的情况。若 $\theta_1 + \cdots + \theta_k = 1$，则称具有 $\theta_1 x_1 + \cdots + \theta_k x_k$ 形式的点为 $x_1, \cdots, x_k$ 的**仿射组合**（*affine combination*）。利用仿射集合的定义（即仿射集合包含其中任意两点的仿射组合），可以归纳出以下结论：一个仿射集合包含其中任意点的仿射组合，即如果 $C$ 是一个仿射集合，$x_1, \cdots, x_k \in C$，并且 $\theta_1 + \cdots + \theta_k = 1$，那么 $\theta_1 x_1 + \cdots + \theta_k x_k$ 仍然在 $C$ 中。

&emsp;&emsp;如果 $C$ 是一个仿射集合并且 $x_0 \in C$，则集合

$$
V = C - x_0 = \left\{ x - x_0 \middle| x \in C \right\}
$$

是一个子空间，即关于加法和数乘是封闭的。

> 简单说明：设 $v_1, v_2 \in V$，$\alpha, \beta \in \bfR$，则有 $v_1 + x_0 \in C$，$v_2 + x_0 \in C$。因为 $C$ 是仿射的，且 $\alpha + \beta + (1 - \alpha - \beta) = 1$，所以
>
> $$
> \alpha v_1 + \beta v_2 + x_0 = \alpha (v_1 + x_0) + \beta(v_2 + x_0) + (1 - \alpha - \beta)x_0 \in C
> $$
>
> 由 $\alpha v_1 + \beta v_2 + x_0 \in C$，可知 $\alpha v_1 + \beta v_2 \in V$。

&emsp;&emsp;因此，仿射集合 $C$ 可以表示为

$$
C = V + x_0 = \left\{ v + x_0 \middle| v \in V \right\}
$$

即一个子空间加上一个偏移。与仿射集合 $C$ 相关联的子空间 $V$ 与 $x_0$ 的选取无关，所以 $x_0$ 可以是 $C$ 中的任意一点。定义仿射集合 $C$ 的维数为子空间 $V = C - x_0$ 的维数，其中 $x_0$ 是 $C$ 中的任意元素。

&emsp;&emsp;称由集合 $C \subseteq \bfR^n$ 中的点的所有仿射组合组成的集合为 $C$ 的**仿射包**（*affine hull*），记为 $\aff{C}$:

$$
\aff{C} = \left\{ \theta_1 x_1 + \cdots + \theta_k x_k \middle| x_1, \cdots, x_k \in C, \theta_1 + \cdots + \theta_k = 1 \right\}
$$

仿射包是包含 $C$ 的最小的仿射集合，也就是说：如果 $S$ 是满足 $C \subseteq S$ 的仿射集合，那么 $\aff{C} \subseteq S$。

# 3. 仿射维数与相对内部

&emsp;&emsp;定义集合 $C$ 的仿射维数为其仿射包的维数。仿射维数在凸分析及凸优化中十分有用，但它与其他维数的定义常常不相容。

> 举例：考虑 $\bfR^2$ 上的单位圆环 $\left\{x\in\bfR^2\middle| x_1^2 + x_2^2 = 1 \right\}$。它的仿射包是全空间 $\bfR^2$，所以其仿射维数为 $2$。但是，在其他
大多数维数的定义下，$\bfR^2$ 上的单位圆环的维数为 $1$。

&emsp;&emsp;如果集合 $C \subseteq \bfR^n$ 的仿射维数小于 $n$, 那么这个集合在仿射集合 $\aff C \ne \bfR^n$ 中。定义集合 $C$ 的**相对内部**（*relative interior*）为 $\aff C$ 的内部，记为 $\relint C$，即

$$
\relint C = \left\{ x \in C \middle| B(x,r) \cap \aff C \subseteq C\ 对于某些\ r > 0 \right\}
$$

其中 $B(x,r) = \left\{ y \middle| \left\| y-x \right\| \leqslant r \right\}$，即半径为 $r$，中心为 $x$ 并由范数 $\| \cdot \|$ 定义的球（这里的 $\| \cdot \|$ 可以是任意范数，并且所有范数定义了相同的相对内部）。于是可以定义
集合 C 的**相对边界**（*relative boundary*）为 $\cl C\ \backslash \relint C$, 此处 $\cl C$ 表示 $C$ 的闭包。

# 参考文献

1. Stephen P. Boyd and Lieven Vandenberghe, *Convex optimization*. Cambridge, UK: Cambridge University Press, 2004.
2. Stephen P. Boyd and Lieven Vandenberghe, *凸优化*. 北京: 清华大学出版社, 2013.

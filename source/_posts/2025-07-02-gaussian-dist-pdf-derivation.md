---
title: 信息论角度推导高斯分布的概率密度函数
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
abbrlink: '741621e3'
date: 2025-07-02 09:20:07
tags:
  - 高斯分布
  - 信息论
categories:
  - [数学, 概率论]
---

$\newcommand\diff{\mathop{}\!\mathrm{d}}$

# 背景与目标

在统计建模中，我们常常面临这样一个问题：**已知某个连续随机变量的期望和方差，如何合理地确定它的概率分布？** 最大熵原理（Maximum Entropy Principle）提供了一个具有信息论意义的答案：

> 在已知约束（如期望与方差）下，熵最大的概率密度函数是最“中立”或最“无偏”的选择。

熵在此表示不确定性或信息量。连续型随机变量的微分熵定义为：

$$
\begin{equation*}
H[p] = -\int_{-\infty}^{\infty} p(x) \ln p(x) \, \diff x
\end{equation*}
$$

我们的目标是：**在已知期望和方差的约束下，最大化熵 $H[p]$，求出最优概率密度函数 $p(x)$**。

<!-- more -->

# 约束条件

我们假设随机变量 $X$ 满足：

- 归一化约束：

  $$
  \begin{equation*}
  \int_{-\infty}^{\infty} p(x) \, \diff x = 1
  \end{equation*}
  $$

- 期望约束：

  $$
  \begin{equation*}
  \int_{-\infty}^{\infty} x \, p(x) \, \diff x = \mu
  \end{equation*}
  $$

- 方差约束：

  $$
  \begin{equation*}
  \int_{-\infty}^{\infty} (x - \mu)^2 \, p(x) \, \diff x = \sigma^2
  \end{equation*}
  $$

# 构造拉格朗日泛函

引入拉格朗日乘子 $\lambda_0, \lambda_1, \lambda_2$，构造带约束的目标泛函：

$$
\begin{equation*}
\mathcal{L}[p] = -\int p(x) \ln p(x) \diff x
+ \lambda_0 \left( \int p(x) \diff x - 1 \right)
+ \lambda_1 \left( \int x p(x) \diff x - \mu \right)
+ \lambda_2 \left( \int (x - \mu)^2 p(x) \diff x - \sigma^2 \right)
\end{equation*}
$$

将所有积分项合并为：

$$
\begin{equation*}
\mathcal{L}[p] = \int \left[
- p(x) \ln p(x)
+ \lambda_0 p(x)
+ \lambda_1 x p(x)
+ \lambda_2 (x - \mu)^2 p(x)
\right] \diff x
\end{equation*}
$$

# 变分法求极值

我们考虑一个扰动：

$$
\begin{equation*}
p(x) \rightarrow p(x) + \epsilon \eta(x)
\end{equation*}
$$

其中 $\epsilon$ 是足够小的实数，$\eta(x)$ 是任意光滑、在积分区间内有界且满足边界条件的函数（可理解为“任意小的扰动”）。为了求出 $\dfrac{\delta \mathcal{L}}{\delta p(x)}$，我们代入扰动项进行展开：

$$
\begin{equation*}
\mathcal{L}[p + \epsilon \eta]
= -\int (p(x) + \epsilon \eta(x)) \ln (p(x) + \epsilon \eta(x)) \, \diff x + \text{其余约束项}
\end{equation*}
$$

> 注意：这里只展开了主熵项 $-\displaystyle\int p \ln p$，约束项形式类似，将在后面统一处理。

---

对主项使用泰勒展开（忽略高阶小量）：

> **泰勒展开回顾：**
>
> 设 $f(p) = p \ln p$，我们对 $p(x) + \epsilon \eta(x)$ 展开，有：
> $$
> f(p + \epsilon \eta) = p \ln p + \epsilon \eta (1 + \ln p) + o(\epsilon)
> $$
> 其中：
> $$
> \frac{\diff }{\diff p}(p \ln p) = \ln p + 1
> $$

将其代入：

$$
\begin{align*}
\mathcal{L}[p + \epsilon \eta]
&= -\int \left[ p(x) \ln p(x) + \epsilon \eta(x)(1 + \ln p(x)) \right] \diff x + o(\epsilon) + \text{约束项} \\
&= \mathcal{L}[p] - \epsilon \int \eta(x) (1 + \ln p(x)) \diff x + o(\epsilon) + \text{约束项}
\end{align*}
$$

对约束项也类似展开：

- 对归一化项：

  $$
  \begin{equation*}
  \lambda_0 \left( \int (p + \epsilon \eta) \diff x - 1 \right)
  = \lambda_0 \left( \int p \, \diff x - 1 + \epsilon \int \eta \, \diff x \right)
  \end{equation*}
  $$

- 对期望项：

  $$
  \begin{equation*}
  \lambda_1 \left( \int x (p + \epsilon \eta) \diff x - \mu \right)
  = \lambda_1 \left( \int x p \, \diff x - \mu + \epsilon \int x \eta \, \diff x \right)
  \end{equation*}
  $$

- 对方差项：

  $$
  \begin{equation*}
  \lambda_2 \left( \int (x - \mu)^2 (p + \epsilon \eta) \diff x - \sigma^2 \right)
  = \lambda_2 \left( \int (x - \mu)^2 p \, \diff x - \sigma^2 + \epsilon \int (x - \mu)^2 \eta(x) \diff x \right)
  \end{equation*}
  $$

---

将所有一阶 $\epsilon$ 项收集起来：

$$
\begin{equation*}
\mathcal{L}[p + \epsilon \eta] = \mathcal{L}[p]
+ \epsilon \int \eta(x) \left[ - (1 + \ln p(x)) + \lambda_0 + \lambda_1 x + \lambda_2 (x - \mu)^2 \right] \diff x + o(\epsilon)
\end{equation*}
$$

对 $\epsilon$ 求导并令其在 $\epsilon = 0$ 处为零：

$$
\begin{equation*}
\left. \frac{\diff}{\diff \epsilon} \mathcal{L}[p + \epsilon \eta] \right|_{\epsilon=0}
= \int \eta(x) \left[ -\ln p(x) - 1 + \lambda_0 + \lambda_1 x + \lambda_2 (x - \mu)^2 \right] \diff x = 0
\end{equation*}
$$

根据变分法基本引理，这个积分当且仅当括号内为 $0$ 时对所有扰动函数 $\eta(x)$ 为零：

> 变分法基本引理：
>
> 若函数 $F(x)$ 满足
> $$
> \int_{a}^{b} F(x) \cdot \eta(x) dx = 0
> $$
> 对所有合适扰动 $\eta(x)$ 成立，则必有
> $$
> F(x) = 0 \quad \forall x \in (a, b)
> $$
>
> 这是因为若 $F(x_0) \ne 0$，则可构造在 $x_0$ 附近支持的 $\eta(x)$ 使得积分不为零，导致矛盾。

$$
\begin{equation*}
\frac{\delta \mathcal{L}}{\delta p(x)} = -\ln p(x) - 1 + \lambda_0 + \lambda_1 x + \lambda_2 (x - \mu)^2 = 0
\end{equation*}
$$

也即求得了变分导数。移项得：

$$
\begin{equation*}
\ln p(x) = \lambda_0' + \lambda_1 x + \lambda_2 (x - \mu)^2
\end{equation*}
$$

两边取指数：

$$
\begin{equation*}
p(x) = \exp\left( \lambda_0' + \lambda_1 x + \lambda_2 (x - \mu)^2 \right)
\end{equation*}
$$

要满足可积性，需 $\lambda_2 < 0$。设

$$
\begin{equation*}
p(x) = A \cdot \exp\left( -\alpha (x - \mu)^2 \right)
\end{equation*}
$$

# 归一化与确定参数

根据归一化条件：

$$
\begin{equation*}
A \cdot \int_{-\infty}^{\infty} \exp\left( -\alpha (x - \mu)^2 \right) \diff x = 1
\end{equation*}
$$

已知高斯积分结果：

$$
\begin{equation*}
\int_{-\infty}^{\infty} \exp\left( -\alpha (x - \mu)^2 \right) \diff x = \sqrt{\frac{\pi}{\alpha}}
\end{equation*}
$$

得到：

$$
\begin{equation*}
A = \sqrt{\frac{\alpha}{\pi}}
\end{equation*}
$$

若令 $\alpha = \dfrac{1}{2\sigma^2}$，则最终得：

$$
\begin{equation*}
p(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left( -\frac{(x - \mu)^2}{2\sigma^2} \right)
\end{equation*}
$$

# 总结

从最大熵原理出发，在已知均值与方差的约束下，最“无偏”或熵最大的概率密度函数是：

$$
\begin{equation*}
p(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left( -\frac{(x - \mu)^2}{2\sigma^2} \right)
\end{equation*}
$$

这正是**高斯分布**的标准形式，说明它不仅数学上方便，**信息论上也是最合理的选择**。

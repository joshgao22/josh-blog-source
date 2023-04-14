---
title: 高等数学常用公式
mathjax: true
comments: true
copyright: true
toc:
  enable: true
  number: false
  wrap: true
  expand_all: true
  max_depth: 4
abbrlink: 1823c9d1
date: 2023-04-14 16:25:59
tags:
  - 等价无穷小
  - 导数公式
  - Maclaurin 公式
  - 曲率
  - 积分公式
  - 三角函数
categories:
  - [数学, 微积分]
---

# 1. 常见等价无穷小

||||
| :--: | :--: | :--: |
| $\sin x \sim x$ | $\tan x \sim x$ | $\arcsin x \sim x$ |
| $\arctan x \sim x$ | $\displaystyle\left( 1 - \cos x \right) \sim \frac{x^2}{2}$ | $\ln (1+x) \sim x$ |
| $\left( e^x -1 \right) \sim x$ | $\left( a^x - 1 \right) \sim x \ln a$ | $\left( \left( 1+x \right) ^\alpha -1 \right) \sim \alpha x$  |

<!-- more -->

# 2. 导数基本公式

| 一般函数 | 三角函数 | 反三角函数 | 双曲函数<br>(圆函数) |
| :--: | :--: | :--: | :----: |
| $\left( C \right)' = 0$ | $\left( \sin x \right)' = \cos x$ | $\displaystyle\left( \arcsin x \right)' = \frac{1}{\sqrt{1-x^2}}$ | $\displaystyle\left( \sinh x \right)' = \left(\frac{e^x-e^{-x}}{2}\right)' = \cosh x$ |
| $\left( x^\alpha \right)'=\alpha x^{\alpha-1} \left( \alpha \in  \mathbf{Z} \right)$ | $\left( \cos x \right)' = -\sin x$ | $\displaystyle\left( \arccos x \right)' = -\frac{1}{\sqrt{1-x^2}}$ | $\displaystyle\left( \cosh x \right)' = \left( \frac{e^x+e^{-x}}{2} \right)'=\sinh x$ |
| $\displaystyle\left( \log_ax\right)'=\frac{1}{x\ln a}$ | $\displaystyle\left( \tan x \right)' = \frac{1}{\cos^2 x}=\sec^2 x$ | $\displaystyle\left( \arctan x \right)' = \frac{1}{1+x^2}$ | $\displaystyle\left( \mathrm{arsinh} \, x \right)' = \left(\ln \left( x+\sqrt{x^2 + 1} \right) \right)' = \frac{1}{x^2+1}$ |
| $( \ln$\| $x$ \|$)'=\displaystyle\frac{1}{x}$ | $\displaystyle\left( \cot x \right)' = -\frac{1}{\sin^2 x} = -\csc^2 x$ | $\displaystyle\left( \mathrm{arccot} \, x \right)' = -\frac{1}{1+x^2}$ | $\displaystyle\left( \mathrm{arcosh} \, x \right)' = \left( \ln \left( x + \sqrt{x^2 - 1} \right) \right)' = \frac{1}{\sqrt{x^2-1}}$
|  | $\left( \sec x \right)' = \sec x \cdot \tan x$ |
|  | $\left( \csc x \right)' = \csc x \cdot \cot x$ |

- Newton-Leibniz 公式：

  $$
  \begin{aligned}
  \left( u \cdot v \right)^{\left( n \right)} &= u^{\left( n \right)}v + nu^{\left( n-1 \right)}v' + \frac{n \left( n-1 \right)}{2!}u^{\left( n-2 \right)}v'' + \cdots + \frac{n\left( n-1 \right)\cdots\left(n-k+1\right)}{k!}u^{\left( n-k \right)}v^{(k)} + \cdots + uv^{\left( n \right)} \\
  &= \sum_{k=0}^{n}C_n^k u^{\left( u-k \right)}v^k
  \end{aligned}
  $$

  可以注意到 Newton-Leibniz 公式具有类似二项式定理展开的形式。

# 3. 几个初等函数的 Maclaurin 公式

## 3.1 Taylor 公式的一般形式

$$
f(x)=f(x_0) + f'(x_0)(x-x_0) + \frac{f''(x_0)}{2!}(x-x_0)^2 + \cdots + \frac{f^{(n)}(x_0)}{n!}(x-x_0)^n + R_n
$$

其中

$$
R_n = \frac{f^{(n+1)}(\xi)}{(n+1)!}(x-x_0)^{n+1} \, (\xi\,在\,x\,和\,x_0\,之间) = o\left( \left( x - x_0 \right)^n \right)(x\to x_0)
$$

## 3.2 Maclaurin 公式的一般形式

$$
f(x)=f(0)+f'(0)\,x + \frac{f''(0)}{2!}x^2 + \cdots + \frac{f^{(n)}(0)}{n!}x^n + R_n
$$

其中

$$
R_n = \frac{f^{(n+1)}(\xi)}{(n+1)!}x^{n+1} \, (\xi\,在\,0\,和\,x\,之间) = \frac{f^{(n+1)}(\theta x)}{(n+1)!} x^{n+1}(0<\theta<1) = o\left( \left( x - x_0 \right)^n \right)(x\to x_0)
$$

## 3.3 指数函数的 Maclaurin 展开式

$$
e^x=1+x+\frac{x^2}{2} + \frac{x^3}{3} + \cdots +\frac{x^n}{n!} + \frac{e^{\theta x}}{(n+1)!}(0<\theta<1)
$$

## 3.4 正弦函数的 Maclaurin 展开式

$$
\sin x = x - \frac{x^3}{3!} + \frac{x^5}{5!} + \cdots + (-1)^{m-1}\frac{x^{2m-1}}{(2m-1)!} + R_{2m}
$$

其中

$$
R_{2m}=\frac{\sin\left( \theta x + \frac{(2m-1)\pi}{2}\right)}{(2m+1)!}x^{2m+1} = o(x^{2m})(0<\theta <1)
$$

## 3.5 余弦函数的 Maclaurin 展开式

$$
\cos x = 1 - \frac{x^2}{2!} + \frac{x^4}{4!} + \cdots + (-1)^m\frac{x^{2m}}{2m!} + R_{2m+1}
$$

其中

$$
R_{2m+1}=\frac{\cos(\theta x + (m+1)\pi)}{(2m+2)!} x^{2m+2}=o(x^{2m+2}) (0<\theta <1)
$$

## 3.6 $\ln (1+x)$ 的 Maclaurin 展开式

$$
\ln(1+x) = x - \frac{x^2}{2} + \frac{x^3}{3} -\frac{x^4}{4} + \cdots + \frac{(-1)^{n-1}x^n}{n} + R_n(x)
$$

其中

$$
R_n(x) = \frac{(-1)^n}{(n+1)(1+\theta x)^{n+1}}x^{n+1} = o(x^n)(0<\theta <1)
$$

## 3.7 $(1+x)^\alpha$ 的 Maclaurin 展开式

$$
(1+x)^\alpha = 1 + \alpha x + \frac{\alpha(\alpha-1)}{2!}x^2 + \cdots + \frac{\alpha(\alpha - 1)\cdots (\alpha-n+1)}{n!}x^n + R_n(x)
$$

其中

$$
R_n(x) = \frac{\alpha(\alpha -1)\cdots(\alpha-n)}{(n+1)!}(1+\theta x)^{\alpha-n-1}x^{n+1} = o(x)(0<\theta <1)
$$

## 3.8 $\frac{1}{1-x}$ 的 Maclaurin 展开式

$$
\frac{1}{1-x} = 1+x+x^2+\cdots+x^n +o(x^n)
$$

# 4. 曲率及曲率半径

| 曲率 | 曲率半径 |
| :--: | :--: |
| $K=\left\| \dfrac{\mathrm{d}\alpha}{\mathrm{d}s}\right\| \xlongequal{\tan \alpha = y' \Rightarrow \alpha = \arctan y'} \dfrac{\left\| \frac{y''}{1+y'^2}\mathrm{d}x \right\|}{\sqrt{1+y'^2}}\mathrm{d}x = \dfrac{\left\| y'' \right\|}{\left( 1 + y' \right)^\frac{3}{2}}$ | $R = \frac{1}{K}$ |

# 5. 积分常用公式

| 序 | 描述 | 公式 |
| :--: | :----: | :----------------------------------------------:|
| 1 | 定积分的定义 | $\displaystyle\int^1_0f(x)\mathrm{d}x=I=\lim_{\lambda \to 0}\sum_{i=1}^{n}f(\xi_i)\Delta x_i$，其中 $\lambda = \max \left\{ \Delta x_1,\Delta x_2, \cdots, \Delta x_n \right\}$ |
| 2 | 变上限积分 | $考察定积分\displaystyle\int^x_af(x)\mathrm{d}x=\int^x_af(t)\mathrm{d}t$，对 $\displaystyle\Phi(x)=\int_a^xf(t)\mathrm{d}t$，有  $\displaystyle\Phi'(x)=\frac{\mathrm{d}}{\mathrm{d}x}\int_a^xf(t)\mathrm{d}t = f(x)$ |
| 3. | — | $\displaystyle\frac{\mathrm{d}}{\mathrm{d}x}\int^{\beta(x)}_{\alpha(x)}f'(t)\mathrm{d}t = f\left[ \beta\left(x\right)\right]\beta'(x)-f[\alpha(x)]\alpha'(x)$ |
| 4 | 上下限对称的积分 | 若 $f(x)$ 为偶函数，则 $\displaystyle\int_{-a}^af(x)\mathrm{d}x=2\int_0^af(x)\mathrm{d}x$；若 $f(x)$ 为奇函数，则 $\displaystyle\int_{-a}^af(x)\mathrm{d}x=0$ |
| 5 | 周期函数的积分 | $\displaystyle f(x+T)=f(x)\Rightarrow\int_0^{nT}f(x)\mathrm{d}x=n\int^T_0f(x)\mathrm{d}x$ |
| 6 | 分部积分法 | $\displaystyle\int uv'\mathrm{d}x=uv - \int u'v\mathrm{d}x \Leftrightarrow \int u\mathrm{d}v = uv - \int v\mathrm{d}u$ |

# 6. 基本积分表

| 序 | 积分式 |
| :--: | :-------------------------: |
| 1 | $\displaystyle\int k\mathrm{d}x=kx+C$，特别地 $\displaystyle\int\mathrm{d}x=x+C$ |
| 2 | $\displaystyle\int x^\mu\mathrm{d}x=\frac{x^{\mu+1}}{\mu+1}+C(\mu\ne-1)$，特别地 $\displaystyle\int \frac{\mathrm{d}x}{x^2}=-\frac{1}{x}+C,\int\frac{\mathrm{d}x}{\sqrt{x}}=2\sqrt{x}+C$ |
| 3 | $\displaystyle\int\frac{\mathrm{d}x}{x}=\ln \|x\|+C$ |
| 4 | $\displaystyle\int\frac{\mathrm{d}x}{1+x^2} = \arctan x +C$ |
| 5 | $\displaystyle\int\frac{\mathrm{d}x}{\sqrt{1-x^2}}=\arcsin x+C$ |
| 6 | $\displaystyle\int\sin x\mathrm{d}x = -\cos x+C, \int\cos x\mathrm{d}x = \sin x +C$ |
| 7 | $\displaystyle\int\frac{\mathrm{d}x}{\cos^2x} = \int\sec^2x\mathrm{d}x = \tan x +C, \int\frac{\mathrm{d}x}{\sin^2x}=\int\csc^2x\mathrm{d}x = -\cot x +C$ |
| 8 |  $\displaystyle\int a^x\mathrm{d}x = \frac{a^x}{\ln a} + C$，特别地 $\displaystyle\int e^x\mathrm{d}x = e^x +C$
| 9 | $\displaystyle\int \sinh x\mathrm{d}x = \cosh x + C，\int \cosh x \mathrm{d}x=\sinh x +C$ |

# 7. 三角函数相关公式

| 序 | 公式 |
| :--: | :-------------------------: |
| 1 | $\sin^2x+\cos^2x=1, \tan^2x+1=\sec^2x,\cot^2x+1=\csc^2x$ |
| 2 | $\begin{aligned}&\cosh^2x-\sinh^2x=1,\\ &\sinh(x\pm y)=\sinh x\cdot\cosh y \pm \sinh y\cdot\cosh x, \cosh(x\pm y) = \cosh x\cdot\cosh y,\\ &\sinh2x = 2\sinh x\cdot \cosh x, \cosh 2x = \cosh^2x+\sinh^2x = 2\cosh^2x - 1\end{aligned}$ |
| 3<br>积化和差公式 | $\sin\alpha\cos\beta=\frac12[\sin(\alpha+\beta)+\sin(\alpha-\beta)],\cos\alpha\sin\beta = \frac12[\sin(\alpha+\beta) - \sin(\alpha-\beta)]$ <br> $\cos\alpha\cos\beta = \frac12[cos(\alpha+\beta) + cos(\alpha - \beta)], \sin\alpha\sin\beta=-\frac12[\cos(\alpha+\beta) - \cos(\alpha-\beta)]$ |
| 4<br>和差化积公式 | $\displaystyle\sin\alpha + \sin\beta = 2\sin\left(\frac{\alpha+\beta}{2}\right) \cos\left(\frac{\alpha-\beta}{2}\right), \sin\alpha - \sin\beta = 2\sin\left(\frac{\alpha-\beta}{2}\right) \cos\left(\frac{\alpha+\beta}{2}\right)$ <br> $\displaystyle\cos\alpha + \cos\beta = 2\cos\left(\frac{\alpha+\beta}{2}\right) \cos\left(\frac{\alpha-\beta}{2}\right), \cos\alpha - \cos\beta = -2\sin\left(\frac{\alpha+\beta}{2}\right) \sin\left(\frac{\alpha-\beta}{2}\right)$ |
| 5<br>三角代换 | $\displaystyle\sqrt{a^2-x^2}\xrightarrow{x=a\sin t}a\cos t, \sqrt{a^2+x^2} \xrightarrow{x=a\tan t}a\sec t, \sqrt{x^2-a^2}\xrightarrow{x=a\sec t}a\tan t \left( a>0,t\in\left( -\frac{\pi}{2} , \frac{\pi}{2} \right) \right)$ |
| 6 | $\displaystyle\int\sqrt{a^2-x^2}\mathrm{d}x = \frac{a^2}{2}\arcsin\frac xa + \frac12x\sqrt{a^2-x^2} + C(a>0)$ |
| 7 | $\displaystyle\int\sqrt{x^2+a^2}\mathrm{d}x = \frac x2\sqrt{x^2+a^2} + \frac{a^2}2\ln \left\| x+\sqrt{x^2+a^2} \right\|+C(a>0)$ |
| 8 | $\displaystyle\int\frac{1}{\sqrt{x^2+a^2}}\mathrm{d}x = \ln\left\| x+ \sqrt{x^2+a^2} \right\| +C$, $\displaystyle\int\frac{1}{\sqrt{x^2-a^2}}\mathrm{d}x = \ln\left\| x + \sqrt{x^2+a^2}\right\|(a>0)$，特别地，$\displaystyle\int\frac{1}{\sqrt{x^2+1}}\mathrm{d}x=\left(x+\sqrt{x^2+1}\right) = \mathrm{arsinh}\, x$, $\displaystyle\int\frac{1}{\sqrt{x^2-1}}\mathrm{d}x = \ln\left( x+\sqrt{x^2-1}\right) = \mathrm{arcosh}\, x$ |
| 9 | $\displaystyle\int\sec x\mathrm{d}x = \int\frac{1}{\cos x}\mathrm{d}x = \int\frac{\sec^2x+\sec x \tan x}{\sec x + \tan x}\mathrm{d}x = \int\frac{1}{\sec x + \tan x}\mathrm{d}(\sec x + \tan x) = \ln \left\| \sec x + \tan x \right\| + C$ |
| 10 | $\displaystyle\int\csc x\mathrm{d}x = \int\frac{1}{\sin x}\mathrm{d}x = \ln\left\| \tan\frac x2 \right\| + C = \frac 12 \ln \left\| \frac{1-\cos x}{1 + \cos x}\right\| + C$ |
| 11 | $\displaystyle\int\tan x\mathrm{d}x = \int\frac{\sin x}{\cos x}\mathrm{d}x = -\int\frac{1}{\cos x}\mathrm{d}(\cos x) = -\ln \left\| \cos x\right\| +C$ |
| 12 | $\displaystyle\int\frac{1}{\tan x}\mathrm{d}x = \int\frac{\cos x}{\sin x}\mathrm{d}x = \int\frac{1}{\sin x}\mathrm{d}(\sin x) = \ln\left\| \sin x \right\| + C$ |
| 13 | $\displaystyle\int\arctan x\mathrm{d}x = x\arctan x - \ln \left\| 1+x^2 \right\| +C$ |
| 14<br>Wallis 公式 | $\displaystyle\int^{\frac{\pi}{2}}_{0}\sin^nx\mathrm{d}x = \int^{\frac{\pi}{2}}_{0}\cos^nx\mathrm{d}x = \begin{cases} \displaystyle\frac{n-1}{n}\cdot\frac{n-3}{n-2}\cdot\cdots\cdot\frac12\cdot\frac\pi2,&n为偶数 \\ \displaystyle\frac{n-1}{n}\cdot\frac{n-3}{n-2}\cdot\cdots\cdot\frac23\cdot1,&n为奇数 \end{cases} \\ = \begin{cases} \displaystyle\frac{(n-1)!!}{n!!}\cdot\frac\pi2,&n为偶数 \\ \displaystyle\frac{(n-1)!!}{n!!},&n为奇数 \end{cases}$ |
| 15 | $\displaystyle\int_0^\pi\sin^nx\mathrm{d}x = 2\int^\frac\pi2_0\sin^nx\mathrm{d}x,\int_0^\pi\cos^nx\mathrm{d}x = \begin{cases} 2\displaystyle\int^\frac\pi2_0\cos^nx\mathrm{d}x,&n为偶数 \\ 0,&n为奇数 \end{cases}$ <br> $\displaystyle\int_0^{2\pi}\sin^nx = \int_0^{2\pi}\cos^nx\mathrm{d}x = \begin{cases} 4\displaystyle\int^\frac\pi2_0\sin^nx\mathrm{d}x,&n为偶数 \\ 0,&n为奇数 \end{cases}$ <br> $\displaystyle\int_0^\frac\pi2f(\sin x)\mathrm{d}x = \int_0^\frac\pi2f(\cos x)\mathrm{d}x$, $\displaystyle\int_0^\pi f(\sin x)\mathrm{d}x \ne \int_0^\pi f(\cos x)\mathrm{d}x$ <br> $\displaystyle\int_0^\pi\pi f(\sin x)\mathrm{d}x = \frac\pi2\int_0^\pi f(\sin x)\mathrm{d}x = \pi\int_0^\frac\pi2 f(\sin x)\mathrm{d}x$ |
| 16<br>万能公式 | 令 $u=\displaystyle\tan\frac x2,x=2\arctan u$，则 $\displaystyle\sin x = \frac{2u}{1+u^2},\cos x=\frac{1-u^2}{1+u^2}, \mathrm{d}x = \frac{2}{1+u^2}\mathrm{d}u$ |
| 17 | $\displaystyle\int \frac{a\cos x+ b\sin x}{c\cos x + d \sin x} = A\int\mathrm{d}x+B\int\frac{\mathrm{d}(c\cos x+d \sin x)}{c \cos x + d \sin x}$ <br> (设 $a\cos x + b\sin x = A(c \cos x + d \sin x) + B(c \cos x + d \sin x)'$，由待定系数法求出A、B)$ |

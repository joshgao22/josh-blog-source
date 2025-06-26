---
title: Josh's Summary — 工科数学分析<br>Part 1.1 基本微分与积分
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
  - - Josh 的学习笔记
    - 微积分
  - - 数学
    - 微积分
---

$\newcommand\diff{\mathop{}\!\mathrm{d}}$

# 1. 常见等价无穷小

||||
| :--: | :--: | :--: |
| $\sin x \sim x$ | $\tan x \sim x$ | $\arcsin x \sim x$ |
| $\arctan x \sim x$ | $\displaystyle\left( 1 - \cos x \right) \sim \dfrac{x^2}{2}$ | $\ln (1+x) \sim x$ |
| $\left( e^x -1 \right) \sim x$ | $\left( a^x - 1 \right) \sim x \ln a$ | $\left( \left( 1+x \right) ^\alpha -1 \right) \sim \alpha x$  |

<!-- more -->

# 2. 导数基本公式

| 一般函数 | 三角函数 | 反三角函数 | 双曲函数<br>(圆函数) |
| :--: | :--: | :--: | :----: |
| $\left( C \right)' = 0$ | $\left( \sin x \right)' = \cos x$ | $\displaystyle\left( \arcsin x \right)' = \dfrac{1}{\sqrt{1-x^2}}$ | $\displaystyle\left( \sinh x \right)' = \left(\dfrac{e^x-e^{-x}}{2}\right)' = \cosh x$ |
| $\left( x^\alpha \right)'=\alpha x^{\alpha-1} \left( \alpha \in  \mathbb{Z} \right)$ | $\left( \cos x \right)' = -\sin x$ | $\displaystyle\left( \arccos x \right)' = -\dfrac{1}{\sqrt{1-x^2}}$ | $\displaystyle\left( \cosh x \right)' = \left( \dfrac{e^x+e^{-x}}{2} \right)'=\sinh x$ |
| $\displaystyle\left( \log_ax\right)'=\dfrac{1}{x\ln a}$ | $\displaystyle\left( \tan x \right)' = \dfrac{1}{\cos^2 x}=\sec^2 x$ | $\displaystyle\left( \arctan x \right)' = \dfrac{1}{1+x^2}$ | $\displaystyle\left( \mathrm{arsinh} \, x \right)' = \left(\ln \left( x+\sqrt{x^2 + 1} \right) \right)' = \dfrac{1}{x^2+1}$ |
| $( \ln$\| $x$ \|$)'=\displaystyle\dfrac{1}{x}$ | $\displaystyle\left( \cot x \right)' = -\dfrac{1}{\sin^2 x} = -\csc^2 x$ | $\displaystyle\left( \mathrm{arccot} \, x \right)' = -\dfrac{1}{1+x^2}$ | $\displaystyle\left( \mathrm{arcosh} \, x \right)' = \left( \ln \left( x + \sqrt{x^2 - 1} \right) \right)' = \dfrac{1}{\sqrt{x^2-1}}$
|  | $\left( \sec x \right)' = \sec x \cdot \tan x$ |
|  | $\left( \csc x \right)' = \csc x \cdot \cot x$ |

- Newton-Leibniz 公式：

  $$
  \begin{aligned}
  \left( u \cdot v \right)^{\left( n \right)} &= u^{\left( n \right)}v + nu^{\left( n-1 \right)}v' + \dfrac{n \left( n-1 \right)}{2!}u^{\left( n-2 \right)}v'' + \cdots + \dfrac{n\left( n-1 \right)\cdots\left(n-k+1\right)}{k!}u^{\left( n-k \right)}v^{(k)} + \cdots + uv^{\left( n \right)} \\
  &= \sum_{k=0}^{n}C_n^k u^{\left( u-k \right)}v^k
  \end{aligned}
  $$

  可以注意到 Newton-Leibniz 公式具有类似二项式定理展开的形式。

# 3. 几个初等函数的 Maclaurin 公式

## 3.1 Taylor 公式的一般形式

$$\begin{aligned}
f(x)&=f(x_0) + f'(x_0)(x-x_0) + \dfrac{f''(x_0)}{2!}(x-x_0)^2 + \cdots + \dfrac{f^{(n)}(x_0)}{n!}(x-x_0)^n + R_n
\\
&= \sum_{k=0}^n \dfrac{f^{(n)}(x_0)}{n!}(x - x_0)^n + R_n
\end{aligned}$$

其中

$$
R_n = \dfrac{f^{(n+1)}(\xi)}{(n+1)!}(x-x_0)^{n+1} \xlongequal{x\to x_0} o\left( \left( x - x_0 \right)^n \right), \quad \xi\,在\,x\,和\,x_0\,之间
$$

## 3.2 Maclaurin 公式的一般形式

$$\begin{aligned}
f(x) &= f(0)+f'(0)\,x + \dfrac{f''(0)}{2!}x^2 + \cdots + \dfrac{f^{(n)}(0)}{n!}x^n + R_n
\\
&= \sum_{k=0}^n \dfrac{f^{(n)}(0)}{n!}x^n + R_n
\end{aligned}$$

其中

$$
R_n = \dfrac{f^{(n+1)}(\xi)}{(n+1)!}x^{n+1} = \dfrac{f^{(n+1)}(\theta x)}{(n+1)!} x^{n+1} \xlongequal{x\to x_0} o\left( \left( x - x_0 \right)^n \right), \quad \xi\,在\,0\,和\,x\,之间, 0<\theta<1
$$

## 3.3 指数函数的 Maclaurin 展开式

$$
e^x=1+x+\dfrac{x^2}{2} + \dfrac{x^3}{3} + \cdots +\dfrac{x^n}{n!} + \dfrac{e^{\theta x}}{(n+1)!}, \quad 0<\theta<1
$$

## 3.4 正弦函数的 Maclaurin 展开式

$$
\sin x = x - \dfrac{x^3}{3!} + \dfrac{x^5}{5!} + \cdots + (-1)^{m-1}\dfrac{x^{2m-1}}{(2m-1)!} + R_{2m}
$$

其中

$$
R_{2m}=\dfrac{\sin\left( \theta x + \dfrac{(2m-1)\pi}{2}\right)}{(2m+1)!}x^{2m+1} = o(x^{2m}), \quad 0<\theta<1
$$

## 3.5 余弦函数的 Maclaurin 展开式

$$
\cos x = 1 - \dfrac{x^2}{2!} + \dfrac{x^4}{4!} + \cdots + (-1)^m\dfrac{x^{2m}}{2m!} + R_{2m+1}
$$

其中

$$
R_{2m+1}=\dfrac{\cos(\theta x + (m+1)\pi)}{(2m+2)!} x^{2m+2}=o(x^{2m+2}), \quad 0<\theta<1
$$

## 3.6 $\ln (1+x)$ 的 Maclaurin 展开式

$$
\ln(1+x) = x - \dfrac{x^2}{2} + \dfrac{x^3}{3} -\dfrac{x^4}{4} + \cdots + \dfrac{(-1)^{n-1}x^n}{n} + R_n(x)
$$

其中

$$
R_n(x) = \dfrac{(-1)^n}{(n+1)(1+\theta x)^{n+1}}x^{n+1} = o(x^n), \quad 0<\theta<1
$$

## 3.7 $(1+x)^\alpha$ 的 Maclaurin 展开式

$$
(1+x)^\alpha = 1 + \alpha x + \dfrac{\alpha(\alpha-1)}{2!}x^2 + \cdots + \dfrac{\alpha(\alpha - 1)\cdots (\alpha-n+1)}{n!}x^n + R_n(x)
$$

其中

$$
R_n(x) = \dfrac{\alpha(\alpha -1)\cdots(\alpha-n)}{(n+1)!}(1+\theta x)^{\alpha-n-1}x^{n+1} = o(x), \quad 0<\theta<1
$$

## 3.8 $\dfrac{1}{1-x}$ 的 Maclaurin 展开式

$$
\dfrac{1}{1-x} = 1+x+x^2+\cdots+x^n +o(x^n)
$$

# 4. 曲率及曲率半径

| 曲率 | 曲率半径 |
| :--: | :--: |
| $K=\left\| \dfrac{\diff \alpha}{\diff s}\right\| \xlongequal{令\ \tan \alpha = y', 则\ \alpha = \arctan y'} \dfrac{\left\| \dfrac{y''}{1+y'^2}\diff x \right\|}{\sqrt{1+y'^2}}\diff x = \dfrac{\left\| y'' \right\|}{\left( 1 + y' \right)^{3/2}}$ | $R = \dfrac{1}{K}$ |

# 5. 积分常用公式

| 序 | 描述 | 公式 |
| :--: | :----: | :----------------------------------------------:|
| 1 | 定积分的定义 | $\displaystyle\int^1_0f(x)\diff x=I=\lim_{\lambda \to 0}\sum_{i=1}^{n}f(\xi_i)\Delta x_i$，其中 $\lambda = \max \left\{ \Delta x_1,\Delta x_2, \cdots, \Delta x_n \right\}$ |
| 2 | 变上限积分 | $考察定积分\displaystyle\int^x_af(x)\diff x=\int^x_af(t)\diff t$，对 $\displaystyle\Phi(x)=\int_a^xf(t)\diff t$，有  $\displaystyle\Phi'(x)=\dfrac{\diff }{\diff x}\int_a^xf(t)\diff t = f(x)$ |
| 3 | 变上限积分的扩展 | $\displaystyle\dfrac{\diff }{\diff x}\int^{\beta(x)}_{\alpha(x)}f'(t)\diff t = f\left[ \beta\left(x\right)\right]\beta'(x)-f[\alpha(x)]\alpha'(x)$ |
| 4 | 上下限对称的积分 | 若 $f(x)$ 为偶函数，则 $\displaystyle\int_{-a}^af(x)\diff x=2\int_0^af(x)\diff x$；若 $f(x)$ 为奇函数，则 $\displaystyle\int_{-a}^af(x)\diff x=0$ |
| 5 | 周期函数的积分 | $\displaystyle f(x+T)=f(x)\Rightarrow\int_0^{nT}f(x)\diff x=n\int^T_0f(x)\diff x$ |
| 6 | 分部积分法 | $\displaystyle\int uv'\diff x=uv - \int u'v\diff x \Leftrightarrow \int u\diff v = uv - \int v\diff u$ |

# 6. 基本积分表

| 序 | 积分式 |
| :--: | :-------------------------: |
| 1 | $\displaystyle\int k\diff x=kx+C$，特别地 $\displaystyle\int\diff x=x+C$ |
| 2 | $\displaystyle\int x^\mu\diff x=\dfrac{x^{\mu+1}}{\mu+1}+C(\mu\ne-1)$，特别地 $\displaystyle\int \dfrac{1}{x^2}\diff x=-\dfrac{1}{x}+C,\int\dfrac{1}{\sqrt{x}}\diff x=2\sqrt{x}+C$ |
| 3 | $\displaystyle\int\dfrac{1}{x}\diff x=\ln \|x\|+C$ |
| 4 | $\displaystyle\int\dfrac{1}{1+x^2}\diff x = \arctan x +C$ |
| 5 | $\displaystyle\int\dfrac{1}{\sqrt{1-x^2}}\diff x=\arcsin x+C$ |
| 6 | $\displaystyle\int\sin x\diff x = -\cos x+C, \int\cos x\diff x = \sin x +C$ |
| 7 | $\displaystyle\int\dfrac{\diff x}{\cos^2x} = \int\sec^2x\diff x = \tan x +C, \int\dfrac{\diff x}{\sin^2x}=\int\csc^2x\diff x = -\cot x +C$ |
| 8 |  $\displaystyle\int a^x\diff x = \dfrac{a^x}{\ln a} + C$，特别地 $\displaystyle\int e^x\diff x = e^x +C$
| 9 | $\displaystyle\int \sinh x\diff x = \cosh x + C，\int \cosh x \diff x=\sinh x +C$ |

# 7. 三角函数相关公式

| 序 | 公式 |
| :--: | :-------------------------: |
| 1 | $\sin^2x+\cos^2x=1, \tan^2x+1=\sec^2x,\cot^2x+1=\csc^2x$ |
| 2 | $\begin{aligned}&\cosh^2x-\sinh^2x=1,\\ &\sinh(x\pm y)=\sinh x\cdot\cosh y \pm \sinh y\cdot\cosh x, \cosh(x\pm y) = \cosh x\cdot\cosh y,\\ &\sinh2x = 2\sinh x\cdot \cosh x, \cosh 2x = \cosh^2x+\sinh^2x = 2\cosh^2x - 1\end{aligned}$ |
| 3<br>积化和差公式 | $\sin\alpha\cos\beta=\dfrac12[\sin(\alpha+\beta)+\sin(\alpha-\beta)],\cos\alpha\sin\beta = \dfrac12[\sin(\alpha+\beta) - \sin(\alpha-\beta)]$ <br> $\cos\alpha\cos\beta = \dfrac12[cos(\alpha+\beta) + cos(\alpha - \beta)], \sin\alpha\sin\beta=-\dfrac12[\cos(\alpha+\beta) - \cos(\alpha-\beta)]$ |
| 4<br>和差化积公式 | $\displaystyle\sin\alpha + \sin\beta = 2\sin\left(\dfrac{\alpha+\beta}{2}\right) \cos\left(\dfrac{\alpha-\beta}{2}\right), \sin\alpha - \sin\beta = 2\sin\left(\dfrac{\alpha-\beta}{2}\right) \cos\left(\dfrac{\alpha+\beta}{2}\right)$ <br> $\displaystyle\cos\alpha + \cos\beta = 2\cos\left(\dfrac{\alpha+\beta}{2}\right) \cos\left(\dfrac{\alpha-\beta}{2}\right), \cos\alpha - \cos\beta = -2\sin\left(\dfrac{\alpha+\beta}{2}\right) \sin\left(\dfrac{\alpha-\beta}{2}\right)$ |
| 5<br>三角代换 | $\displaystyle\sqrt{a^2-x^2}\xrightarrow{x=a\sin t}a\cos t, \sqrt{a^2+x^2} \xrightarrow{x=a\tan t}a\sec t, \sqrt{x^2-a^2}\xrightarrow{x=a\sec t}a\tan t \left( a>0,t\in\left( -\dfrac{\pi}{2} , \dfrac{\pi}{2} \right) \right)$ |
| 6 | $\displaystyle\int\sqrt{a^2-x^2}\diff x = \dfrac{a^2}{2}\arcsin\dfrac xa + \dfrac12x\sqrt{a^2-x^2} + C(a>0)$ |
| 7 | $\displaystyle\int\sqrt{x^2+a^2}\diff x = \dfrac x2\sqrt{x^2+a^2} + \dfrac{a^2}2\ln \left\| x+\sqrt{x^2+a^2} \right\|+C(a>0)$ |
| 8 | $\displaystyle\int\dfrac{1}{\sqrt{x^2+a^2}}\diff x = \ln\left\| x+ \sqrt{x^2+a^2} \right\| +C$, $\displaystyle\int\dfrac{1}{\sqrt{x^2-a^2}}\diff x = \ln\left\| x + \sqrt{x^2-a^2}\right\|(a>0)$<br>特别地，$\displaystyle\int\dfrac{1}{\sqrt{x^2+1}}\diff x=\ln\left(x+\sqrt{x^2+1}\right) = \mathrm{arsinh}\, x$, $\displaystyle\int\dfrac{1}{\sqrt{x^2-1}}\diff x = \ln\left( x+\sqrt{x^2-1}\right) = \mathrm{arcosh}\, x$ |
| 9 | $\displaystyle\int\sec x\diff x = \int\dfrac{1}{\cos x}\diff x = \int\dfrac{\sec^2x+\sec x \tan x}{\sec x + \tan x}\diff x = \int\dfrac{1}{\sec x + \tan x}\diff (\sec x + \tan x) = \ln \left\| \sec x + \tan x \right\| + C$ |
| 10 | $\displaystyle\int\csc x\diff x = \int\dfrac{1}{\sin x}\diff x = \ln\left\| \tan\dfrac x2 \right\| + C = \dfrac 12 \ln \left\| \dfrac{1-\cos x}{1 + \cos x}\right\| + C$ |
| 11 | $\displaystyle\int\tan x\diff x = \int\dfrac{\sin x}{\cos x}\diff x = -\int\dfrac{1}{\cos x}\diff (\cos x) = -\ln \left\| \cos x\right\| +C$ |
| 12 | $\displaystyle\int\dfrac{1}{\tan x}\diff x = \int\dfrac{\cos x}{\sin x}\diff x = \int\dfrac{1}{\sin x}\diff (\sin x) = \ln\left\| \sin x \right\| + C$ |
| 13 | $\displaystyle\int\arctan x\diff x = x\arctan x - \ln \left\| 1+x^2 \right\| +C$ |
| 14<br>Wallis 公式 | $\displaystyle\int^{\pi/2}_{0}\sin^nx\diff x = \int^{\pi/2}_{0}\cos^nx\diff x = \begin{cases} \displaystyle\dfrac{n-1}{n}\cdot\dfrac{n-3}{n-2}\cdot\cdots\cdot\dfrac12\cdot\dfrac\pi2,&n为偶数 \\ \displaystyle\dfrac{n-1}{n}\cdot\dfrac{n-3}{n-2}\cdot\cdots\cdot\dfrac23\cdot1,&n为奇数 \end{cases} \\ = \begin{cases} \displaystyle\dfrac{(n-1)!!}{n!!}\cdot\dfrac\pi2,&n为偶数 \\ \displaystyle\dfrac{(n-1)!!}{n!!},&n为奇数 \end{cases}$ |
| 15 | $\displaystyle\int_0^\pi\sin^nx\diff x = 2\int^{\pi/2}_0\sin^nx\diff x,\int_0^\pi\cos^nx\diff x = \begin{cases} 2\displaystyle\int^{\pi/2}_0\cos^nx\diff x,&n为偶数 \\ 0,&n为奇数 \end{cases}$ <br> $\displaystyle\int_0^{2\pi}\sin^nx = \int_0^{2\pi}\cos^nx\diff x = \begin{cases} 4\displaystyle\int^{\pi/2}_0\sin^nx\diff x,&n为偶数 \\ 0,&n为奇数 \end{cases}$ <br> $\displaystyle\int_0^{\pi/2}f(\sin x)\diff x = \int_0^{\pi/2}f(\cos x)\diff x$, $\displaystyle\int_0^\pi f(\sin x)\diff x \ne \int_0^\pi f(\cos x)\diff x$ <br> $\displaystyle\int_0^\pi\pi f(\sin x)\diff x = \dfrac\pi2\int_0^\pi f(\sin x)\diff x = \pi\int_0^{\pi/2} f(\sin x)\diff x$ |
| 16<br>万能公式 | 令\ $u=\displaystyle\tan\dfrac x2,x=2\arctan u$，则\ $\displaystyle\sin x = \dfrac{2u}{1+u^2},\cos x=\dfrac{1-u^2}{1+u^2}, \diff x = \dfrac{2}{1+u^2}\diff u$ |
| 17 | $\displaystyle\int \dfrac{a\cos x+ b\sin x}{c\cos x + d \sin x} = A\int\diff x+B\int\dfrac{\diff (c\cos x+d \sin x)}{c \cos x + d \sin x}$ <br> (设 $a\cos x + b\sin x = A(c \cos x + d \sin x) + B(c \cos x + d \sin x)'$，由待定系数法求出 $A, B$) |

# 8. 定积分的几何应用

## 8.1 平面

### 8.1.1 直角坐标

1. 直角梯形的面积

$$\begin{equation*}
A = \int_a^b [f(x_1) - f(x_2)] \diff x
\end{equation*}$$

2. 曲边梯形的面积：对于参数方程 $\begin{cases}
     x = \varphi(x) \\ y = {\psi(x)}
   \end{cases}$，曲边梯形的面积（变换上下限）

   $$\begin{equation*}
   A = \int_a^b y\diff x = \int_{t_1}^{t_2} \psi(t) \diff \varphi(t) = \int_{t_1}^{t_2} \psi(t)\varphi'(t) \diff t
   \end{equation*}$$

3. 椭圆的面积：椭圆 $\begin{cases}
     x = a \cos t \\ y = b \sin t
   \end{cases}$，即 $\dfrac{x^2}{a^2} + \dfrac{y^2}{b^2} = 1$ 的面积

   $$\begin{equation*}
     A = 4 \int_0^a y \diff x = 4ab \int_0^{\pi/2} (1-\cos t)^2 \diff t = 3\pi a^2
   \end{equation*}$$

4. 摆线的面积：摆线 $\begin{cases}
     x = a (t - \sin t) \\ y = a (1 - \cos t)
   \end{cases}$ 的一拱与 $x$ 轴所围图形的面积

   $$\begin{equation*}
     A = \int_0^2\pi a y \diff x = a^2 \int_0^{2\pi} (1 - \cos t)^2 \diff t = 3\pi a^2
   \end{equation*}$$

### 8.1.2 极坐标

1. 扇形的面积：由 $\rho  = \rho(\theta)$ 及射线 $\theta = \alpha$ 和 $\theta = \beta$ 所围曲边扇形的面积

   $$\begin{equation*}
      A = \int_\alpha^\beta \frac{1}{2} \rho^2(\theta) \diff \theta = \frac{1}{2} \int_\alpha^\beta \rho^2 \diff \theta
   \end{equation*}$$

2. 阿基米德螺线：阿基米德螺线 $\rho = a \theta (a > 0)$ 对应 $\theta$ 从 $0$ 变换到 $2\pi$ 所围图形的面积

   $$\begin{equation*}
      A = \int_0^{2\pi} \frac{1}{2} (a\theta)^2\diff \theta = \frac{4}{3} \pi^3 a^2
   \end{equation*}$$

3. 双纽线：双纽线 $\rho^2 = a^2 \cos 2\theta$ 所围平面图形的面积

   $$\begin{equation*}
      A = 4 \int_0^{\pi/4} a^2 \cos 2\theta \diff \theta = a^2
   \end{equation*}$$

4. 心形线：心形线 $\rho = a(a+\cos\theta), a>0$ 所围平面图形的面积

   $$\begin{equation*}
      A = 2 \cdot \frac{1}{2} \int_0^\pi a^2 (1+ \cos \theta)^2 \diff \theta = \frac{3}{2}\pi a^2
   \end{equation*}$$

5. 曲线的弧长

   | 弧微分 | 直角坐标 | 参数方程 | 极坐标 |
   | :--: | :--: | :--: | :--: |
   | $\diff s = \sqrt{(\diff x)^2 + (\diff y)^2}$ | $s = \displaystyle\int_a^b \sqrt{1 + y'^2}\diff x$ | $s = \displaystyle\int_a^b \sqrt{\varphi'^2(t) + \psi'^2(t)}\diff t$ | $s = \displaystyle\int_\alpha^\beta \sqrt{\rho^2{\theta} + \rho'^2(\theta)} \diff\theta$

## 8.2 空间

1. 已知平行截面面积求立体体积：令 $A(x)$ 为截面面积，则立体体积

   $$\begin{equation*}
      V = \int_a^b A(x) \diff x
   \end{equation*}$$

2. 旋转体体积

   | 薄片法 | 柱壳法 |
   | :--: | :--: |
   | $V = \displaystyle\int_a^b \pi f^2(x) \diff x$ | $V = \displaystyle\int_a^b 2\pi x \left| f(x) \right|\diff x$ |

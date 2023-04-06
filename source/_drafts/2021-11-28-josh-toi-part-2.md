---
title: Josh 的学习笔记之信息论（Part 2 —— 离散信源及其信息测度）
mathjax: true
comments: true
copyright: true
sticky: false
toc:
  enable: true
  number: true
  wrap: true
  expand_all: true
  max_depth: 4
tags:
  - 信息测度
  - 离散信源
  - 离散平稳信源
  - Markov 信源
  - 信息熵
categories:
  - - Josh 的学习笔记
    - 信息论
abbrlink: 3db469da
date: 2021-11-28 00:13:29
---

# 信源的数学模型及分类[^MathematicalModelofInformationSource]

[^MathematicalModelofInformationSource]: 在通信系统中，收信者在未收到信息以前，对信源发出什么样的消息是不确定的，是随机的，所以可以用随机变量、随机矢量或随机过程来描述信源输出的消息，或者说用一个样本空间及其概率测度来描述信源。不同的信源根据其输出消息的不同的随机性质进行分类。

## 一维离散信源和一维连续信源(单符号)

|  | 概率空间 | 完备约束 | 含义 |
| :--: | :--------------: | :----: | :-------------- |
| 一维离散信源 | $$
\left[ \begin{array}{c}
 X\\
 P\left( x \right)\\
\end{array} \right] =\left[ \begin{matrix}
 x_1,&  x_2,&  \cdots ,&  x_q\\
 P\left( x_1 \right) ,&  P\left( x_2 \right) ,&  \cdots ,&  P\left( x_q \right)\\
\end{matrix} \right]
$$ | $$
\sum_i{P\left( x_i \right)}=1
$$ | 其中 $0\leq P\left( x_i \right) \leq 1\left( i=1,2,\cdots ,q \right)$ 是信源输出符号 $x_i\left( i=1,2,\cdots ,q \right)$ 的先验概率。一维离散信源每次只输出一个消息，消息的可能数目是**有限多个** |
| 一维连续信源 | $$
\left[ \begin{array}{c}
 X\\
 p\left( x \right)\\
\end{array} \right] =\left[ \begin{array}{c}
 \left( a,b \right)\\
 p\left( x \right)\\
\end{array} \right]
$$ | $$
\int_a^b{p\left( x \right) \mathrm{d}x}=1
$$ | 其中 $p\left( x \right)$ 是随机变量 $X$ 的概率密度函数。一维连续信源每次只输出一个消息，但消息的可能数目是**无限多个** |

## 多符号离散信源

&emsp;&emsp;采用随机矢量描述多符号离散信源，即

$$\begin{equation}\label{eq2-1}
\vec{X}=\left( X_1X_2\cdots X_N \right)
\end{equation}$$

$N$ 维随机矢量 $\vec{X}$ 也称为**随机序列**。

<!-- more -->

## 离散平稳信源和连续平稳信源

### 离散平稳信源

&emsp;&emsp;若信源输出的随机序列中每个随机变量都是离散型随机变量，且随机序列的各维概率密度函数与时间起点无关，即随机变量的统计特性不随着时间的推移而有所变化，此时称为离散平稳信源。

### 连续平稳信源

&emsp;&emsp;若信源输出的消息可用 $N$ 维随机矢量来描述，其中每个随机分量都是连续型随机变量，且随机变量的各维概率密度函数与时间起点无关，即随机变量的统计特性不随着时间的推移而有所变化，此时称为连续平稳信源。

> 平稳信源又分为无记忆信源(输出符号彼此统计独立)和有记忆信源。

# 离散信源的信息熵

## 信息量

### 信息量的直观定义

$$\begin{align}
&\text{收到某消息获得的信息量} \nonumber \\
=&\text{不确定性减少的量} \nonumber \\
=&(\text{收到消息}\textbf{前}\text{关于某件事发生的不确定性})-(\text{收到此消息}\textbf{后}\text{的不确定性}) \nonumber
\end{align}$$

> 特别地，对于无噪信道
> $$\begin{align}
&\text{收到某消息获得的信息量} \nonumber \\
=&\text{收到消息前关于某事件发生的不确定性} \nonumber \\
=&\text{信源输出某消息中所含信息量} \nonumber
\end{align}$$

### 自信息量

&emsp;&emsp;若已知事件 $a_i$ 发生，则该事件所含有的信息量称为自信息量，定义为[^BaseNumberNote] [^SelfInformationNote]

$$\begin{equation}\begin{aligned}
&\bbox{I\left( a_i \right) =\log _2\frac{1}{P\left( a_i \right)}\left( \mathrm{bit} \right) =\log \frac{1}{P\left( a_i \right)}\left( \mathrm{bit} \right) } \\
&\phantom{\,I\left( a_i \right) }=\ln \frac{1}{P\left( a_i \right)}\left( \mathrm{nat} \right) =\lg \frac{1}{P\left( a_i \right)}\left( \mathrm{hart} \right) =\log _r\frac{1}{P\left( a_i \right)}\left( r\text{进制单位} \right)
\end{aligned}\end{equation}$$

[^BaseNumberNote]: 一般采用以2 为底的对数，且为了书写简洁，把底数“2”略去不写。
[^SelfInformationNote]: $a_i$ 是一个随机变量，而 $I\left( a_i \right)$ 是 $a_i$ 的函数，没有确定的值，**不能作为整个信源的信息测度**。

> 自信息量的含义：当事件 $a_i$ 发生以前，表示事件 $x_i$ 发生的不确定性；当事件 $a_i$ 发生以后，表示事件 $a_i$ 所含有(或所提供)的信息量。在**无噪信道**中，能正确无误地传输到收信者，所以 $I\left( a_i \right)$ 可代表接收到消息 $x_i$ 后所获得的信息量，这是因为消除了 $I\left( a_i \right)$ 大小的不确定性，才获得这么大小的信息量。

### 联合自信息量

&emsp;&emsp;设信源模型为

$$\begin{equation}
\left[ \begin{array}{c}
 XY\\
 P\left( XY \right)\\
\end{array} \right] =\left[ \begin{matrix}
 x_1y_1,&  \cdots ,&  x_1y_m,&  x_2y_1,&  \cdots ,&  x_2y_m,&  \cdots ,&  x_ny_1,&  \cdots ,&  x_ny_m\\
 P\left( x_1y_1 \right) ,&  \cdots ,&  P\left( x_1y_m \right) ,&  P\left( x_2y_1 \right) ,&  \cdots ,&  P\left( x_2y_m \right) ,&  \cdots ,&  P\left( x_ny_1 \right) ,&  \cdots ,&  P\left( x_ny_m \right)\\
\end{matrix} \right]
\end{equation}$$

其中 $0\leq P\left( x_iy_j \right) \leq 1\left( i=1,2,\cdots n;j=1,2,\cdots ,m \right)$ 且 $\displaystyle\sum_i{\sum_j{P\left( x_iy_j \right)}}=1$，则联合自信息量为

$$\begin{equation}
\bbox{I\left( x_iy_j \right) =\log _2\frac{1}{P\left( x_iy_j \right)}}
\end{equation}$$

> 当 $X$ 和 $Y$ 相互独立时，$P\left( x_iy_j \right) =P\left( x_i \right) P\left( y_j \right)$
> $$\begin{equation}
I\left( x_iy_j \right) =\log _2\frac{1}{P\left( x_iy_j \right)}=\log _2\frac{1}{P\left( x_i \right)}+\log _2\frac{1}{P\left( y_j \right)}=I\left( x_i \right) +I\left( y_j \right)
\end{equation}$$
> 即**两个随机事件相互独立时，同时发生得到的信息量，等于各自自信息量之和**。

### 条件自信息量

&emsp;&emsp;设在 $y_i$ 条件下发生 $x_i$ 的条件概率为 $P\left( x_i|y_j \right) $，则其条件自信息量定义为

$$\begin{equation}
\bbox{I\left( x_i|y_j \right) =\log _2\frac{1}{P\left( x_i|y_j \right)}}
\end{equation}$$

表示在特定的条件下($y_i$ 已定)随机事件 $x_i$ 所带来的信息量。

> 自信息量、条件自信息量和联合自信息量之间的关系：
> $$\begin{equation}
\bbox{\begin{array}{c}
 I\left( x_iy_j \right) =\displaystyle\frac{1}{\log _2P\left( y_j \right) P\left( x_i|y_j \right)}=I\left( y_j \right) +I\left( x_i|y_j \right)\\
 \phantom{I\left( x_iy_j \right) }=\displaystyle\frac{1}{\log _2P\left( x_i \right) P\left( y_j|x_i \right)}=I\left( x_i \right) +I\left( y_j|x_i \right) \\
\end{array}}
\end{equation}$$

## 互信息量及其性质

### 互信息的定义

&emsp;&emsp;考虑信源 $X$、信宿 $Y$ 均为一维离散信源的情况，则 $y_i$ 对 $x_i$ 的互信息量定义为**后验概率与先验概率比值的对数**，即

$$\begin{equation}\begin{aligned}
I\left( x_i;y_j \right) &=\log _2\frac{P\left( x_i|y_j \right)}{P\left( x_i \right)}=\log _2\frac{1}{P\left( x_i \right)}-\log _2\frac{1}{P\left( x_i|y_j \right)}
\\
&=I\left( x_i \right) -I\left( x_i|y_j \right)
\end{aligned}\end{equation}$$

其中 $i=1,2,\cdots ,n; j=1,2,\cdots ,m$；信源发出消息 $x_i$ 的概率 $P\left( x_i \right)$ 称为**先验概率**；信宿收到 $y_i$ 后推测信源发出
的概率 $P\left( x_i|y_j \right)$ 称为**后验概率**。

| 观察角度 | 互信息量 | 含义 |
| :--: | :--------------: | :------------ |
| 接收端 | $$\begin{align}
I\left( x_i;y_j \right) &=\log _2\frac{1}{P\left( x_i \right)}-\log _2\frac{1}{P\left( x_i|y_j \right)} \nonumber \\
&\bbox{=I\left( x_i \right) -I\left( x_i|y_j \right) } \nonumber
\end{align}$$ | 两个不确定度之差是**不确定度被消除的部分**，即等于自信息量减去条件自信息量 |
| 发送端 | $$\begin{align}
I\left( y_j;x_i \right) &=\log _2\frac{1}{P\left( y_j \right)}-\log _2\frac{1}{P\left( y_j|x_i \right)} \nonumber \\
&\bbox{=I\left( y_j \right) -I\left( y_j|x_i \right) } \nonumber
\end{align}$$ | 观察者得知输入端发出 $x_i$ 前、后对输出端出现 $y_i$ 的不确定度的差 |
| 通信系统 | $$\begin{align}
I\left( x_i;y_j \right) &=\log _2\frac{1}{P\left( x_i \right) P\left( y_j \right)}-\log _2\frac{1}{P\left( x_iy_j \right)} \nonumber \\
&\bbox{=I\left( x_i \right) +I\left( y_j \right) -I\left( x_iy_j \right) } \nonumber
\end{align}$$ | 通信前，输入随机变量 $X$ 和输出随机变量 $Y$ 之间没有任何关联关系，即 $X$，$Y$ 统计独立；通信后，输入随机变量 $X$ 和输出随机变量 $Y$ 之间由信道的统计特性相联系

> 注：这三种表达式实际上是**等效的**，在实际应用中可根据具体情况选用一种较为方便的表达式。

### 互信息的性质

#### 对称性

$$
\bbox{I\left( x_i;y_j \right) =I\left( y_j;x_i \right) }
$$

互信息量反映两个随机事件的可能结果 $x_i$ 和 $y_i$ 之间的**统计约束程度**，从 $y_i$ 得到的关于 $x_i$ 的信息量 $I\left( x_i;y_j \right)$ 与从 $x_i$ 得到的关于 $y_i$ 的信息量 $I\left( y_i;x_j \right)$ 是一样的，只是观察的角度不同而已。

#### $X$ 和 $Y$ 相互独立时的互信息

$$\begin{align}
I\left( x_i;y_j \right) &=\log _2\frac{1}{P\left( x_i \right) P\left( y_j \right)}-\log _2\frac{1}{P\left( x_iy_j \right)} \nonumber \\
&\xlongequal{P\left( x_iy_j \right) =P\left( x_i \right) P\left( y_j \right)}0 \nonumber
\end{align}$$

$x_i$ 和 $y_i$ 之间不存在统计约束关系，从 $y_i$ 得不到关于 $x_i$ 的任何信息，反之亦然。

#### 互信息量可为正值或负值

- 当后验概率**大于**先验概率时，互信息量为**正**。
- 当后验概率**小于**先验概率时，互信息量为**负**[^NegativeInformation]。

[^NegativeInformation]: 收信者未收到 $y_i$ 以前，对消息 $x_i$ 是否出现的猜测难疑程度较小；但由于噪声的存在，接收到消息 $y_i$ 后对 $x_i$ 是否出现的猜测的难疑程度增加了，也就是收信者接收到消息 $y_i$ 后对 $x_i$ 出现的不确定性反而增加，所以获得的信息量为负值。

#### 条件互信息量

&emsp;&emsp;给定 $z_k$ 的条件下，$x_i$ 与 $y_i$ 之间的互信息量称为条件互信息量，记作

$$
\bbox{I\left( x_i;y_j|z_k \right) =\log _2\frac{P\left( x_i|y_jz_k \right)}{P\left( x_i|z_k \right)}} =\log _2\frac{1}{P\left( x_i|z_k \right)}-\log _2\frac{1}{P\left( x_i|y_jz_k \right)}
$$

消息 $x_i$ 与消息 $y_jz_k$ 之间的互信息量记作

$$
I\left( x_i;y_jz_k \right) =\log _2\frac{P\left( x_i|y_jz_k \right)}{P\left( x_i \right)}=\log _2\frac{1}{P\left( x_i \right)}-\log _2\frac{1}{P\left( x_i|y_jz_k \right)}
$$

则 $x_i$ 与 $y_jz_k$ 之间的互信息量可进一步表示为

$$
I\left( x_i;y_jz_k \right) =I\left( x_i;z_k \right) +I\left( x_i;y_j|z_k \right)
$$

即一个联合事件 $y_jz_k$ 发生后所提供的有关 $x_i$ 的信息量 ，等于 $z_k$ 发生后提供的有关 $x_i$ 的信息量 $I\left( x_i;z_k \right)$ 与给定 $z_k$ 条件下再出现 $y_i$ 后所提供的有关 $x_i$ 的信息量 $I\left( x_i;y_j|z_k \right)$ 之和。

## 信息熵

### 信息熵的定义

&emsp;&emsp;已知单符号离散无记忆信源的数学模型为

$$
\left[ \begin{matrix}
 X\\  P\left( x \right)\\
\end{matrix} \right] =\left[ \begin{matrix}
 a_i\\  P\left( a_i \right)\\
\end{matrix} \right]
$$

则定义信源的信息熵(也称为无条件熵、Shannon 熵、熵函数、熵)，即各离散消息**自信息量的数学期望**，为信源的**平均自信息量**[^AverageSelfInformation]，表示为

$$\begin{equation}
  \bbox{H\left( X \right) =E\left[ \log _2\frac{1}{P\left( a_i \right)} \right] =-\sum_i{P\left( a_i \right) \log _2P\left( a_i \right)}\,\, \left( \mathrm{bit}/\text{符号} \right) }
\end{equation}$$

[^AverageSelfInformation]: 信源熵和平均自信息量两者在数值上是相等的，但含义并不相同。**信源熵表征信源的<u>平均不确定度</u>**，平均自信息量是消除信源不确定度所需要的信息的量度。信源一定，不管它是否输出离散消息，只要这些离散消息具有一定的概率特性，必有信源的熵值，这熵值在总体平均的意义上才有意义，因而是一个确定值。

### 信源熵三种物理含义

1. 信源熵 $H\left(X\right)$ 是表示信源输出**后**，每个消息/符号所提供的平均信息量；

2. 信源熵 $H\left(X\right)$ 是表示信源输出**前**，信源的平均不确定性；

3. 用信源熵 $H\left(X\right)$ 来表征变量 $X$ 的随机性。

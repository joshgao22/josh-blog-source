---
title: MATLAB 函数 —— upsample（Signal Processing Toolbox）
tags:
  - MATLAB
  - 翻译
  - 上采样
categories:
  - [MATLAB, 函数用法]
mathjax: true
copyright: true
comments: true
abbrlink: 3a606c9c
date: 2020-11-26 22:23:39
---

`upsample`：将采样率提高整数倍

# 语法

`y = upsample(x,n)`

`y = upsample(x,n,phase)`

<!-- more -->

# 说明

`y = upsample(x,n)` 通过在样本之间插入 `n - 1`个零来增加 `x` 的采样率。如果 `x` 是矩阵，则该函数将每一列视为单独的序列。[举例](#Examples-01)

`y = upsample(x,n,phase)` 指定未采样序列所偏移的样本数。

# 示例

## 提高采样率<a name="Examples-01"></a>

将序列的采样率提高 `3` 倍。

``` matlab 输入
x = [1 2 3 4];
y = upsample(x,3)
```

``` plain 输出
y = 1×12

    1     0     0     2     0     0     3     0     0     4     0     0
```

将序列的采样率增加 `3` 倍，并将相位偏移增加 `2`。

``` matlab 输入
x = [1 2 3 4];
y = upsample(x,3,2)
```

``` plain 输出
y = 1×12

    0     0     1     0     0     2     0     0     3     0     0     4     0
```

将矩阵的采样率提高3倍。

``` matlab 输入
x = [1 2;
     3 4;
     5 6];
y = upsample(x,3)
```

``` plain 输出
y = 9×2

    1     2
    0     0
    0     0
    3     4
    0     0
    0     0
    5     6
    0     0
    0     0
```

# 输入参数

## `x` - 输入数组

输入数组，指定为**向量**或**矩阵**。如果 `x` 是矩阵，该函数会将不同列向量视为独立的通道。

**举例**：`cos(pi/4*(0:159)) + randn(1,160)` 指定加有高斯白噪声的正弦曲线。

**举例**：`cos(pi./[4;2]*(0:159))' + randn(160,2)` 指定一个两通道的正弦波。

## `n` - 上采样因子

上采样因子，指定为**正整数**。

**数据类型**：`single` | `double`

## `phase` - 偏移量

偏移量，指定为从 `0` 到 `n - 1` 的**正整数**。默认为 `0`。

**数据类型**：`single` | `double`

# 输出参数

## `y` - 上采样数组

上采样数组，以**向量**或**矩阵**的形式返回。`y` 有 `x × n` 个样本。

# 扩展功能

## C/C++ 代码生成

> 使用 MATLAB® Coder™ 生成 C 代码和 C++ 代码。

# 版本历史

在 R2006a 之前推出

# 另请参阅

[`decimate`](https://ww2.mathworks.cn/help/signal/ref/decimate.html) | [`downsample`](https://ww2.mathworks.cn/help/signal/ref/downsample.html) | [`interp`](https://ww2.mathworks.cn/help/signal/ref/interp.html) | [`interp1`](https://ww2.mathworks.cn/help/matlab/ref/interp1.html) | [`resample`](https://ww2.mathworks.cn/help/signal/ref/resample.html) | [`spline`](https://ww2.mathworks.cn/help/matlab/ref/spline.html) | [`upfirdn`](https://ww2.mathworks.cn/help/signal/ref/upfirdn.html)

> 原文参考：[https://ww2.mathworks.cn/help/signal/ref/upsample.html](https://ww2.mathworks.cn/help/signal/ref/upsample.html)

---
title: MATLAB 函数 —— awgn
tags:
  - MATLAB
  - 翻译
  - 高斯白噪声
categories:
  - [MATLAB, 函数用法]
  - [信号处理, 噪声]
mathjax: true
copyright: true
comments: true
abbrlink: 2f6db45b
date: 2020-11-27 01:15:12
---

`awgn`：在信号中添加高斯白斯噪声

# 语法

`out = awgn(in,snr)`
`out = awgn(in,snr,signalpower)`

`out = awgn(in,snr,signalpower,randobject)`
`out = awgn(in,snr,signalpower,seed)`
`out = awgn(___,powertype)`

<!-- more -->

# 说明

`out = awgn(in,snr)` 将高斯白噪声添加到矢量信号 `in` 中。此条语句假定 `in` 的功率为 0 dBW。

`out = awgn(in,snr,signalpower)` 接受以 dBW 为单位的输入信号功率值。要使该函数在添加噪声之前测量 `in` 的功率，请将 `signalpower` 指定为 `measured`。[举例](#Examples-01)

`out = awgn(in,snr,signalpower,randobject)` 接受满足前述两条语法，外加随机数流对象的输入组合以生成满足正态分布的随机噪声样本。有关产生可重复噪声样本的信息，请参见[提示](#tips)。[举例](#Examples-03)

`out = awgn(in,snr,signalpower,seed)` 指定用于初始化正态随机数生成器的种子值，该种子在添加高斯白噪声到输入信号时使用。有关产生可重复噪声样本的信息，请参见[提示](#tips)。

`out = awgn(___,powertype)` 除了前面任何语法中的输入自变量之外，还将可信号和噪声功率类型指定为 `dB` 或 `linear`。

有关 SNR 与噪声相对功率的其他度量（例如 $E_\mathrm{S}/N_0$ 和 $E_\mathrm{b}/N_0$）之间的关系，请参阅 [AWGN 信道噪声级别](https://ww2.mathworks.cn/help/comm/ug/awgn-channel.html#a1071501088)。

# 示例

## 将 AWGN 添加到锯齿波<span id="Examples-01"></span>

创建一个锯齿波。

``` matlab
t = (0:0.1:10)';
x = sawtooth(t);
```

添加高斯白噪声并绘制结果。

``` matlab
y = awgn(x,10,'measured');
plot(t,[x y])
legend('Original Signal','Signal with AWGN')
```

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-matlab-awgn/2020-11-27-matlab-awgn-010-AddAWGNToSawtoothSignalExample-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

## AWGN 信道中的常规 QAM 调制<span id="Examples-02"></span>

在存在高斯噪声的情况下，使用非矩形 16 元星座发送和接收数据。绘制噪声星座图，并针对两种不同的信噪比估算误符号率（SER）。

根据电话线调制解调器的 V.29 标准创建一个 16-QAM 星座。

``` matlab
c = [-5 -5i 5 5i -3 -3-3i -3i 3-3i 3 3+3i 3i -3+3i -1 -1i 1 1i];
M = length(c);
```

生成随机符号。

``` matlab
data = randi([0 M-1],2000,1);
```

使用 `genqammod` 函数调制数据。由于星座图不是矩形，因此必须进行常规 QAM 调制。

``` matlab
modData = genqammod(data,c);
```

使信号通过具有 20 dB 信噪比（SNR）的 AWGN 信道。

``` matlab
rxSig = awgn(modData,20,'measured');
```

显示接收信号的星座图和参考星座图。

``` matlab
h = scatterplot(rxSig);
hold on
scatterplot(c,[],[],'r*',h)
grid
hold off
```

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-matlab-awgn/2020-11-27-matlab-awgn-020-GeneralQAMModulationInAnAWGNChannelExample-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

使用 `genqamdemod` 函数解调接收到的信号。计算误符号数和误符号率。

``` matlab 输入
demodData = genqamdemod(rxSig,c);
[numErrors,ser] = symerr(data,demodData)
```

``` plain 输出
numErrors = 1
ser = 5.0000e-04
```

使用具有 10 dB SNR 的 AWGN 信道重复传输和解调过程。计算降低 SNR 后的误符号率。不出所料，当 SNR 降低时，性能会下降。

``` matlab 输入
rxSig = awgn(modData,10,'measured');
demodData = genqamdemod(rxSig,c);
[numErrors,ser] = symerr(data,demodData)
```

``` plain 输出
numErrors = 462
ser = 0.2310
```

## 用 RandStream 产生可重复的 AWGN<span id="Examples-03"></span>

使用 [`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html) 和 [`reset`](https://ww2.mathworks.cn/help/matlab/ref/randstream.reset.html) 对象函数生成高斯白噪声相加的结果。

将 X 的功率指定为 0 dBW，添加噪声以产生 10 dB 的 SNR，并利用本地随机流。

``` matlab 输入
S = RandStream('mt19937ar','Seed',5489);
sigin = sqrt(2)*sin(0:pi/8:6*pi);
sigout1 = awgn(sigin,10,0,S);
```

将 AWGN 添加到 `sigin`。 使用 [`isequal`](https://ww2.mathworks.cn/help/matlab/ref/isequal.html) 比较 `sigout1` 和 `sigout2`。 当不重置随机流时，输出不相等。

``` matlab 输入
sigout2 = awgn(sigin,10,0,S);
isequal(sigout1,sigout2)
```

``` plain 输出
ans = logical
   0
```

重置随机流对象，将对象置为初始状态，然后再将 AWGN 添加到 `sigout1`。将 AWGN 添加到 `sigin` 并将 `sigout1` 与 `sigout3` 比较。 重置随机流后，输出相等。

``` matlab 输入
reset(S);
sigout3 = awgn(sigin,10,0,S);
isequal(sigout1,sigout3)
```

``` plain 输出
ans = logical
   1
```

# 输入参数

## `in` - 输入信号

输入信号，指定为**标量**，**向量**或**数组**。假定输入信号的功率为0 dBW。

**数据类型**：`double`
**复数支持**：是

## `snr` - 信噪比（SNR）

信噪比（以 dB 为单位），指定为**标量**。

{% note info %}

注意：

加上噪声后，此函数会将相同的 `snr` 应用于完整输入信号的所有元素。阵列输入信号没有独立通道的概念。 要独立考虑多个频道，请参阅 [`comm.AWGNChannel`](https://ww2.mathworks.cn/help/comm/ref/comm.awgnchannel-system-object.html)。

{% endnote %}

**数据类型**：`double`

## `signalpower` - 信号功率

信号功率，指定为**标量**或 `measured`。

- 当 `signalpower` 为标量时，该值用作 `in` 的信号电平，以便根据 `snr` 的值确定适当的噪声电平。

- 当 `signalpower` 为 `measured` 时，将计算 `in` 的信号电平，以根据 `snr` 的值确定适当的噪声电平。

{% note info %}

注意：

当指定 `measured` 时，此函数将使用完整输入信号的所有元素来计算信号功率。在计算功率时，阵列输入信号没有独立通道的概念。

{% endnote %}

**数据类型**：`double`

## `randobject` - 随机数流对象

随机数流对象，指定为 [`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html) 对象。 随机流对象的状态确定 [`randn`](https://ww2.mathworks.cn/help/matlab/ref/randn.html) 函数产生的数字序列。使用 [`reset (RandStream)`](https://ww2.mathworks.cn/help/matlab/ref/randstream.reset.html) 函数及其属性来配置随机流对象。

`wgn` 使用 [`randn`](https://ww2.mathworks.cn/help/matlab/ref/randn.html) 生成正常的随机噪声样本。[`randn`](https://ww2.mathworks.cn/help/matlab/ref/randn.html) 函数使用 [`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html) 对象中的一个或多个统一值来生成每个正态值。

有关产生可重复噪声样本的信息，请参见[提示](#tips)。

## `seed` - 随机数生成器种子

随机数生成器种子，指定为**标量**。

## `powertype` - 信号功率单元

信号功率单元，指定为 `dB` 或 `linear`。默认为 `dB`。

- 当功率类型为 `dB` 时，以 dB 为单位测量 snr，以 dBW 为单位测量信号功率。

- 当功率类型为 `linear` 时，`snr` 以比率进行测量，`signalpower` 以瓦特为单位。

有关 SNR 与噪声相对功率的其他度量（例如 $E_\mathrm{S}/N_0$ 和 $E_\mathrm{b}/N_0$）之间的关系，请参阅 [AWGN 信道噪声级别](https://ww2.mathworks.cn/help/comm/ug/awgn-channel.html#a1071501088)。

# 输出参数

## `out`  - 输出信号

输出信号，以**标量**，**向量**或**数组**形式返回。 返回的输出信号是添加了高斯白噪声的输入信号。

# 提示<span id="tips"></span>

- 要生成可重复的高斯白噪声样本，请使用以下技巧之一：

  - 提供静态 `seed` 值作为 `awgn` 的输入。

  - 在将 `randobject` 传递为 `awgn` 的输入之前，请对 `randobject` 使用 [`reset (RandSteam)`](https://ww2.mathworks.cn/help/matlab/ref/randstream.reset.html) 函数。

  - 将处于已知状态的 `randobject` 提供给 `awgn`。有关更多信息，请参见 [`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html)。

# 扩展功能

## C/C++ 代码生成

> 使用 MATLAB® Coder™ 生成 C 代码和 C++ 代码。
>
>> 用法说明和限制：
>>
>> 支持代码生成，但包括 [`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html) 对象的语法除外。

# 版本历史

在 R2006a 之前推出

# 另请参阅

## 函数

[`RandStream`](https://ww2.mathworks.cn/help/matlab/ref/randstream.html) | [`bsc`]([RandStream](https://ww2.mathworks.cn/help/matlab/ref/randstream.html)) | [`randn`](https://ww2.mathworks.cn/help/matlab/ref/randn.html) | [`wgn`](https://ww2.mathworks.cn/help/comm/ref/wgn.html)

## 对象

[`comm.AWGNChannel`](https://ww2.mathworks.cn/help/comm/ref/comm.awgnchannel-system-object.html)

## 话题

[AWGN Channel Noise Level](https://ww2.mathworks.cn/help/comm/ug/awgn-channel.html#a1071501088)

原文参考：[https://ww2.mathworks.cn/help/comm/ref/awgn.html](https://ww2.mathworks.cn/help/comm/ref/awgn.html)

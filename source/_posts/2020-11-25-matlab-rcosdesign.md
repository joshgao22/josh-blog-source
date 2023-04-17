---
title: MATLAB 函数 —— rcosdesign
tags:
  - MATLAB
  - 翻译
  - 成形滤波器
  - 滤波器
categories:
  - [MATLAB, 函数用法]
mathjax: true
copyright: true
comments: true
abbrlink: 5c45edfe
date: 2020-11-25 22:32:42
---

`rcosdesign`：升余弦 FIR 脉冲成形滤波器设计

# 语法

`b = rcosdesign(beta,span,sps)`

`b = rcosdesign(beta,span,sps,shape)`

<!-- more -->

# 说明

`b = rcosdesign(beta,span,sps)` 返回系数 `b`，该系数对应于具有由 `beta` 指定的衰减因子的平方根升余弦 FIR 滤波器。滤波器有 `span` 个符号，每个符号包含 `sps` 个样本。 滤波器的阶次，也即 `sps * span` 必须是偶数。滤波器的能量为 1。[举例](#Examples-01)

`b = rcosdesign(beta,span,sps,shape)` 根据 `shape` 返回不同的滤波器。当 `shape` 为 `sqrt` 时，将返回根升余弦滤波器；当 `shape` 为 `normal` 时，将返回升余弦 FIR 滤波器。[举例](#Examples-02)

# 示例

## 设计根升余弦滤波器<a name="Examples-01"></a>

将滚降系数设为 `0.25`，波器的符号数设为 `6`，每个符号 `4` 个采样点。验证 `sqrt` 是 `shape` 参数的默认值。

``` matlab
h = rcosdesign(0.25,6,4);
mx = max(abs(h-rcosdesign(0.25,6,4,'sqrt')))
```

> <font face="Consolas">mx = 0</font><br>

``` matlab
fvtool(h,'Analysis','impulse')
```

![ ](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-25-matlab-rcosdesign/2020-11-25-matlab-rcosdesign-010-DesignASquareRootRaisedCosineFilterExample-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

## 升余弦和根升余弦滤波器的脉冲响应<a name="Examples-02"></a>

将升余弦滤波器与根升余弦滤波器进行比较。一个理想的（无限长）升余弦脉冲成形滤波器等效于两个级联的理想的根升余弦滤波器。因此，FIR 升余弦滤波器的脉冲响应应类似于与其自身卷积的根升余弦滤波器的脉冲响应。

设计一个衰减为 `0.25` 的升余弦滤波器。指定滤波器有 `4` 个符号，每个符号 `3` 个采样点。

``` matlab
rf = 0.25;
span = 4;
sps = 3;

h1 = rcosdesign(rf,span,sps,'normal');
fvtool(h1,'impulse')
```

![ ](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-25-matlab-rcosdesign/2020-11-25-matlab-rcosdesign-020-ImpulseResponsesOfNormalAndSquareRootRaisedCosineFiltersExample-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

**升余弦滤波器在 `sps` 的整数倍处具有零值点**。因此，它满足无码间串扰（ISI）的 Nyquist 准则。但是，根升余弦滤波器没有这样的特性：

``` matlab
h2 = rcosdesign(rf,span,sps,'sqrt');
fvtool(h2,'impulse')
```

![ ](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-25-matlab-rcosdesign/2020-11-25-matlab-rcosdesign-030-ImpulseResponsesOfNormalAndSquareRootRaisedCosineFiltersExample-02.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

将根升余弦滤波器与自身进行卷积。在最大值处截断脉冲响应，使其长度与 `h1` 相同。使用最大值将响应归一化。然后，将卷积后的根升余弦滤波器与升余弦滤波器进行比较。

``` matlab
h3 = conv(h2,h2);
p2 = ceil(length(h3)/2);
m2 = ceil(p2-length(h1)/2);
M2 = floor(p2+length(h1)/2);
ct = h3(m2:M2);

stem([h1/max(abs(h1));ct/max(abs(ct))]','filled')
xlabel('Samples')
ylabel('Normalized amplitude')
legend('h1','h2 * h2')
```

![ ](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-25-matlab-rcosdesign/2020-11-25-matlab-rcosdesign-040-ImpulseResponsesOfNormalAndSquareRootRaisedCosineFiltersExample-03.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

卷积响应的长度是有限的，因此根升余弦滤波器与自身进行卷积得到的结果会与升余弦滤波器不一致。增加 `span` 可以在响应之间获得更紧密的一致性，并更好地符合 Nyquist 准则。

## 将信号通过升余弦滤波器<a name="Examples-03"></a>

本例说明如何将信号通过根升余弦滤波器。

指定滤波器参数。

``` matlab
rolloff = 0.25;     % 滚降因子
span = 6;           % 滤波器宽度（符号数）
sps = 4;            % 每个符号的样本数
```

生成根升余弦滤波器的系数。

``` matlab
b = rcosdesign(rolloff, span, sps);
```

生成双极性数据向量。

``` matlab
d = 2*randi([0 1], 100, 1) - 1;
```

上采样并对数据进行滤波以实现脉冲成形。

``` matlab
x = upfirdn(d, b, sps);
```

添加噪声。

``` matlab
r = x + randn(size(x))*0.01;
```

对接收到的信号进行滤波和下采样以进行匹配滤波。

``` matlab
y = upfirdn(r, b, 1, sps);
```

有关如何使用根升余弦滤波器对信号进行插值和抽取的信息，请参见使[用 RRC 滤波器进行插值和抽取](https://ww2.mathworks.cn/help/comm/ug/interpolate-and-decimate-using-rrc-filter.html)（Communications Toolbox）。

# 输入参数

## `beta` - 滚降系数

滚降系数，指定为不大于 `1` 的**实非负标量**。滚降系数决定滤波器的多余带宽。滚降系数为 `0` 时为矩形滤波器（brick-wall filter），滚降因子为 `1` 时为纯升余弦滤波器。

**数据类型**：`double` | `single`

## `span` - 符号数

符号数，指定为**正整数标量**。

**数据类型**：`double` | `single`

## `sps` - 每个符号的样本数

每个符号的样本数（上采样因子），指定为**正整数标量**。

**数据类型**：`double` | `single`

## `shape` - 升余弦滤波器的形状

升余弦滤波器的形状，指定为 `normal` 或 `sqrt`。

# 输出参数

## `b` - FIR 滤波器系数

升余弦滤波器的系数，以**列向量**的形式返回。

**数据类型**：`double` | `single`

# 提示

- 如果您拥有 Communications Toolbox™ 软件的许可证，则可以执行具有流传输行为的多速率升余弦滤波。为此，请使用 System object™ 滤波器——`comm.RaisedCosineTransmitFilter` 和 `comm.RaisedCosineReceiveFilter`。

# 参考

[1] Tranter, William H., K. Sam Shanmugan, Theodore S. Rappaport, and Kurt L. Kosbar. *Principles of Communication Systems Simulation with Wireless Applications.* Upper Saddle River, NJ: Prentice Hall, 2004.

# 扩展功能

## C/C++ 代码生成

> 使用 MATLAB® Coder™ 生成 C 代码和 C++ 代码。
>
>> 用法说明和限制：
>>
>> 所有输入必须为常数。 如果表达式或变量的值不变，则也允许使用。

# 版本历史

在 R2013b 中推出

# 另请参阅

[`gaussdesign`](https://ww2.mathworks.cn/help/signal/ref/gaussdesign.html)

## 主题

[用 RRC 滤波器进行插值和抽取](https://ww2.mathworks.cn/help/comm/ug/interpolate-and-decimate-using-rrc-filter.html)（Communications Toolbox）

> 原文参考：[https://ww2.mathworks.cn/help/signal/ref/rcosdesign.html](https://ww2.mathworks.cn/help/signal/ref/rcosdesign.html)

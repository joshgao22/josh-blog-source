---
title: MATLAB 函数 —— gaussdesign
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
abbrlink: a2967b00
date: 2020-11-26 15:56:26
---

`gaussdesign`：高斯 FIR 脉冲成形滤波器设计

# 语法

`h = gaussdesign(bt,span,sps)`

<!-- more -->

# 说明

`h = gaussdesign(bt,span,sps)` 设计一个低通 FIR 高斯脉冲成形滤波器，并返回滤波器系数向量 `h`。滤波器有 `span` 个符号，每个符号包含 `sps` 个样本。滤波器的阶次，也即 `sps * span` 必须是偶数。[举例](#Examples-01)

# 示例

## GSM GMSK 数字蜂窝通信系统的高斯滤波器<a name="Examples-01"></a>

指定用于传输比特位的调制是高斯最小频移键控（GMSK）脉冲。此脉冲的 3-dB 带宽等于比特率的 `0.3` 倍，滤波器的符号数为 `4`，每个符号 `8` 个采样点。

``` matlab
bt = 0.3;
span = 4;
sps = 8;
h = gaussdesign(bt,span,sps);
fvtool(h,'impulse')
```

![ ](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-26-matlab-gaussdesign/2020-11-26-matlab-gaussdesign-010-GaussianFilterForAGSMGMSKDigitalCellularCommunicationSysExample-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

# 输入参数

## `bt` - 3-dB带宽符号时间积

3-dB 单边带带宽（以 Hz 为单位）和符号时间（以秒为单位）的乘积，指定为**正实标量**。较小的 `bt` 值会产生较大的脉冲宽度。

**数据类型**：`double` | `single`

## `span` - 符号数

符号数，指定为**正整数标量**（默认为 3）。

**数据类型**：`double` | `single`

## `sps` - 每个符号的样本数

每个符号周期的样本数（上采样因子），指定为**正整数标量**（默认为 2）。

**数据类型**：`double` | `single`

# 输出参数

## `h` - FIR 滤波器系数

高斯脉冲成形滤波器的 FIR 系数，以**行向量**的形式返回。系数已被归一化，因此标称通带增益始终为 1。

**数据类型**：`double` | `single`

# 参考

[1] Krishnapura, N., S. Pavan, C. Mathiazhagan, and B. Ramamurthi. “A baseband pulse shaping filter for Gaussian minimum shift keying.” *Proceedings of the 1998 IEEE International Symposium on Circuits and Systems*. Vol. 1, 1998, pp. 249–252.

[2] Rappaport, Theodore S. *Wireless Communications: Principles and Practice.* 2nd Ed. Upper Saddle River, NJ: Prentice Hall, 2002.

# 版本历史

在 R2013b 中推出

# 另请参阅

[`rcosdesign`](https://ww2.mathworks.cn/help/signal/ref/rcosdesign.html)

原文参考：[https://ww2.mathworks.cn/help/signal/ref/gaussdesign.html](https://ww2.mathworks.cn/help/signal/ref/gaussdesign.html)

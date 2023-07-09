---
title: "Josh's Notes: Verilog（Part 3 — 描述方式和设计层次）"
date: 2020-11-30 00:57:49
tags:
  - Verilog
  - HDL
  - 描述方式
  - 设计层次
  - Verilog 语法
categories:
  - [Josh 的学习笔记, Verilog]
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
abbrlink: fd117896
description: Verilog 语言可以用多种方式来描述硬件，同时，使用这些描述方式又可以在多个抽象层次上设计硬件。这是 Verilog 语言的重要特征。本文主要侧重于阐述 Verilog 语言的描述方法和设计层次，重点介绍了 Verilog 的 3 种描述方式以及 Verilog 可以应用的设计层次，体现出 Verilog 语言是一种非常灵活、强大的硬件描述语言。
---

# 1. 描述方式

在 [Part 2——Verilog 语言基础][]中己经介绍过，Verilog 语言有 3 种最基本的描述方式．

- 数据流描述，采用 `assign` 连续赋值语句；
- 行为描述，使用 `always` 语句或 `initial` 语句块中的过程赋值语句；
- 结构化描述，实例化已有的功能模块或原语。

下面分别说明这几种描述方式。

<!-- more -->

# 2. 数据流描述

## 2.1. 数据流

在数字电路中，信号经过组合逻辑时有点类似于数据的流动：信号从输入流向输出，而信号不会在其中存储。当输入发生变化时，总会在一定时间以后体现在输出端。

同样，可以模拟数字电路的这一特性，对其进行建模。通常将这种建模方式称为数据流建模。

数据流描述最基本的语句是 `assign` 连续赋值语句。

## 2.2. 连续赋值语句

<a id="fig.3-1"></a>

![图 3-1 一个电路模型](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-010-XORGate.png!sign){width=800px}

[图 3-1](#fig.3-1) 中的电路模型可以用如下的语句来描述：

``` verilog
assign #1 A_xor_wire = eq0 ^ eq1;
```

在任意一个时刻，`A_xor_wire` 线网的值是由 `eq0` 和 `eq1` 决定的，也可以说是由它们驱动的。

### 2.2.1. 连续赋值语句的特点

1. **连续驱动**

    在 [Part 2——Verilog 语言基础的 7.4 驱动和赋值][]部分，已经解释了驱动和赋值之间的微妙区别。

    连续赋值语句是连续驱动的。也就是说，任何时刻输入的任何变化都将导致该语句的重新计算。

2. **只有线网类型能在 `assign` 中赋值**

    由于仿真器中不会存储连续赋值语句中被赋值的变量值，因此该变量是线网类型（Net），不能是寄存器类型。需要记住，只有线网类型的变量才可以在 `assign` 语句中被赋值。

    另外，线网类型的变量可以被多重驱动，也就是说，可以在多个连续赋值语句中驱动同一个线网。在 [2.4 驱动源线网](#toc.2.4)小节中将进一步介绍。但是，寄存器变量就不同了，它不能被不同的行为进程（例如 `always` 语句块）驱动。

3. **使用 `assign` 对组合逻辑建模**

    通常建议使用 `assign` 对组合逻辑建模。因为 `assign` 语句的连续驱动特点与组合逻辑的行为非常相似。而且，在 `assign` 语句中加延时可以非常精确的模拟组合逻辑的惯性延时。

4. **并行性**

    `assign` 语句和行为语句块（`always` 和 `initial`)、其他连续赋值语句、门级模型之间是并行的。一个连续赋值语句是一个独立的进程，进程之间是并发，同时也是交织的。

### 2.2.2. 实例

<a id="fig.3-2"></a>

![图 3-2 两个半加器和一个或门组成全加器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-020-FullAdder.png!sign){width=800px}

在[图 3-2](#fig.3-2) 中，用两个半加器和一个或门组成一个全加器，这里将使用连续赋值语句描述这个电路。代码如下。

<a id="HalfAdder"></a>

``` verilog HalfAdd.v
module HalfAdd(X, Y, sum, C_out);

// IO端口声明
input X;
input Y;
output sum;
output C_out;

assign sum = X ^ Y;
assign C_out = X & Y;

endmodule
```

``` verilog FullAdd.v
module FullAdd(X, Y, C_in, sum, C_out);

// IO端口声明
input X;
input Y;
input C_in;
output sum;
output C_out;

// 线网类型用于连接
wire HalfAdd_A_sum;
wire HalfAdd_A_Cout;
wire HalfAdd_B_Cout;

assign C_out = HalfAdd_A_Cout | HalfAdd_B_Cout;

HalfAdd u_HalfAdd_A(
    .X      (C_in),
    .Y      (Y),
    .s      (HalfAdd_A_sum),
    .C_out  (HalfAdd_A_Cout)
    );

HalfAdd u_HalfAdd_B(
    .X      (C_in),
    .Y      (HalfAdd_A_sum),
    .s      (sum),
    .C_out  (HalfAdd_B_Cout)
    );

endmodule
```

在 `HalfAdd` 模块中，两个 `assign` 语句之间是**完全并行独立执行**的，它们的顺序与逻辑功能无关。同样，`FullAdd` 模块中，两个 `HalfAdd` 的实例和或门的 `assign` 语句之间的关系也是独立的。

## 2.3. 延时

在连续赋值语句中，可以对电路的延时进行建模。当然，也可以没有延时。比如：

``` verilog
assign #1 A_xor_wire = eq0 ^ eq1;           // `timescale 1ns/1ns
```

这个语句就表示该异或门的延时为 `1ns`，也就是说，输入端信号变化到输出端体现出来需要 `1ns` 的时间。

这里是比较笼统的延时模型。实际上，电路对不同的信号跳变表现出的延时往往并不一致。这些延时模型包括：

- 上升沿延时（输出变为 1）；
- 下降沿延时（输出变为 0）；
- 关闭延时（输出变成 `Z`，高阻态）；
- 输出变成 `X` 的延时。

用户可以分别描述这几种延时，比如：

``` verilog
assign #(1,2) A_xor_wire = eq0 ^ eq1;
assign #(1,2,3) A_xor wire = eq0 ^ eq1;
```

第一句表示，上升延时 `1ns`，下降延时 `2ns`，关闭延时和传递到 `X` 的延时为两者中最小的，即 `1ns`。

第二句表示，上升延时 `1ns`，下降延时 `2ns`，关闭延时为 `3ns`，传递到 `X` 的延时取1、2、3中最小的 `1ns`。

在一些电路模型中，延时分为最大、典型和最小3种情况。连续赋值语句中的延时也可以采用 `min:typ:max` 的格式来表示。例如：

``` verilog
assign #(4:5:6, 3:4:5) A_xor_wire = eq0 ^ eq1;
```

表示上升延时的 `min:typ:max` 为 `4:5:6`，下降延时的 `min:typ:max` 为 `3:4:5`。

需要注意的是，在连续赋值语句中的延时具有硬件电路中惯性延时的特性。也就是说，**任何小于其延时的信号变化脉冲将被滤除掉，不会体现在输出端口上**。

另外，`assign` 语句中的延时特性通常是被逻辑综合工具忽略的。因为综合工具要将 Verilog 语言模型综合成逻辑电路，而逻辑电路的延时是由基本的单元库和走线延时决定的。用户无法对逻辑单元指定延时，但是，用户可以在综合和实现工具中加时序约束，让工具尽量满足设计的时序要求。

## 2.4. 驱动源线网<a id="toc.2.4"></a>

下面分为几种线网类型来描述当线网具有多重驱动源时的情况。

### 2.4.1. 多重驱动 `wire`（错误）

``` verilog WS.v
module WS (A, B, C, D, WireShort)；

input A, B, C, D;
output WireShort;

wire WireShort;         //显式定义为wire类型

assign WireShort = A ^ B；
assign WireShort = C & D；

endmodule
```

在以上代码中，由于 `WireShort` 为 `wire` 类型，同时它有多重驱动源，因此仿真时 `WireShort` 的值将是 `X`，也就是不定态。

### 2.4.2. 线或、线与功能

可以使用 `wor` 线网类型来将不同的输出“线或”在一起，如下：

``` verilog // WO.v
module WO (A, B, C, D, WireOr);

input A, B, C, D;
output WireOr;

wor WireOr;             // 显式定义为 wor 类型

assign WireOr = A ^ B;
assign WireOr = C & D;

endmodule
```

逻辑综合以后，它具体对应的逻辑电路如[图 3-3](#fig.3-3) 所示。

<a id="fig.3-3"></a>

![图 3-3 线或的功能](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-030-WireOr.png!sign){width=700px}

同样，可以使用 `wand` 线网类型来将不同的输出“线与”在一起，如下：

``` verilog WA.v
module WA (A, B, C, D, WireAnd);

input A, B, C, D;
output WireAnd;

wand WireAnd;           // 显式定义为 wand 类型

assign WireAnd = A ^ B;
assign WireAnd = C & D;

endmodule
```

它对应的逻辑电路如[图 3-4](#fig.3-4) 所示。

<a id="fig.3-4"></a>

![图 3-4 线与的功能](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-040-WireAnd.png!sign){width=700px}

### 2.4.3. 三态总线功能

如果要实现多个三态总线相连，可以采用 `tri` 型线网：

``` verilog WT.v
module WT (A, B, C, D, WireTri, en1_n, en2_n);

input A, B, C, D, en1_n, en2_n;
output WireTri;

tri WireTri;            // 显式定义为 tri 类型

assign WireTri = (en1_n) ? 1'bz : (A ^ B);
assign WireTri = (en2_n) ? 1'bz : (C & D);

endmodule
```

其实现的电路如[图 3-5](#fig.3-5) 所示。

<a id="fig.3-5"></a>

![图 3-5 三态驱动总线电路](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-050-WireTri.png!sign){width=700px}

# 3. 行为描述<a id="toc.3"><a>

在 [Part 2——Verilog 语言基础的 2.2 3 种描述方法][]中介绍过行为描述的概念。所谓行为描述，是指用语言描述电路的行为。行为描述的语句有两种：`initial` 和 `always` 语句。

## 3.1. 行为描述的语句格式

`initial` 和 `always` 的后面一般跟语句或语句组（statement group)。语句可以是：非阻塞过程赋值、阻塞过程赋值、连续过程赋值或高级编程语句。下面开始介绍。

### 3.1.1. `initial` 或 `always` 过程块（procedural block）

`initial` 语句在 0 仿真时间执行，而且只执行一次；`always` 语句同样在 0 仿真时间开始执行，但是它将一直循环执行。这样的特点单单从它们的命名上就能看得出来：一个是 `initial`，就是初始化一次的意思；另一个是 `always`，就是总在运行的意思。

下面利用 `initial` 和 `always` 语句各自的特点，产生一个时钟发生器的模型。

``` verilog ClkGen.v
`timescale 1ns/1ns
module ClkGen (clk);

output clk;

reg clk;

initial             // 将 clk 初始化为 0
    clk = 0;

always              // 每 5ns 将 clk 翻转一次
    #5 clk = ~clk;

endmodule
```

在 0 时刻，`initial` 和 `always` 语句同时执行，**顺序随机**。假设先运行 `initial` 语句，那么 `clk` 变量被赋值为 0，这时 `initial` 语句进程将永远被挂起，再也不会执行。

然后，开始运行 `always` 语句。该 `always` 语句每隔 `5ns` 将 `clk` 信号翻转一次，一直不停地运行，这样就产生了一个周期是 `10ns` 的时钟信号。

{% note warning %}

在以上代码中，0 时刻，`initial` 和 `always` 语句同时执行，顺序随机，这样就存在一个冲突的问题。假设先执行的是 `always` 语句， `clk` 就不会被初始化为 0。

{% endnote %}

### 3.1.2. 过程块中的语句种类

在 `initial` 和 `always` 过程块中可以直接跟语句或者语句组。直接跟的语句可以是非阻塞过程赋值、阻塞过程赋值、连续过程赋值或高级编程语句。语句组可以是：`begin ... end` 和 `fork ... join` 两种。

语句组中可以有其他几种语句类型，而高级编程语句中也可以有语句组，它们可以互相嵌套，完成非常复杂的逻辑功能描述。

下面是 `always` 过程块中直接跟阻塞赋值语句：

``` verilog
always              // 每 5ns 将 clk 翻转一次
    #5 clk = ~clk;
```

下面的代码描述了语句组和高级编程语句的互相嵌套：

``` verilog
always @(posedge clk or negedge rst_n)
begin                               // 语句组
    if (~rst_n)                     // 高级编程语句
    begin                           // 语句组
        reg_A <= 0;                 // 非阻塞赋值语句
        reg_B <= 0;                 // 非阻塞赋值语句
    end
    else                            // 高级编程语句
    begin                           // 语句组
        reg_A <= input_A;           // 非阻塞赋值语句
        reg_B <= input_B;           // 非阻塞赋值语句
    end
end
```

从上例中可以看出，高级编程语句里面可以嵌套过程赋值语句，或者其他高级编程语句。

### 3.1.3. 时序控制（Timing Control）

在行为描述中，有几种方式对设计模型进行时序控制，它们是：

- 事件语句（`@`）；
- 延时语句（`#`）；
- 等待语句。

当执行 `initial` 或 `always` 语句块时遇到一个事件语句（`@`）、延时语句（`#`），或其表达式值为假（false）的等待语句时，语句块（或称为进程）的执行将被挂起（suspended)。直到发生该事件，或者已经过了指定延迟的时间单位数，或者等待语句表达式变为真（ture）时，才重新执行 `initial` 或 `always` 语句块。这个过程就是时序控制。Verilog 的行为描述中，正是利用这几种时序控制语句来实现各种各样的逻辑功能。

#### 3.1.3.1. 事件语句（`@`）的用法

<a id="fig.3-6"></a>

![图 3-6 D 触发器模型](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-060-DFlipFlop.png!sign){width=600px}

要实现[图 3-6](#fig.3-6) 中这样一个 D 触发器，通常用以下代码：

``` verilog TYP_DFF.v
module TYP_DFF (clk, D, Q);

input clk, D;
output Q;

reg Q;

always @(posedge clk) begin
    Q <= D;
end

endmodule
```

在 0 仿真时刻，`always` 语句块开始执行。当遇到 `@(posedge clk)` 语句时，该进程被挂起。等待 `clk` 的上升沿到来，才重新激活该进程。当 `clk` 的上升沿出现后，将 `D` 的值赋给 `Q`，`always` 语句块执行完成。

由于 `always` 语句的特点，`always` 语句马上开始重新执行，当遇到 `@(posedge clk)` 语句时，进程再一次挂起，等待 `clk` 的上升沿到来，才继续往下执行。

这样，在 `always` 语句中使用 `@` 事件语句很好地模拟了触发器的行为。综合工具会马上将上述代码映射成[图 3-6](#fig.3-6) 中的 D 触发器。

同样的道理，采用如下的代码也可以得到一样的 D 触发器功能：

``` verilog TYP_DFF.v
module TYP_DFF (clk, D, Q);

input clk, D;
output Q;

reg Q;

always begin
    @(posedge clk)
    Q <= D;
end

endmodule
```

当有多个条件语句时，一般将它们用 `or` 分隔开。例如，要实现一个带异步复位端的 D 触发器可以采用如下代码：

``` verilog TYP_DFF.v
module TYP_DFF (clk, D, Q, rst);

input clk, D, rst;
output Q;

reg Q;

always @(posedge clk or posedge rst) begin
    if (rst)
        Q <= 0;
    else
        Q <= D;
end

endmodule
```

当出现 `clk` 或 `rst` 的下降沿时，才会触发 `always` 语句。

#### 3.1.3.2. 延时语句（`#`）的用法

``` verilog
always              // 每 5ns 将 clk 翻转一次
    #5 clk = ~clk;
```

这里 `always` 的语句开始执行时，马上遇到 `#5`，`always` 语句块挂起。直到 `5ns` 以后才恢复执行，这时将 `clk` 取反。当再次执行 `always` 时，动作与上一次完全一致。这里，模拟了一个周期为 `10ns` 的时钟。

当然，这种写法一般用于仿真激励的产生，仅仅用于仿真。由于综合工具会将延时语句 `#5` 忽略，所以如上的代码无法综合成一个 `10ns` 周期的时钟发生器电路。

以下代码利用延时语句产生一个复位信号：

``` verilog
initial begin
    rst_n =1;
    #5 rst_n = 0;
    #100 rst_n=1;
end
```

当以上 `initial` 语句开始运行时，首先将 `rst_n` 赋值为 `1`。当遇到 `#5` 时，该 `initial` 的执行过程被暂时挂起，等待 `5ns` 后恢复执行，`rst_n` 被置 `0`，处于复位状态。然后，遇到 `#100`，等待 `100ns` 以后再恢复执行，`rst_n` 被置为 `1`。这时，`initial` 语句块被永远挂起，再也不会执行。于是，就产生了一个 `100ns` 的复位信号。

#### 3.1.3.3. 等待语句的用法

下面分析一下等待语句如何做时序控制：

``` verilog
module MY_LATCH (Strobe, D, Q);

input Strobe, D;
output Q;

reg Q;

always begin
    wait (Strobe == 1);
    Q = D;
end

endmodule
```

该语句表示，当 `always` 语句开始执行后，遇到 `wait()` 语句，如果括号内的变量不为真，则该进程被挂起，直到 `(Strobe == 1)` 为真，`always` 才继续往下执行，将 `D` 的值赋值给 `Q`，这样就模拟了一个电平敏感的锁存器。

要注意的是，目前多数综合工具还不支持 `wait` 语句，因此这个锁存器的功能只能在仿真时用，不能实现为具体的电路。

## 3.2. 过程赋值语句<a id="toc.3.2"></a>

所谓过程赋值语句就是在 `initial` 和 `always` 语句块中的赋值语句。赋值对象只能是寄存器变量类型。右边的表达式可以是任意操作符的表达式。

### 3.2.1. 阻塞赋值

阻塞赋值的语法如下：

``` verilog
寄存器变量 = 表达式;
```

所谓“阻塞赋值”，实际上有两层含义：

1. 右边表达式的计算和对左边寄存器变量的赋值是一个统一的原子操作中的两个动作。**这两个动作之间不能插入任何其他的动作**。

2. 如果多个阻塞赋值语句顺序出现在 `begin ... end` 语句中，**前面的语句在执行时，将完全阻塞后面的语句，直到前面语句的赋值完成以后，才会执行下一句的右边表达式计算**。例如 `begin m = n; n = m; end` 语句中，当 `m` 被完全赋值以后，再开始执行 `n=m`，将 `m` 的新值赋给 `n`。这样执行的结果就是的初始值不变，而且与 `n` 相等。

由于阻塞賦值的这一特点，通常会建议在对组合逻辑建模的时候，采用阻塞賦值，先看一段代码：

<a id="CombinationalLogicDemo"></a>

``` verilog
wire A_in, B_in, C_in;
reg Temp, D_out;
// ...
always @(A_in or B_in or C_in) begin
    Temp = A_in & B_in;             // 阻塞赋值
    D_out = Temp | C_in;            // 阻塞赋值
end
```

设计者的根本目的是得到如[图 3-7](#fig.3-7) 所示的电路。

<a id="fig.3-7"></a>

![图 3-7 一个组合逻辑](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-070-ACombinationalLogic.png!sign){width=700px}

在以上代码仿真时，正是利用了阻塞赋值的这一特点才模拟了该组合逻辑的行为。

首先，任何一个输入发生变化，`D_out` 必然发生变化。因此，在 `always` 的敏感列表中，包括 `A_in`，`B_in` 和 `C_in`。在内部计算时，首先将 `A_in` 和 `B_in` 相与，得到一个中间结果 `Temp`。等 `Temp` 被完全赋值后，才开始执行下一个语句：将 `Temp` 的新值与 `C_in` 相或，得到 `D_out` 的值。我们也许可以感受到，这个 `always` 语句非常精确地模拟了上图中电路的行为。

另外，有一点会使得初学者产生较大的疑问。为什么 Verilog 规定只有寄存器（`register`）类型的变量才能够在过程赋值语句中被赋值呢？有时候在 Verilog 中定义的寄存器变量，在综合时并不一定映射成一个实在的触发器硬件。比如在以上的例子中，`Temp` 和 `D_out` 被定义成 `reg` 变量，而综合结果它们却还是组合逻辑，并不是存储单元。

在 Verilog  语言中，寄存器变量的特点是**需要在仿真运行器件上保持它的值**，也就是说，这个变量在仿真时需要占据内存空间。

在以上的 `always` 实例中，`always` 语句块只对 `A_in` 等 3 个输入变化敏感。如果没有这 3 个变量的变化事件，`Temp` 和 `D_out` 变量将需要保存其值，因此它们必须被定义为寄存器类型变量。但是，它们在综合之后，并不对应硬件锁存器或者触发器。

### 3.2.2. 非阻塞赋值

非阻寒赋值的语法如下：

``` verilog
寄存器变量 <= 表达式;
```

它的特点是在执行该语句时，首先计算右边的表达式，然后并不立刻对左边的变量赋值。由于这个赋值操作在当前仿真时间事件队列中的优先级比较低，因此**将赋值推迟到当前仿真时刻的后期运行**。关于具体非阻塞赋值的右式计算和左式更新是何时完成的，请参考后续内容。

<!-- [comment]:<> (第8章中“分层事件队列”部分内容。) -->

与阻塞赋值不同的是，如果多个非阻塞赋值语句顺序出现在 `begin ... end` 语句中，前面语句的执行，并不会阻塞后面语句的执行。前面语句的计算完成，还没有赋值时，就会执行下一句的右边表达式计算。例如 `begin m <= n; n <= m; end` 语句中，最后的结果是将m与n值互换了。

如果想要采用如下代码来描述[前述组合逻辑电路](#CombinationalLogicDemo)的功能：

``` verilog
wire A_in, B_in, C_in;
reg Temp, D_out;
...
always @(A_in or B_in or C_in) begin
    Temp <= A_in & B_in;            // 非阻塞赋值
    D_out <= Temp | C_in;           // 非阻塞赋值
end
```

如果使用仿真工具去仿真这段代码，就会发现它的功能并不是所想要的功能。比如当 `A_in` 发生变化，执行 `always` 语句，其中 `Temp <= A_in & B_in;` 这句话并没有立刻对 `Temp` 赋值，而是放在当前仿真时刻的后期才开始执行 `Temp` 的更新。这样，当执行 `D_out <=Temp | C_in;` 表达式的右式计算事件时，`Temp` 的值还是
旧的值，因此这时 `D_out` 并不会发生变化。

大家也许可以尝试将如上的代码用综合工具综合一下，可能同样会得到[前述组合逻辑电路](#CombinationalLogicDemo)的情况。这是由于一些综合工具可以容忍用户的这些代码缺陷。这就造成了 RTL 仿真和综合的结果不一致的现象。

为什么会造成这个现象呢？因为 RTL 的仿真器严格按照 Verilog 的仿真语义执行 RTL 的仿真过程，而综合工具通常只是根据用户的代码推断设计者的意图，然后生成相应的电路结构。因此，综合的过程有一定的主观推断性，并不严格遵守 Verilog 的语义，不同的综合工具判决标准也不一样。

这种情况是每一个设计都应该尽量避免的，因为仿真和综合结果不一致说明源代码中很可能有隐患，不符合 Verilog 的语义，会错过许多 bug，增加设计的不稳定性。

所以保证仿真器和综合器都能正确理解设计的代码，是非常关键的。

{% note info %}

**通常利用非阻塞赋值的特点来对时序逻辑进行建模。**

{% endnote %}

代码如下：

``` verilog
always @(posedge clk) begin
    q1 <= d;
    q2 <= q1;
    q3 <= q2;
end
```

其实现结果如[图 3-8](#fig.3-8)：

<a id="fig.3-8"></a>

![图 3-8 三级流水的寄存器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-080-ThreeFlowStateRegister.png!sign){width=600px}

正是利用了语句之间的非阻塞性，才实现了这个 3 级流水线的逻辑功能。

### 3.2.3. 过程连续赋值

在 Verilog 语言中，还有一种过程赋值语句叫做“过程连续赋值”，它们也是出现在 `always` 和 `initial` 语句块中的。

过程连续赋值主要有两种：

- `assign` 与 `deassign`：在过程语句块中对寄存器变量强制赋值和放开；
- `force` 与 `release`：在过程语句块中对寄存器和线网进行强制赋值和放开。

例如，在下面的代码实例中用 `assign` 和 `deassign` 描述了一个带异步清零端的 D 触发器。

``` verilog DEF.V
module DEF(D, clr, clk, Q);

input D, clr, clk;
output Q;

reg Q;

always @(clr) begin
    if (!clr)
        assign Q = 0;       // D 的值对 Q 无效, 将 Q 强制为 0
    else
        deassign Q;         // 将强制的 Q 值放开
end

always @(negedge clk)
    Q = D;

endmodule
```

这里不再对过程连续赋值进行过多阐述，有兴趣可以参考其他文献。

<!-- [comment]:<> (在第8章中的8．6“再谈阻塞与非阻塞赋值”一节中，通过一个实例，将再次深入阐述非阻塞和阻塞赋值语句的本质。) -->

## 3.3. 语句组

语句组是两条以上语句的组合，它们看起来像一个独立的语句。语句组也是出现在 `initial` 和 `always` 的过程块中的。

根据其中语句的执行顺序，语句组可以分为“顺序语句组"和“并行语句组”两种。

### 3.3.1. 顺序语句组 `begin ... end`

在顺序语句组中，其中的语句是一条一条顺序执行的。比如下面的语句：

``` verilog
always @(A_in or B_in or C_in) begin
    Temp = A_in & B_in;                 // 阻塞赋值
    D_out = Temp | C_in;                // 阻塞赋值
end
```

首先执行第一句，将 `A_in` 和 `B_in` 相与，然后将结果赋给 `Temp` 变量；再执行第一句，将新的 `Temp` 值 `C_in` 相或，结果立刻赋给 `D_out`。

当然，这里是同时利用了 `begin ... end` 语句组和阻塞赋值的特点，才实现了用户想要的逻辑功能。

再比如要产生一个值序列：

``` verilog
initial
begin
    DataBin = 0;
    #6 DataBin = 0;
    #4 DataBin = 1;
    #2 DataBin = 0;
end
```

由于语句是顺序执行的，产生的波形如[图 3-9](#fig.3-9) 所示。

<a id="fig.3-9"></a>

![图 3-9 顺序语句块的执行波形](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-090-SequentialWave.png!sign){width=500px}

### 3.3.2. 并行语句组 `fork ... join`

在`fork ... join` 语句组中，语句是并行执行的。将上一小节的代码改写如下：

``` verilog
initial
fork
    DataBin = 0;
    #6 DataBin = 0;
    #4 DataBin = 1;
    #2 DataBin = 0;
join
```

由于其中的所有语句并行执行，也就是以上 4 条语句都是从 0 时刻开始同时执行的，产生的波形如[图 3-10](#fig.3-10)。

<a id="fig.3-10"></a>

![图 3-10 并行语句块的执行波形](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-100-ParallelWave.png!sign){width=500px}

### 3.3.3. 语句组的标识符

语句组可以有标识符，也可以没有。

**当一个语句组有标识符时，在语句组内部可以定义局部变量，而不会传递到语句组的外部**。然而，在仿真语义上，这个变量是静态变量，它的值在整个仿真运行周期中是不变的，但是不会与其他语句组中同一个名称的变量发生冲突。例如：

``` verilog
integer i;              // always 语句以外的 i 变量
always @(...)
begin: SORT
    // ...
    integer i;          // 语句组内部的 i 变量
    for (i = 0;i <= 7; i = i + 1)
    // ...
end
```

在 `always` 以外的i变量和 `always` 里面定义的i变量属于**两个不同的变量，并不冲突**。它们在仿真的时候将占用两块不同的内存，类似于 C 语言中的静态局部变量。

## 3.4. 高级编程语句<a id="toc.3.4"></a>

### 3.4.1. 为什么需要编程语句

Verilog 作为硬件描述语言，最重要的特性就是其设计层次比较高，不仅停留在晶体管级和门级，而是可以在更高的层次如 RTL 级甚至是行为级描述硬件系统的行为，或者编写测试激励。

为了达到提高描述能力、提高抽象层次，Verilog 语言从 C 语言等编程语言中借鉴了一语句，同时也创造了一些语句，例如 `if`、`case`、`while`、`for`、`repeat` 和 `forever` 等。这些语句被称为高级编程语句。有了这些语句 Verilog 才可以描述比较复杂的电路行为。

编程语句只能出现在 `initial` 和 `always` 的过程块中。编程语句中可以嵌套其他的语句，比如过程赋值语句或者其他编程语句。

高级编程语分为3大类：

- `if ... else` 语句；
- `case` 语句；
- 循环语句：`forever`、`repeat`、`while`、`for`。

下面将逐一介绍。

### 3.4.2. `if ... else` 语句

`if` 语句后面跟语句或语句组（`begin ... end` 或 `fork ... join`)。常和 `else` 搭配来实现不同条件的各种情况。`if` 也可以单独使用，没有 `else` 配合。

现考虑以下代码：

``` verilog
always @(sel_a or sel_b or a or b or c) begin
    if (sel_a)
        q = a;
    else if (sel_b)
        q = b;
    else
        q = c;
end
```

它要实现的逻辑如[图 3-11](#fig.3-11)。

<a id="fig.3-11"></a>

![图 3-11 `if ... else` 语句](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-110-IfElseStatement.png!sign){width=600px}

在 `if ... else` 语句中，条件是从上到下逐条检查的。因此，当满足一个条件时，就执行其后的语句，跳过 `else` 后面的语句、当所有条件都不满足，便执行最后一条 `else` 后面的语句。因此 `if ... else` 语句实际上是有优先级顺序的。

{% note info %}

实际在上例中使用了 `if ... else` 优先级编码的特点，`sel_a` 的判断优先级最高，因此在逻辑中的级数要明显少一些，参考上图。**如果 `sel_a` 为关键路径的话，就可以利用这样的优先级编码提高设计的性能**。

{% endnote %}

在使用 `if ... else` 语句时，**尤其是用在组合逻辑中，需要注意不要引入 Latch 电路**。先来看如下的代码：

``` verilog
always @(sel or a or b or c) begin
    if (sel == 2'b00)
        q = a;
    else if (sel == 2'b01)
        q = b;
    else if (sel == 2'b10)
        q = c;
end
```

由于，最后一个条件 `sel == 2'b11` 的语句没有被显式地写出，言下之意是，当 `sel` 为 `2'b11` 时，`q` 值需要保持不变。这个代码在综合时自然就会产生锁存器，如[图 3-12](#fig.3-12)。

<a id="fig.3-12"></a>

![图 3-12 产生锁存器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-120-CombinationalLogicwithLatch.png!sign){width=800px}

{% note warning %}

**锁存器在数字同步逻辑设计中应该尽量避免。因为锁存器容易引起竞争冒险，同时静态时序分析工具也不好分析穿过锁存器的路径。**

{% endnote %}

在下面的代码中，己经明确写出：当 `se` 为 `2'b11` 时，`q` 值不关心，赋值为 `x`。

``` verilog
always @(sel or a or b or c) begin
    if (sel == 2'b00)
        q = a;
    else if (sel == 2'b01)
        q = b;
    else if (sel == 2'b10)
        q = c;
    else
        q = 1'bx;       // 当 sel 为 2'b11 时, q 值不关心
end
```

既然不关心 `sel` 为 `2'b11` 时 `q` 的值，那么有的综合工具就顺手将 `sel` 等于 `2'b11` 时 `q` 的值也赋值为 `c`，这样就避免了锁存器的产生。实现电路如[图 3-13](#fig.3-13)。

<a id="fig.3-13"></a>

![图 3-13 无锁存器的组合逻辑电路](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-130-CombinationalLogicwithoutLatch.png!sign){width=800px}

然而，在描述时序逻辑时，通常将利用 `if` 语句的隐式条件对带时钟使能的 D 触发器建模。代码如下：

``` verilog
always @(posedge clk or negedge rst_n) begin
    if (~rst_n)
        sum <= 0;
    else if (en)
        sum <= a + b;
end
```

以上语句表示在时钟正沿来临时，如果 `en` 为 1，则将 `a + b` 的值付给 `sum`。言下之意：如果 `en` 为 0，那么 `sum` 保持原值不变。因此，这里综合工具会把代码综合成一个时钟使能的寄存器，如[图 3-14](#fig.3-14) 所示。

<a id="fig.3-14"></a>

![图 3-14 带时钟使能的 D 触发器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-140-DFlipFlopClock.png!sign){width=800px}

其中，`en` 信号时 D 触发器的时钟使能端，`rst_n` 是 D 触发器的异步清零信号。

### 3.4.3. `case` 语句

`case` 语句的功能同正一类似，但是又有很大的不同。它后面也可以跟语句或语句组（`begin ... end` 或 `fork ... join`）。

如下例：

``` verilog
always @(sel or a or b or c) begin
    case(sel)
        2'b00: q = a;
        2'b01: q = b;
        2'b10: q = c;
        default: q = 1'bx；
    endcase
end
```

在 `case` 语句中，`default:` 一条描述了所有没有明确说明的其他可能情况。比如，这里的 `default` 就包含了 `sel` 为 `2'b11`、`2'bzz` 和 `2'bxx` 等情况。

以上的代码将实现如上的[无锁存器的组合逻辑电路](#fig.3-13)。

与 `if ... else` 语句不同的是，在 `case` 语句中，所有被判断的分支条件都具有一样的优先级。

与 `if ... else` 类似的是，语句同样需要考虑所有可能的情况，否则将会产生不想要的锁存器。如果将代码改为：

``` verilog
always @(sel or a or b or c) begin
    case(sel)
        2'b00: q = a;
        2'b01: q = b;
        2'b10: q = c;
    endcase
end
```

这样，将会产生与如上的[带锁存器的组合逻辑电路](#fig.3-12)。这是设计者不愿意看到的。

对于 `case` 语句，有两个派生语句，即 `casez` 及 `casex`。

`casez` 语句将分支条件中所有的 `z` 看作“不关心"的值，而不看作任何逻辑值。条件中的 `z` 可以改写为 `?`，如下例：

``` verilog
casez (encoder)
    4'b1???: high_lvl = 3;
    4'b01??: high_lvl = 2;
    4'b001?: high_lvl = 1;
    4'b0001: high_lvl = 0;
    default: high_lvl = 0;
endcase
```

这里，如果 `encoder` 为 `4b'1zzz` 则 `high_lvl` 取值为 3。

`casex` 语句将分支条件中所有的 `x` 和 `z` 看作“不关心”的值，而不看作任何逻辑值。如下例：

``` verilog
casex (encoder)
    4'b1xxx: high_lvl = 3;
    4'b01xx: high_lvl = 2;
    4'b001x: high_lvl = 1;
    4'b0001: high_lvl = 0;
    default: high_lvl = 0;
endcase
```

这里，如果 `encoder` 为 `4'b1xzx`，则 `high-lvl` 取值为 3。

### 3.4.4. 循环语句

循环语句一般用于重复的操作。

循环语句后面可以跟语句或语句组（`begin ... end` 或 `fork ... join`)。

1. `forever` 循环：永远执行

    ``` verilog
    initial begin
        clk = 0;
        forever #25 clk = ~clk;
    end
    ```

    以上语句产生了一个周期为 50 个时间单位的时钟。

2. `repeat` 循环：执行固定的次数

    ``` verilog
    if (rotate == 1)
    repeat (8)
        begin
            tmp = data[15];
            data = {data<<1, temp};
        end
    ```

    以上语句中，当 `rotate` 为 1 时，重复对 `data` 数据做 8 次循环左移。

3. `while` 循环：当表达式为真时执行

    ``` verilog
    initial begin
        count = 0;
        while (count < 101) begin
            $display("Count = %d", count);
            count = count + 1;
        end
    end
    ```

    在 `while` 语句中，只要后面的条件满足，就持续执行该语句，直到条件不满足，跳出循环。这里，将 `count` 从 0 递增到 101，逐步打印出来。

4. `for` 循环：从初始值开始，如果表达式为真就执行

    ``` verilog
    integer i;          // 为 for 循环声明索引
    always @(inp or cnt) begin
        result[7:4] = 0;
        result[3:0] = inp;
        if (cnt == 1) begin
            for (i = 4; i <= 7; i = i + 1)
                result[i] = result[i-4];
            result[3:0] = 0;
        end
    end
    ```

    `for` 语句开始执行直到 `i` 大于 7，跳出循环。如上代码实现了一个位的左移器。

简单介绍了 Verilog 中的高级编程语句，只要对 C 语言有一定基础，就能迅速掌握其中的用法。

# 4. 结构化描述

结构化描述就是在设计中**实例化已有的功能模块**，这些功能模块包括：门原语、用户自定义原语（UDP）、其他模块（module）。以下是结构化描述的 3 种实例类型：实例化其他模块、实例化门及实例化UDP。

下例是由两个半加器组成的全加器的模型，其中所有模块都采用了结构化描述方法。

``` verilog HalfAdd.v
module HalfAdd(X, Y, sum, C_out);                   // 半加器模块

input X;
input Y;
output sum;
output C_out;

// assign sum = X ^ Y;
// assign C_out = X & Y;
xor u_xor(sum, X, Y);                               // 门级原语实例
and u_and(C_out, X, Y);                             // 门级原语实例

endmodule
```

``` verilog FullAdd.v
module FullAdd(X, Y, C_in, sum, C_out);             // 全加器模块

input X;
input Y;
input C_in;
output sum;
output C_out;

wire HalfAdd_A_sum;
wire HalfAdd_A_Cout;
wire HalfAdd_B_Cout;

// assign C_out = HalfAdd_A_Cout | HalfAdd_B_Cout;
or u_or(C_out, HalfAdd_A_Cout, HalfAdd_B_Cout);     // 门级原语实例

HalfAdd u_HalfAdd_A(                                // 半加器实例 A
    .X      (C_in),
    .Y      (Y),
    .sum    (HalfAdd_A_sum),
    .C_out  (HalfAdd_A_Cout)
    );

HalfAdd u_HalfAdd_B(                                // 半加器实例 B
    .X      (C_in),
    .Y      (HalfAdd_A_sum),
    .sum    (sum),
    .C_out  (HalfAdd_B_Cout)
    );

endmodule
```

上例已将[原半加器代码](#HalfAdder)中的语句改为门原语的实例化。在全加器的模块中，有两个半加器模块的实例和一个 `or` 门原语的实例。

在以上代码中，实例化的 `or` 门原语是 Verilog 语言自带的电路，实例化的半加器模块则是用户自己设计的模块。

实现电路如[图 3-15](#fig.3-15)。

<a id="fig.3-15"></a>

![图 3-15 全加器](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-150-FullAdder.png!sign){width=800px}

以上内容提到了实例化基本门和其他模块。

关于 UDP，它是用户自定义的原语，由于应用不广泛，其功能基本上可以由模块替代，因此不再介绍。

## 4.1. 实例化模块的方法

在结构化描述中，需要将模块实例与外部信号相连接。下面谈谈模块实例的端口连接规则。

先看一个模块内部输入/输出/双向端口的内部属性：

- **`input`：在模块内部默认是一个线网类型；**
- **`output`：在模块内部是一个寄存器（在过程赋值语句中被赋值）或者线网类型；**
- **`inout`：在模块内部默认是一个线网类型，是双向信号，一般定义为 `tri`。**

当这个模块被实例化时，与之相连的信号类型如下：

- **与模块 `input` 端口相连：可以是一个线网或者寄存器；**
- **与模块 `output` 端口相连：一定是驱动到一个线网；**
- **与模块 `inout` 端口相连：输入时从一个线网驱动来，输出时驱动到一个线网。**

初学者经常犯这样一个错误，将寄存器变量驱动 `inout` 端口，导致编译出错。因为，只有线网类型可以驱动 `inout` 端口。

[图 3-16](#fig.3-16) 清楚地了模块端口在内部和外部的类型。

<a id="fig.3-16"></a>

![图 3-16 模块实例端口连接规则](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-160-ModuleConnection.png!sign){width=800px}

下面举例说明模块内部和外部的端口：

``` verilog
HalfAdd u_HalfAdd_A(                                // 半加器实例 A
    .X      (C_in),
    .Y      (Y),
    .sum    (HalfAdd_A_sum),
    .C_out  (HalfAdd_A_Cout)
    );
```

点 `.` 后面紧跟的信号是 `HalfAdd` 内的端口名称，而括号中的信号是上一层 `FullAdd` 模块中的驱动源或被驱动信号。

模块实例的端口对应方式有以下两种：名称对应及位置对应。

1. 名称对应

    所谓名称对应是指：将模块实例外部的信号直接对应于模块的端口名称。在实例化 `HalfAdd` 时就是采用了名称对应的方法。

    在这种端口对应方式下，**端口对应的顺序可以是任意的**。在没有对应外部信号的时候，可以将端口后面的括号留空。如下：

    ``` verilog
    模块 实例名称(
        .模块端口名称           (实例外部信号),
        .模块端口名称           (实例外部信号),
        .模块端口名称           (),             //无对应信号
        ...);
    ```

2. 位置对应

    位置对应方式就是在模块实例化的时候外部的信号需要按照该模块端口声明的顺序一一对应。例如：

    ``` verilog
    module HalfAdd(X, Y, sum, C_out);
    // ...
    endmodule
    ```

    在实例化该模块的时候，可以使用：

    ``` verilog
    HaIfAdd u_HalfAdd(E_X, E_Y, E_sum, E_C_out);
    ```

    其中 `E_X`、`E_Y`、`E_sum` 和 `E_C_out` 分别对应 `HalfAdd` 的端口 `X`、`Y`、`sum` 和 `C_out`，严格按照`HalfAdd` 模块的端口位置顺序。如果在没有对应外部信号的时候，就需要**将位置留空**。例如：

    ``` verilog
    HaIfAdd u_HalfAdd(E_X, E_Y, , E_C_out);
    ```

    其中 `E_X`、`E_Y` 和 `E_C_out` 分别对应 `HaIfAdd` 的端口 `X`、`Y` 和 `C_out`，模块端口 `sum` 没有对应的外部信号。

## 4.2. 参数

在本小节中将讨论可参数化的模块。

### 4.2.1. 参数定义

`module` 中的参数一般是定义其中常量的工具。

如下的代码中定义了半加器的“与门”和“异或门"的延时分别为 2 和 4 个时间单位：

``` verilog
module half_adder(co, sum, a, b)

input a, b;
output co, sum;

parameter and_delay = 2;
parameter xor_delay = 4;

and #and_delay u1(co, a, b);
xor #xor_delay u2(sum, a, b);
endmodule
```

实际上，在 Verilog 语言中，**当实例化模块时用户可以修改模块中的参数**，用来实现不同的特性。这个定制过程是通过“新参数直接带入”或“参数重定义”完成的。

Verilog 模块参数的这一特性非常有用，用户可以定义一个通用的模块，其具有缺省的参数值，然后通过改变参数来做成不同的实例模块。

例如，可以设计一个通用的 RAM 模块，将其位宽和地址深度定义为参数。在具体使用时，如果需要用到不同的位宽和深度，用户则可以通过改变模块中的参数实现。

### 4.2.2. 参数的定制

参数的用户定制有两种方法：

1. 通过 `defparam` 关键字对模块中的参数重新定义；
2. 参数直接在实例化模块时代入。

有意思的是，两家最大的 PLD 供应商 Altera 和 Xilinx 的通用模块定制恰好分别采用了这两种方法。

- 比如 Altera 的 Quartus II 开发环境中，用 MegaWiard 工具定制一个宽 8 深 32 位的单口 RAM 时，将产生如下的代码：

    ``` verilog ran_w8_d32.v
    module ran_w8_d32(address, clock, data, wren, q);

    input [4:0] address;
    input       clock;
    input [7:0] data;
    input       wren;
    output      q;

    altsyncram altsyncram_component(
        .wren_a        (wren),
        .clock0        (clock),
        .address_a     (address),
        .data_a        (data),
        .q_a           (q),
        .aclr0         (1'b0),
        .aclr1         (1'b0),
        .q_b           (),
        .clocken1      (1'b1),
        .clocken0      (1'b0),
        .data_b        (1'b1),
        .rden_b        (1'b1),
        .address_b     (1'b1),
        .wren_b        (1'b0),
        .byteena_b     (1'b0),
        .addressstall_a(1'b0),
        .byteena_a     (1'b1),
        .addressstall_b(1'b0),
        .clocken1      (1'b1)
        );

    defparam
        altsyncram_component.clock_enable_input_a = "BYPASS",
        altsyncram_component.clock_enable_output_a = "BYPASS",
        altsyncram_component.intended_device_family = "Stratix II",
        altsyncram_component.lpm_hint = "ENABLE_RUNTIME_MOD = NO",
        altsyncram_component.lpm_type = "altsyncram",
        altsyncram_component.numwords_a = 32,               // 32 个字
        altsyncram_component.operation_mode = "SINGLE_PORT",
        altsyncram_component.outdata_aclr_a = "NONE",
        altsyncram_component.outdata_reg_a = "UNREGISTERED",
        altsyncram_component.power_up_uninitialized = "FALSE",
        altsyncram_component.ram_block_type = "M4K",
        altsyncram_component.widthad_a = 5,                 // 5 位地址
        altsyncram_component.width_a = 8,                   // 8 位宽的数据
        altsyncram_component.width_byteena_a = 1;

    endmodule
    ```

    其中，`altsyncram` 是 Altera 的块 RAM 的通用模型。它可以在 Altera 的仿真文件“alteramf.v”中找到。

    `altsyncram-component` 是实例名称，`.` 后面是参数的名称，定义的参数值，有的是字符串，有的是整数。

    在 `defparam` 关键字后面的是参数重定义的语句：

    ``` verilog
    altsyncram_component.width_a = 8,               // 8 位宽的数据
    ```

    以上就是将 `altsyncram-component` 中的参数 `width_a` 重新定义为 8。

    使用 `defparam` 的方法重新定义参数时，可以根据需要对部分的参数重新定义，其他的会保留模块的缺省值。

- 用 Xilinx 设计工具 ISE 中的 CORE Generator 产生一个单端口 8 位宽、32 位深的 RAM。注意观察，其中RAM的参数是如何带入的：

    ``` verilog ram_w8_d32.v
    module ram_w8_d32(addr, clk, din, dout, we)

    input [4:O] addr;
    input       clk;
    input [7:O] din.
    output [7:0] dout;
    input we;

    // synopsys translate_off
    BLKMEMSP_V6_1 #(
        5,                  // c_addr_width
        "0",                // c_default_data
        32,                 // c_depth
        0,                  // c_enable_rlocs
        1,                  // c_has_default_data
        1,                  // c_has_din
        0,                  // c_has_en
        0,                  // c_has_limit_data_pitch
        0,                  // c_has_nd
        0,                  // c_has_rdy
        0,                  // c_has_rfd
        0,                  // c_has_sinit
        1,                  // c_has_we
        18,                 // c_limit_data_pitch
        "mif_file_16_1",    // c_em_init_file
        0,                  // c_pipe_stages
        0,                  // c_reg_inputs
        "0",                // c_sinit_value
        8,                  // c_width
        0,                  // c_write_mode
        "0",                // c_ybottom_addr
        1,                  // c_yclk_is_rising
        1,                  // c_yen_is_high
        "hierarchy1",       // c_hierarchy
        0,                  // c_ymake_bmm
        "32kx1",            // c_yprimitive_type
        1,                  // c_ysinit_is_high
        "1024",             // c_ytop_addr
        0,                  // c_yuse_single_primitive
        1,                  // c_ywe_is_high
        1                   // c_yydisable_warnings
        )

    inst(
        .ADDR (addr),
        .CLK  (clk),
        .DIN  (din),
        .DOUT (dout),
        .WE   (we),
        .EN   (),
        .ND   (),
        .RFD  (),
        .RDY  (),
        .SINIT()
        );
    // synopsys translate_on
    endmodule
    ```

    其中 `BLKMEMSP_V6_1` 是 Xilinx 的块状RAM的通用模型。`inst` 是用户实例名称。用户需要在通用RAM类型的基础上，通过参数带入实现。采用 `#(...)` 方法来实现参数逐个覆盖。

    使用上面这种参数直接带入法时，要注意一点，所有的参数都需要全部按顺序列出来，不能遗漏，也不能颠倒顺序，否则就容易对应不上。

# 5. 设计层次<a id=toc.5></a>

在前面几节中，介绍了 Verilog 的描述方式。

Verilog 语言是一种强大的硬件描述语言，可以支持多个设计层次。在这里将进行简单的介绍。

## 5.1. 系统级和行为级<a id=toc.5.1></a>

Verilog 语言作为一种用户工具，提供给用户许多描述硬件的手段，如前面所述：数据流描述、行为描述（`always` 和 `initial` 语句）、结构化描述。同时，不同用户可以根据自己的需要，在不同抽象层次上对硬件进行描述。有如下设计层次金字塔供参考。

<a id="fig.3-17"></a>

![图 3-17 设计层次金字塔](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-170-DesignLevelPyramid.png!sign){width=600px}

下面简单介绍几种不同角色的工作特点，以及它们所处的设计层次：

1. 系统构架师：在目前业界主流的设计方法学中，系统构架师（System Architect）通常用高级语言，如 SystemC，来描述一个系统的规格，仿真整个系统的功能和性能等。这种早期的设计和探索往往不涉及到具体的实现细节，甚至于软件和硬件的划分都没有开始。系统构架师也可以采用 Verilog 来描述系统的功能，它们往往不考虑硬件实现的细节。因而称这种设计层次为系统级或算法级。

2. 逻辑设计工程师：他们利用前面所讲的 Verilog 各种描述手段，设计 RTL 级的代码，精确到时钟周期。逻辑设计工程师的代码，通过综合工具的综合，可以转换为 Verilog 的门级网表，其中所有的功能块都是由基本的门单元组成的。

3. 物理设计工程师：他们将这些门级网表进行布局和布线，做成实际的芯片。

4. 验证工程师：他们负责对设计的电路进行验证，他们编写的代码主要是用来产生激励，这些激励大部分需要的抽象层次更高，以使仿真的效率更高。然后在工具中对电路进行仿真，检查响应结果。这些代码不会实现为具体癭件，有些并不需要精确到时钟周期，而只是在软件的仿真工具中运行，实现一定的功能即可，称这种描述层次为行为级。

这里的行为级描述，不同于 [3. 行为描述](#toc.3)中所述的“行为描述方式”。这里特指一种描述的抽象层次。

下面举一个实例说明 RTL 级和行为级的区别，要实现的状态机一部分如[图 3-18](#fig.3-18) 所示。=

<a id="fig.3-18"></a>

![一个简单的握手协议状态机](https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-30-josh-verilog-part-3/2020-11-30-josh-verilog-part-3-180-ASimpleHandshakingStateMachine.png!sign){width=500px}

上图中只有两个状态，当在左边的状态时，`REQ` 输出 1；当在右边的状态时，`REQ` 输出 0。`ACK` 是状态机的输入信号，决定了状态的跳转。

如果是让一个 RTL 设计工程师来设计，他马上就会与硬件实现的细节联系起来，很可能就会用如下代码实现：

``` verilog
/************************
RTL 描述
************************/
reg [1:0] curr_sm;
reg [1:0] next_sm;              // 定义状态寄存器

parameter ...;
state0 = 2'b01;
state1 = 2'b10;

// state registers
always @(posedge clk or posedge rst) begin
    if (rst)
        curr_sm <= ...;
    else
        curr_sm <= next_sm;
end

// next state logic
always @(curr_sm or ACK) begin
    case(curr_sm)
        ...
        state0: begin
            REQ = 1;
            if (ACK == 1)
                next_sm = state1;
            else
                next_sm = state0;
        end
        state1: begin
            REQ = 0;
            if (ACK == 0)
                next_sm = ...;
            else
                next_sm = state1;
        end
    endcase
end
```

然而，如果让一个逻辑验证工程师来设计这个状态机，他不考虑硬件实现的细节，只需要在语义上满足要求即可。甚至可以不出现状态寄存器。如下例：

``` verilog
/************************
行为描述
************************/
initial begin
    // ...
    REQ = 1;
    wait (ACK == 1);
    REQ = 0;
    wait (ACK == 0);
    // ...
end
```

从上例中可以看出，逻辑验证工程师设计的方式十分简单，仅仅利用了 `begin ... end` 语句组的顺序执行特性，同时利用 `wait` 语句来实现状态的转移。这是典型的行为级设计风格，也是逻辑验证工程师们所追求的思维方式。

## 5.2. RTL 级

所谓寄存器传输级（RTL级），就是描述电路的时候，只需要关注寄存器本身，以及寄存器到寄存器之间的逻辑功能，而不用关心寄存器和组合逻辑的实现细节（具体用了多少逻辑门等）。

随着逻辑综合工具的兴起，工程师才可以从 RTL 级进行电路设计了，而不需要像传统设计方法一样从门级电路搭起。它们的 RTL 设计代码将直接通过逻辑综合工具，综合成门级的设计网表，通常是由基本的门单元组成的。逻辑综合是 EDA 流程的重要组成部分。

Verilog 设计电路最常用的设计层次就是 RTL 级。在 RTL 描述时，设计者需要关注寄存器的行为，其中保存着数据；同时需要关注寄存器和寄存器之间的组合逻辑功能，是否能满足功能需求和时序需求。RTL 级模型是严格精确到时钟周期的模型。

[Part 4 —— RTL 概念与常用 RTL 建模][]、[Part 5 —— RTL 设计与编码指导][]和[Part 6—— 如何写好状态机][]会重点介绍 RTL 设计的方法和技巧。

{% note info %}

RTL 级是 HDL 语言最重要的概念之一，RTL 级是综合器最常用的设计输入层次。目前使用 FPGA/CPLD 等可编程逻辑器件时，设计输入都为 RTL 级。这是因为门级输入过于繁琐；而对于行为级和系统级设计输入，很多综合器不支持，并容易产生综合歧义。

{% endnote %}

## 5.3. 门级

在 Verilog 语义中，使用一些基本的门原语可以直接描述电路的门级功能。例如：

``` verilog HalfAdd.v
module HalfAdd(X, Y, sum, C_out);                   // 半加器模块

input X;
input Y;
output sum;
output C_out;

xor u_xor(sum, X, Y);                               // 门级原语实例
and u_and(C_out, X, Y);                             // 门级原语实例

endmodule
```

其中直接调用了 `xor` 和 `and` 的两个 Verilog 门原语。

门级设计就是指在逻辑门一级将电路搭出来。特别是对于大设计来说，这种设计非常耗时、效率低下，同时容易出错。但是对于一些逻辑容量较小，性能和面积要求非常高的设计，有时还采用门级设计，以满足一些特殊的需求。

门级设计类似于软件中的汇编语言设计，非常精确，但是耗时耗力。

## 5.4. 晶体管级

逻辑门是由一个个晶体管组成的。在 Verilog 语言中，有用于直接描述 NMOS 和 PMOS 的原语。这里不过多叙述，有兴趣可查阅相关资料。

## 5.5. 混合描述

Verilog 支持不同设计层次的混描述。

实际上，这些描述层次之间没有严格的界限。这里只是想将概念描述清楚，这一点对初学者非常重要。

# 参考文献

EDA 先锋工作室. _轻松成为设计高手——Verilog HDL 实用精解._ 北京航空航天大学出版社, 2012.

[Part 2——Verilog 语言基础]: https://josh-gao.top/posts/fd2ca242.html
[Part 2——Verilog 语言基础的 2.2 3 种描述方法]: https://josh-gao.top/posts/fd2ca242.html#toc2.2
[Part 2——Verilog 语言基础的 7.4 驱动和赋值]: https://josh-gao.top/posts/fd2ca242.html#toc7.4
[Part 4 —— RTL 概念与常用 RTL 建模]: https://josh-gao.top/posts/ecb88422.html
[Part 5 —— RTL 设计与编码指导]: https://josh-gao.top/posts/53b8b42e.html
[Part 6—— 如何写好状态机]: https://josh-gao.top/posts/d860a67e.html

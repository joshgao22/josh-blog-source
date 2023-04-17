---
title: Josh 的学习笔记之 Verilog（Part 4 —— RTL 概念与常用 RTL 建模）
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
tags:
  - Verilog
  - HDL
  - 非阻塞赋值
  - 阻塞赋值
  - 组合逻辑
  - 三态
  - 同步复位
  - 异步复位
  - 条件语句
categories:
  - [Josh 的学习笔记, Verilog]
abbrlink: ecb88422
date: 2020-12-02 01:05:32
description: Verilog HDL 的基本功能之一是描述可综合的硬件电路。如何合理使用 Verilog HDL 描述高性能的可综合电路是 Verilog 系列学习笔记的目的，也是后续要讨论的主要问题。本文介绍了 RTL 和综合的基本概念，通过常用 RTL 电路模型来对可综合的 RTL 级描述方式建立整体性的认识。力图通过范例，逐步引导初学者建立可综合 RTL 子集的概念。
---

# 1. RTL 和综合

从[《Part 3 —— 描述方式和设计层次》的第 5 小节](https://josh-gao.top/posts/fd117896.html#5-%E8%AE%BE%E8%AE%A1%E5%B1%82%E6%AC%A1)可知，HDL 语言是分层次的。

{% note default %}
最常用的层次概念有系统级（System Level）或功能模块级（Functional Model Level）、行为级（BehavioraI Level）、寄存器传输级（RTL，Register Transfer Level）和门级（Gate Level）等。
{% endnote %}

<!-- more -->

其中寄存器传输级（RTL，Register Transfer Level）指：**{% label @ 不关注寄存器和组合逻辑的细节（如使用了多少逻辑门，逻辑门之间的连接拓扑结构等），通过描述寄存器到寄存器之间的逻辑功能描述电路的 HDL 层次%}**。RTL 级是比门级更高的抽象层次，使用 RTL 级语言描述硬件电路一般比门级描述电路简单、高效得多。

RTL 级语言的最重要特性就是：{% label primary @RTL 级描述是可综合的描述层次 %}。

综合（Synthesize）指：**将 HDL 语言、原理图等设计输入翻译成由与、或、非门等基本逻辑单元组成的门级连接（网表），并根据设计目标与要求（约束条件）优化所生成的逻辑连接，输出门级网表文件**。RTL 级综合指将 RTL 级源代码翻译并优化为门级网表。

随着综合工具的不断智能化，使用RTL级语言描述硬件电路越来越方便。特别是在可编程逻辑器件（PLD，主要指 FPGA 和 CPLD）设计领域，最重要的代码设计层次就是 RTL 级。

# 2. RTL 级的基本要素和设计步骤

典型的 RTL 设计包含以下3个部分：

1. 时钟域描述

    描述设计所使用的所有时钟，时钟之间的主从与派生关系，时钟域之间的转换。

2. 时序逻辑描述（寄存器描述）

    根据时钟沿的变换，描述寄存器之间的数据传输方式。

3. 组合逻辑描述

    描述电平敏感信号的逻辑组合方式与逻辑功能。

RTL 描述中时序逻辑和组合逻辑的连接关系和拓扑结构决定了设计的性能，如何调整时序逻辑、组合逻辑的连接关系和拓扑结构以达到最佳的性能就是后面讨论的编码风格（Coding Style）的核心。

工程师们设计 RTL 级代码的顺序各种各样，推荐使用如下的设计步骤：

1. **功能定义与模块划分**

    根据系统功能的定义和模块划分准则（详见Part 5 第 4 节）划分各个功能模块。

2. **定义所有模炔的接口**

    首先清晰定义每个模块的接口，完成每个模块的信号列表。这种思路也和 Modular Design（模块化设计方法）一致，利于模块重用、调试、修改。

3. **设计的时钟域**

    根据设计的时钟复杂程度定义时钟之间的派生关系，分析设计中有哪些时钟域，是否存在异步时钟域之间的数据交换。对于 PLD 设计，还需确认全局时钟和是否使用 PLL/DLL 完成时钟的分频、倍频、移相等功能，哪些时钟使用全局资源布线，哪些时钟使用第二全局时钟资源布线。

    全局时钟资源的特点是：几乎没有 CIock Skew（时钟偏斜），有一定的 Clock Delay（时钟延迟），驱动能力最强；第二全局时钟的特点是：有较小的 CIock Skew，较小的 CIock DeIay，时钟驱动能力较强。

4. **考虑设计的关键路径**

    关键路径是指设计中时序要求最难以满足的路径。设计的时序要求主要体现在频率、建立时间、保持时间等时序指标上。在设计初期，设计者可以根据系统的频率要求，粗略地分析出设计的时序难点（如最高频率的路径，计数器的最低位，包含复杂组合逻辑的时序路径等），通过一些时序优化手段（如 Pipeline、Retiming、逻辑复制等，这些时序优化手段在 Part 5 第 7 节有详细介绍）**从代码上缓解设计的时序压力**，这种方法比单依靠综合与布局布线工具的自动优化有效得多。

5. **顶层设计**

    常用的设计方法有两种，一种是自顶而下的设计方法，即先描述设计的顶层，然后描述设计的每个子模块；另一种是由底向上的设计方法，即首先描述设计的子模块，最后定义设计的顶层。RTL 设计推荐使用自顶而下的设计方法，因为这种设计方法与模块规划的顺序一致，而且更利于进行 Modular Design（模块化设计方法），并行开展设计工作，提高模块重用率。

6. **FSM 设计**

    有限状态机（FSM）是逻辑设计的最重要内容之一。Part 6 主要讨论如何写好 FSM。

7. **时序逻辑设计**

    首先根据时钟域规划好寄存器组，然后描述各个寄存器组之间的数据传输方式。

8. **组合逻辑设计**

    一般来说，**大段的组合逻辑最好与时序逻辑分开描述**，这样更利于时序约束和时序分析，使综合器和布局布线器达到更好的优化效果。

# 3. 常用 RTL 级建模

## 3.1. 非阻塞赋值、阻塞赋值、连续赋值

有很多文章论述阻塞赋值和非阻塞赋值的概念，[《Part 3 —— 描述方式和设计层次》的 3.2 小节](https://josh-gao.top/posts/fd117896.html#3-2-%E8%BF%87%E7%A8%8B%E8%B5%8B%E5%80%BC%E8%AF%AD%E5%8F%A5)详细讨论了因不当使用阻塞赋值和非阻塞赋值引起的仿真与设计意图不一致。为了避免不当使用阻塞賦值和非阻塞赋值而造成的种种歧义和错误，这里推荐：

1. **对于时序逻辑，即 `always` 模块的敏感表为{% label primary @沿敏感信号 %}（多为时钟或复位的正沿或负沿），统一使用非阻塞賦值 `<=`**。例如：

    ```verilog // cnt1.v
    /******************************
    时序逻辑中使用非阻塞赋值。
    ******************************/

    module cnt1 (clock, cnt_out);

    input        clock;
    output [3:0] cnt_out;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [3:0] cnt_out;
    always @(posedge clock)
        cnt_out <= cnt_out + 1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

2. **对于 `always` 模块的敏感表为{% label primary @电平敏感 %}信号的组合逻辑，统一使用阻塞賦值**。例如：
<span id="cnt2.v"></span>

    ```verilog cnt2.v
    /******************************
    always 模块的敏感表为电平敏感信号的组合逻辑, 使用阻塞赋值。请注意, 此例
    "cnt_out_plus"虽然被指定为 reg 型, 但是实现时是纯组合逻辑。
    ******************************/

    module cnt2 (cnt_out, cnt_out_plus);

    input  [3:0] cnt_out;
    output [3:0] cnt_out_plus;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [3:0] cnt_out_plus;
    always @(cnt_out)
        cnt_out_plus = cnt_out + 1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

3. **对于 `assign` 关键字描述的{% label primary @组合逻辑 %}，称为连续赋值语句，统一使用 `=`，变量被定义为 `wire` 型信号**。例如：

    ```verilog cnt3.v
    /******************************
    assign 描述的组合逻辑。"cnt_out_plus"被定义为 wire 型信号。
    ******************************/

    module cnt3 (cnt_out, cnt_out_plus);

    input  [3:0] cnt_out;
    output [3:0] cnt_out_plus;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    wire [3:0] cnt_out_plus;
    assign cnt_out_plus = cnt_out + 1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

{% note warning %}
在 2 和 3 中，为什么不采用类似例 1 中的 `cnt_out <= cnt_out + 1;` 呢？因为，**这样会产生组合逻辑环**。组合逻辑环是同步时序逻辑设计的大忌，它会使得时序路径无法被工具所分析，**因不同的芯片的延时差异，而造成逻辑功能不稳定**。有些已经完成很久的设计，在换了芯片批次后，逻辑功能不正确，大多数都是由组合逻辑环造成的。
{% endnote %}

对于初学者而言，以上几点是必须遵循的法则，详细原因可以参考[《Part 3 —— 描述方式和设计层次》](https://josh-gao.top/posts/fd117896.html)和 Part 5 的论述。

## 3.2. 寄存器电路建模

寄存器和组合逻辑是数字逻辑电路的两大基本要素。寄存器一般和同步时序逻辑关联，其特点为仅当时钟的沿（上升沿或下降沿）到达时，才有可能发生输出的改变。根据实现目标不同，寄存器的建模结构略有不同，需要注意如下要点：

1. **寄存器信号声明**：**寄存器定义是 `reg` 型**。

    {% note info %}
    请注意，这个命题的反命题不一定成立。**某些信号虽然被定义为 `reg` 型，但是最终综合实现结果并不是寄存器**，如上述例子 [cnt2.v](#cnt2.v) 中 `cnt_out_plus` 虽然被指定为 `reg` 型，但是实现时是纯组合逻辑。**只有定义为 `reg` 型，且 `always` 的敏感表为 `posedge` 或 `negedge` 沿敏感操作时，该信号才是寄存器**。在[《Part 2 —— Verilog 语言基础》](https://josh-gao.top/posts/fd2ca242.html)和[《Part 3 —— 描述方式和设计层次》](https://josh-gao.top/posts/fd117896.html)也有相关内容描述这个问题。
    {% endnote %}

2. **时钟输入**：在每个时钟的正沿或负沿对数据进行处理。数据的正沿或负沿起作用，是在 `always` 的敏感表中通过 `posedge` 和 `negedge` 指定的。

    ```verilog cnt4.v
    /******************************
    指定寄存器的触发沿是时钟的下降沿。
    ******************************/

    module cnt4 (clock, cnt_out);

    input        clock;
    output [3:0] cnt_out;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [3:0] cnt_out;
    always @(negedge clock)
        cnt_out <= cnt_out + 1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

3. **异步复位/置位**：绝大多数目标器件的寄存器模型都包含异步复位/置位端。异步复位／置位是指无论时钟沿是否有效，当复位/置位信号有效沿到达时，复位/置位立即发挥功能。指定异步复位/置位时，**只需在 `always` 的敏感表中加入复位/置位信号的有效沿即可**。下例描述的异步复位电路是最常用的寄存器复位形式之一。

    ```verilog
    /******************************
    最常用的异步复位电路, 设异步复位信号"reset_"是低有效信号(也即下降沿开始复位)。用户可
    以在 begin/end 结构之间填入复杂的用户逻辑。
    ******************************/

    // reg_counter.v
    module reg_counter (clock, reset_, cnt_reg_s, cnt_reg_a);

    input        clock;
    input        reset_;
    output [3:0] cnt_reg_s;
    output [3:0] cnt_reg_a;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [3:0] cnt_reg_s;  //asynchronous reset
    always @(posedge clock or negedge reset_)
        if (!reset_)
            cnt_reg_s <= 4'b0000;
        else begin
            cnt_reg_s [2:0] <= cnt_reg_s [2:0] + 1;
            cnt_reg_s [3]   <= ~cnt_reg_s [3];
        end
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    reg [3:0] cnt_reg_a;  //synchronous reset
    always @(posedge clock)
        if (!reset_)
            cnt_reg_a <= 4'b0000;
        else begin
            cnt_reg_a [2:0] <= cnt_reg_a [2:0] + 1;
            cnt_reg_a [3]   <= ~cnt_reg_a [3];
        end

    endmodule
    ```

4. **同步复位/置位**：任何寄存器都可实现同步复位/置位功能。指定同步复位/置位时，**`always` 的敏感表中仅有时钟沿信号**，当同步复位/置位信号变化时，同步复位/置位并不立即发生，仅仅当时钟沿采到同步复位/置位的有效电平时，才会在时钟沿到达时刻进行复位/置位操作。

    ```verilog reg_counter.v
    /******************************
    常用的同步复位电路, 设异步复位信号"reset_"是低电平有效信号。请比较上例, 对比 always
    的敏感表。
    ******************************/

    module reg_counter (clock, reset_, cnt_reg_s, cnt_reg_a);

    input        clock;
    input        reset_;
    output [3:0] cnt_reg_s;
    output [3:0] cnt_reg_a;

    reg [3:0] cnt_reg_s;  //asynchronous reset
    always @ (posedge clock or negedge reset_)
        if (!reset_)
            cnt_reg_s <= 4'b0000;
        else begin
            cnt_reg_s [2:0] <= cnt_reg_s [2:0] + 1;
            cnt_reg_s [3]   <= ~cnt_reg_s [3];
        end

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [3:0] cnt_reg_a;  //synchronous reset
    always @ (posedge clock)
        if (!reset_)
            cnt_reg_a <= 4'b0000;
        else begin
            cnt_reg_a [2:0] <= cnt_reg_a [2:0] + 1;
            cnt_reg_a [3]   <= ~cnt_reg_a [3];
        end
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    > 关于同步复位和异步复位的优缺点，在 [3.9 小节](#0309) 有详细介绍。

5. **同时使用时钟上升和下降沿的问题**：有时因为数据采样或调整数据相位等需求，设计者会在一个 `always` 的敏感表中同时使用时钟的 `posedge` 和 `negedge`，或者在两个 `always` 的敏感表中分别使用时钟的 `posedge` 和 `negedge` 对某些寄存器电路操作。在这两种描述下，当时钟上沿或下沿到达时，该寄存器电路都会做相应的操作。**这个双沿电路往往可以等同于使用了原时钟的倍频时钟的单沿操作电路**。对于实现在 PLD 的设计而言，同时使用时钟的上、下沿往往是不推荐的，因为 PLD 内嵌的 PLL/DLL 和一些时钟电路往往只能对时钟的一个沿保证非常好的指标，而另个沿的抖动、偏斜、斜率等指标不见得非常优化，有时同时使用时钟的正负沿会因时钟的抖动、偏斜、占空比、斜率等问题造成一定的性能恶化。因此推荐的做法是：将原时钟通过 PLL/DLL 倍频，然后使用倍频时钟的单沿（如上升沿）进行操作。但是电路设计不可一概而论，如果必须使用时钟的双沿对同一个寄存器操作时，请设计者明确此时相当于使用了倍频时钟。

    ```verilog clock_edge.v
    /******************************
    某电路使用 50MHz 时钟双沿操作相当于使用同相位 100MHz 倍频时钟操作。
    ******************************/

    module clock_edge (clk_50M, clk_100M, rst_, cnt1, cnt2);

    input        clk_50M, clk_100M, rst_;
    output [3:0] cnt1, cnt2;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg    [3:0] cnt_temp1, cnt_temp2;
    wire   [3:0] cnt1;
    always @(posedge clk_50M or negedge rst_ )
        if (!rst_)
            cnt_temp1 <= 4'b0000;
        else
            cnt_temp1 <= cnt_temp2 + 1;

    always @(negedge clk_50M or negedge rst_ )
        if (!rst_)
            cnt_temp2 <= 4'b0000;
        else
            cnt_temp2 <= cnt_temp1 + 1;

    assign cnt1 = (clk_50M)? cnt_temp2 : cnt_temp1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    reg    [3:0] cnt2;
    always @(posedge clk_100M or negedge rst_ )
        if (!rst_)
            cnt2 <= 4'b0000;
        else
            cnt2 <= cnt2 + 1;

    endmodule
    ```

    这里相当于使用与 50MHz 时钟 `clk_50M` 同相位的 2 倍频时钟 `cIk_100M` 进行累加计算，等效描述如下：

    ```verilog
    reg    [3:0] cnt2;

    always @(posedge clk_100M or negedge rst_ )
        if (!rst_)
            cnt2 <= 4'b0000;
        else
            cnt2 <= cnt2 + 1;
    ```

    以上代码综合后，对应的电路结构如[图 4-1](#图4-1) 所示。

    <a name="图4-1"></a>
    <div align="center">
      <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-010-RegisterCircuitModeling-01.png!900px" alt="某双沿加法电路与等价倍频时钟单沿加法电路结构示意图"/>
    </div>

    RTL 仿真波形如[图 4-2](#图4-2) 所示。

    <a name="图4-2"></a>
    <div align="center">
      <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-020-RegisterCircuitModeling-02.png!900px" alt="某双沿加法电路与等价倍频时钟单沿加法电路仿真波型图"/>
    </div>

    通过这个例子可以看出 ，某些使用双沿操作的电路等价于使用倍频时钟的单沿电路，也就是说，这些电路使用了时钟的双沿触发寄存器，则相当于提高了设计频率，提高了设计要求的时序难度。对于这些电路，在附加时序约束和进行时序分析时一定要考虑充分。

## 3.3. 组合逻辑建模

组合逻辑是逻辑电路设计的另一重要组成部分。组合逻辑的特点是输出的变化仅仅和输入的电平相关，而与时钟沿无关。RTL 级常用组合逻辑的建模有两种：第一种是 `always` 模块的敏感表为电平敏感信号的电路；第二种是 `assign` 等关键字描述的组合逻辑电路。

1. **`always` 模块的敏感表为电平敏感信号的组合逻辑电路**

    这种形式描述组合逻辑电路应用非常广泛，如果不考虑代码的复杂性，几乎任何组合逻辑电路都可以用这种方式建模。`always` 模块的敏感表为所有判定条件和输入信号，**在使用这种结构描述组合逻辑时一定要将敏感表列写完整**。在 `always` 模块中可以使用 `if ... else`、`case`、`for` 循环等各种 RTL 关键字结构。下面以某组合逻辑译码电路为例，说明这种组合逻辑建模方式。如前所述，在这种组合逻辑 `always` 模块中．推荐使用阻塞赋值 `=`，虽然信号被定义为 `reg` 型，但是最终综合实现结果并不是寄存器，而是组合逻辑，定义为 `reg` 型仅是纯语法需要而已。

    ```verilog decode_cmb.v
    /******************************
    某组合逻辑译码电路。
    ******************************/

    module decode_cmb (addr, CS, cs1, cs2, cs3, cs4);

    input  [7:0]      addr;         // only the 2 MSB bits used
    input             CS;           // Low effect
    output cs1, cs2, cs3, cs4;      // Low effect

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg    cs1, cs2, cs3, cs4;
    parameter chip1_decode = 2'b00,
              chip2_decode = 2'b01,
              chip3_decode = 2'b10,
              chip4_decode = 2'b11;

    always @(CS or addr)
        if (CS)
            {cs1, cs2, cs3, cs4} = 4'b1111;
        else begin
            case (addr[7:6])
                chip1_decode: {cs1, cs2, cs3, cs4} = 4'b0111;
                chip2_decode: {cs1, cs2, cs3, cs4} = 4'b1011;
                chip3_decode: {cs1, cs2, cs3, cs4} = 4'b1101;
                chip4_decode: {cs1, cs2, cs3, cs4} = 4'b1110;
            endcase
        end
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    综合后对应的结构示意图如下[图 4-3](#图4-3) 所示。

    <a name="图4-3"></a>
    <div align="center">
      <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-030-CombinatorialLogictModeling.png!700px" alt="某组合逻辑译码电路的结构示意图"/>
    </div>

2. `assign` 等语句描述的组合逻辑电路

    这种形式描述组合逻辑电路适用于描述那些相对简单的组合逻辑。信号一般被定义为 `wire` 型，常用的 `assign` 结构除了直接赋值逻辑表达式外，还可以使用 `?` 语句。

    ```verilog decode_cmb2.v
    /******************************
    使用 assign 结构描述该组合逻辑译码电路。
    ******************************/

    module decode_cmb2 (addr, CS, cs1, cs2, cs3, cs4);

    input  [7:0]      addr;           // only the 2 MSB bits used
    input             CS;             // Low effect
    output cs1, cs2, cs3, cs4;        // Low effect

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    wire   cs1, cs2, cs3, cs4;
    parameter chip1_decode = 2'b00,
              chip2_decode = 2'b01,
              chip3_decode = 2'b10,
              chip4_decode = 2'b11;

    assign cs1 = (!CS && (addr[7:6] == chip1_decode))? 0 : 1 ;
    assign cs2 = (!CS && (addr[7:6] == chip2_decode))? 0 : 1 ;
    assign cs3 = (!CS && (addr[7:6] == chip3_decode))? 0 : 1 ;
    assign cs4 = (!CS && (addr[7:6] == chip4_decode))? 0 : 1 ;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    该描述方式实现的电路同[图 4-3 某组合逻辑译码电路的结构示意图](#图4-3)。

{% note %}
可以看到，对于简单的组合逻辑使用 `assign` 和 `?` 语句比较清晰。但是如果所描述的组合逻辑过于复杂，则需要很多条 `assign` 语句或者嵌套的 `?` 语句来描述，不易解读，此时推荐使用第一种组合逻辑建模方法。
{% endnote %}

## 3.4. 双线端口与三态信号建模

前面谈到所有的**双向总线（既作输出又作输出的总线）应该在顶层模块定义为三态信号，禁止在顶层以外的其他子层次定义双向端口**。某些早期 EDA 软件和器件支持在子模块定义双向总线，实例化三态信号，其实从理论上讲任何子模块中定义的三态信号都可以迁移到顶层来。实际上，很多流行的综合工具也是对子模块定义的三态信号综合为选择器，将子模块例化的三态信号迁移到顶层。

为了避免仿真和综合实现结果不一致，并便于维护，强烈建议仅在顶层定义双向总线和例化的三态信号，禁止在除顶层以外的其他层次赋值高阻态 `Z`，在顶层将双向信号分为输入信号和输出信号两种类型，然后根据需要分别传递到不同的子模块中。这样做的另一个好处是便于描述仿真激励。

以下介绍几种典型的双向信号和三态信号的描述方法。

1. 为了清晰起见，在顶层可以将一个双向总线分成输入和输出两条总线，然后分别引入子模块使用。

    ```verilog  bibus.v
    /******************************
    将双向总线 data_bus 分为输入总线 data_in 和输出总线 data_in, 然后分别使用。
    ******************************/

    module bibus (clk, rst, sel, data_bus, addr);
    input       clk, rst, sel;
    input [7:0] addr;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    inout [7:0] data_bus;
    wire [7:0] data_in, data_out;

    assign data_in = data_bus;
    assign data_bus = (sel) ? data_out : 8'bZ;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    decode decode_inst (
        .clock (clk),
        .reset (rst),
        .data_bus_in (data_in),
        .addr_bus (addr),
        .data_bus_out (data_out)
        );
    endmodule
    ```

    简单的三态信号用 `assign` 语法描述，如上述 `bibus.v` 代码中，描述三态总线为：

    ```verilog
    assign data_bus = (sel) ? data_out : 8'bZ;
    ```

2. 如果三态总线的使能关系比较复杂，不是单一信号，此时可以使用嵌套的问号表达式，或者使用语句描述（当然也可以用 `if ... else` 结构）。

    ```verilog complex_bibus.v
    /******************************
    双向总线 databus 的输出由 3 个使能信号 sel1、sel2、se13共同确定, 下述代码中中使用嵌
    套的问号表达式描述了该三态总线的使能选择。
    ******************************/

    module complex_bibus (clk, rst, sel1, sel2, sel3, data_bus, addr);
    input       clk, rst;
    input       sel1, sel2, sel3;
    input [7:0] addr;

    wire [7:0] data_in;
    //wire [7:0] data_out;         //use wire type
    wire [7:0] decode_out;
    wire [7:0] cnt_out;

    assign data_in = data_bus;

    decode decode_inst (
        .clock (clk),
        .reset (rst),
        .data_bus_in (data_in),
        .addr_bus (addr),
        .data_bus_out (decode_out)
        );

    counter counter_inst (
        .clock (clk),
        .reset (rst),
        .data_bus_in (data_in),
        .cnt_out (cnt_out)
        );

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    inout [7:0] data_bus;
    assign data_bus = (sel1)? decode_out : ((sel2)? cnt_out : ((sel3)? 8'b11111111:
        8'bZZZZZZZZ));
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    但是如果使能情况更复杂，就不宜再使用上述 `complex-bibus.v` 中嵌套的问号表达式了。更清晰的描述方法是 `case` 语句，

3. 通过 `case` 语句可以清晰地罗列每种使能组合情况下的输出情况。

    ```verilog complex_bibus2.v
    /******************************
    这种描述需要使到组合逻辑的模块，此时需要引入中间变量 data_out，并定义为 reg 型，
    ******************************/

    module complex_bibus2 (clk, rst, sel1, sel2, sel3, data_bus, addr);
    input       clk, rst;
    input       sel1, sel2, sel3;
    input [7:0] addr;

    assign data_in = data_bus;

    decode decode_inst (
        .clock (clk),
        .reset (rst),
        .data_bus_in (data_in),
        .addr_bus (addr),
        .data_bus_out (decode_out)
        );

    counter counter_inst (
        .clock (clk),
        .reset (rst),
        .data_bus_in (data_in),
        .cnt_out (cnt_out)
        );

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    inout [7:0] data_bus;

    wire [7:0] data_in;
    reg  [7:0] data_out;    //use reg type, but not registers
    wire [7:0] decode_out;
    wire [7:0] cnt_out;

    always @(decode_out or cnt_out or sel1 or sel2 or sel3) begin
        case ({sel1, sel2, sel3})
            3'b100:  data_out = decode_out;
            3'b010:  data_out = cnt_out;
            3'b001:  data_out = 8'b11111111;
            default: data_out = 8'bZZZZZZZZ;
        endcase
    end

    assign data_bus = data_out;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    请注意，虽然输出的中间变量 `data_out`，被定义为 `reg` 型，但是在物理实现时它并不是寄存器，而是纯组合逻辑。引入这个变量的原因是 `inout` 类型的信号只能被定义为 `wire` 或 `tri` 型，不能在组合逻辑的 `always` 模块中直接赋值。

    ```verilog counter.v
    /******************************
    三态实例中调用的子模块
    ******************************/

    module counter (clock,
                    reset,
                    data_bus_in,
                    cnt_out
                    );


    input        clock, reset;
    input  [7:0] data_bus_in;
    output [7:0] cnt_out;

    reg    [7:0] cnt_out;

    always @(posedge clock or posedge reset)
        if (reset)
            cnt_out <= 8'b00000000;
        else
            cnt_out <= data_bus_in + 1;

    endmodule
    ```

    ```verilog decode.v
    /******************************
    三态实例中调用的子模块
    ******************************/

    module decode (clock, reset, data_bus_in, addr_bus, data_bus_out);

    input        clock, reset;
    input  [7:0] data_bus_in;
    input  [7:0] addr_bus;

    output [7:0] data_bus_out;

    reg    [7:0] data_bus_out;

    always @(posedge clock or posedge reset)
        if (reset)
            data_bus_out <= 8'b00000000;
        else
            case (addr_bus)
                8'b 11110000: data_bus_out <= 8'b00001111;
                8'b 00001111: data_bus_out <= 8'b11110000;
                8'b 10100000: data_bus_out <= 8'b11111111;
                default:      data_bus_out <= data_bus_in;
            endcase

    endmodule
    ```

## 3.5. mux 建模

mux 也是一种组合逻辑电路，它的常用建模方式也有两种：对于简单的 mux，可以直接用 `assign` 和 `?` 表达式建模，对于复杂的 mux，则需要使用 `always` 和 `if ... else`、`case` 等条件判断语句建模。

1. **简单的 mux 用 `?` 表达式建模。信号被定义为 `wire` 型，`?` 表达式的判断条件是的选择端。**

    ```verilog mux.v
    /******************************
    使用"?"表达式描述一个 2 选 1 的 mux。
    ******************************/

    module mux (en, a, b, mux_out);

    input        en;
    input        a, b;
    output       mux_out;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    wire mux_out;
    assign mux_out = (en)? a : b;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

2. **复杂的 mux 用 `case` 或嵌套的 `if ... else` 建模。信号被定义为 `reg` 型，每个`case` 或 `if ... else` 的条件分支即一路 mux 的选择输出。**

    ```verilog
    /******************************
    使用 case 描述一个 4 选 1 的 mux。
    ******************************/

    // mux2.v
    module mux2 (en, a, b, c, d, mux_out);

    input  [1:0] en;
    input        a, b, c, d;
    output       mux_out;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg mux_out;

    always @(en or a or b or c or d)
        case(en)
            2'b00:  mux_out = a;
            2'b01:  mux_out = b;
            2'b10:  mux_out = c;
            2'b11:  mux_out = d;
        endcase
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

## 3.6. 存储器建模

逻辑电路设计经常使用一些单口 RAM，双口 RAM 和 ROM 等存储器。Verilog 语法中基本的存储单元定义格式为：

```verilog
reg [datawidth] MemoryName [addresswidth];
```

例如，定义一个数据位宽为 8bit，地址为 63 位的 RAM8x64，则可定义为：

```verilog
reg [7:0] RAM8x64 [0:63];
```

在使用存储单元时，不能直接操作存储器的某地址的某位，如想取地址为 `32` 的第 2 位和高两位，但是这两种描述都是错误的。

```verilog
RAM8x64 [32][2]
RAM8x64 [32][6:7]
```

正确的操作方法是：**先将存储单元赋值给某个寄存器，然后再对该寄存器的某位进行相关操作**。如下例所示。

```verilog ram_basic.v
/******************************
一个简单的 8 位宽, 64 位地址 RAM 的读/写电路，读的时候，先将 RAM8x64 某地址的数据读
到 mem_data 寄存器，然后就可以对寄存器的任意位进行相关操作。

******************************/

module ram_basic (clk, CS, WR, addr, data_in, data_out, en);

input         clk;
input         CS;           // CS = 1, RAM enable
input         WR;           // WR =1 then WRite enable; WR = 0 then read enable
input         en;           // data_out enable, convert the data sequency
input  [5:0]  addr;
input  [7:0]  data_in;
output [7:0]  data_out;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
reg [7:0] RAM8x64 [0:63];
reg [7:0] mem_data;

always @(posedge clk)
    if (WR && CS)           // write
        RAM8x64 [addr] <= data_in [7:0];
    else if (~WR && CS)     // read
        mem_data <= RAM8x64 [addr];

assign data_out = (en)? mem_data[7:0] : {~mem_data[7], mem_data[6:0]};
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

上面讲解的仅仅是 Verilog 语法建模存储单元的一般方法。而对于 PLD 设计、由于几乎所有的 FPGA 都内嵌有 RAM 资源，则**并不推荐使用 Verilog 直接建模 RAM**。FPGA 内嵌的 RAM 资源大致分为两类：块RAM（BIock RAM）资源和分布式 RAM 资源（Distributed RAM，是一种基于特殊底层逻辑单元，通过查找表和触发器实现的 RAM 结构）。PLD中使用存储结构的基本方法有两大类：

- 第一种方法是：通过器件商的开发平台中内嵌的 **IP 生成器**，在图形化界面中直接选择存储器类型（如双口 RAM、单口 RAM、ROM、分布式 RAM 等），配置存储器参数，生产相应 IP，然后在用户逻辑中直接调用该 IP 即可。这种设计方法是 PLD 设计中推荐的方法，因为器件商最了解 PLD 的底层硬件结构，通过 IP 生成器，可以自动地选择使用 PLD 内嵌的 RAM 资源，并生成存储器的粘合逻辑（glue logic），方便、高效、可靠。

- 第二种方法是：直接根据上面的描述用 Verilog 语言建模存储器，由综合器根据代码描述类推并优化存储器结构，调用器件内嵌的硬件存储器资源。这种方法有两个问题：第一是要清晰合理地在代码中描述存储器，有一定的设计难度；第二是最终实现结果在很大的程度上取决于综合器的类推算法，有一定的不确定性。这种方法经常使用在两个场合；第一是 PLD 本身没有块 RAM 或分布式 RAM 等专用存储单元（如 CPLD 等）；第二是用户非常熟悉综合器的类推算法，并能通过综合器的相关约束属性指定所需使用的底层硬件 RAM 资源。

## 3.7. 简单的时钟分频电路

时钟电路是 PLD 设计的核心。在 Part 5 介绍了同步时序电路的相关知识，并介绍了如何处理一些常用时钟电路设计。

对于 PLD 设计而言，由于大多数 PLD（特别是FPGA）都内嵌有专用 PLL/DLL 模块，通过这些内嵌的 PLL 或 DLL，可以实现灵活的分频/倍频（一般可实现小数分频倍频）、移相等调整与运算。所以这类 PLD 设计时钟电路的方法如上小节一样，都推荐使用器件商的开发平台中内嵌的 IP 生成器，在图形化界面中直接配置 PLL/DLL 的参数，生产相应的 IP，然后在用户逻辑中直接调用该IP即可。

这里介绍的一般时钟分频电路建模方法，适用于没有上述内嵌 PLL/DLL 时钟电路（如 CPLD、ASIC 设计等），或内嵌 PLL/DLL 资源不能满足所需时钟关系时的一些处理方法。

一般来说，PLD 中的**主要时钟处理为分频和移相**。偶数分频十分简单，只需用高速时钟做一个同小计数器，然后在相应的位抽头即可。奇数分频电路相对复杂一些。移相的基本方法是通过高速时钟调整相位，或者通过时钟反向调整相位。

1. **偶数分频 + 相位控制**

    ```verilog clk_div_phase.v
    /******************************
    将一个 200kHz 时钟做 2 分频、4 分频、8 分频, 要求分频后的 3 个时钟同相, 而且与源时
    钟近似同相。在这个设计中, 因为输入时钟速率很低, 仅有 200kHz, 而一般 PLD 内嵌的 PLL
    的输入频率下限都在 MHz 级，所以无法使用 PLL 完成分频与相位调整要求。另外对于低速时
    钟的分频，使用计数器既能满足时序要求，也比较节约器件资源。
    ******************************/

    module clk_div_phase (rst, clk_200K, clk_100K, clk_50K, clk_25K);

    input        clk_200K;
    input        rst;
    output       clk_100K, clk_50K, clk_25K;
    wire         clk_100K, clk_50K, clk_25K;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [2:0] cnt;

    always @ (posedge clk_200K or negedge rst)
        if (!rst)
            cnt <= 3'b000;
        else
            cnt <= cnt + 1;

    assign clk_100K = ~cnt [0];
    assign clk_50K  = ~cnt [1];
    assign clk_25K  = ~cnt [2];
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    这个设计的**难点在于如何调整所有时钟的相位关系**。本例巧妙地通过对计数器每个 bit 的反向，完成了所有分频后时钟的相位调整，保证了3个分频后时钟的相位严格同相。对硬件结构比较清晰则会发现，这 3 个派生时钟与源时钟相比有一个非常小的相位差，**这个相位差是由寄存器的固有 $T_{\mathrm{co}}$（延迟）和计数器累加的组合逻辑造成的**，一般来说在 PLD 中寄存器固有 $T_{\mathrm{co}}$ 的典型值为 1~2ns，而简单的加法运算的组合逻辑门延迟也约为 ns 级，这两个延迟的总和与时钟周期相比微乎其微。如果忽略这个 ns 级的延迟，则通过每个分频时钟的反向，使 3 个分频时钟与源时钟同相，也就是说这 4 个时钟有共同的上升沿。

2. **奇数分频**

    ```verilog clk_3div.v
    /******************************
    对源时钟做 3 分频, 要求 3 分频时钟占空比为50％。
    ******************************/

    module clk_3div (clk, reset, clk_out);

    input       clk, reset;
    output      clk_out;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    reg [1:0]   state;
    reg         clk1;

    always @(posedge clk or negedge reset)
        if(!reset)
            state <= 2'b00;
        else
            case(state)
                2'b00:  state <=  2'b01;
                2'b01:  state <=  2'b11;
                2'b11:  state <=  2'b00;
                default:state <=  2'b00;
            endcase

    always @(negedge clk or negedge reset)
        if(!reset)
            clk <= 1'b0;
        else
            clk1 <= state[0];

    assign clk_out = state[0] & clk1;
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

    3 分频、5 分频等奇数分频时钟可以使用 `case` 结构或简单的状态机（FSM）描述，设计**难点在于如何通过组合逻辑调整分频时钟的占空比。**

## 3.8. 串/并转换建模

数据流串/并转换的实现方法多种多样，根据数据的排序和数量的要求，可以选用移位寄存器、RAM 等实现。对于**数量比较小的设计可以采用移位寄存器**完成串/并转换；对于**排列顺序有规定的串/并转换，可以用 `case` 语句判断实现**；对于**复杂的串/并转换，还可以用状态机实现**。

```verilog srl2pal.v
/******************************
简单的串行到并行转换, 数据排列顺序是高位在前。
******************************/

module srl2pal (clk, rst, srl_in, pal_out);

input        clk;
input        rst;
input        srl_in;
output [7:0] pal_out;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
reg    [7:0] pal_out;

always @ (posedge clk or negedge rst)
    if (!rst)
        pal_out <= 8'b0;
    else
        pal_out <= {pal_out,srl_in};
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

## 3.9. 同步复位和异步复位<a name="0309"></a>

复位电路是每个数字逻辑电路的最重要组成部分之一。复位的目的有两个方面：第一是仿真的时候使电路进入初始状态或者其他预知状态；第二是对于综合实现的真实电路，通过复位使电路进入初始状态或者其他预知状态。一般来说，逻辑电路的任何一个寄存器，存储器结构和其他逻辑单元都必须要附加复位逻辑以保证电路能够从错误状态中恢复，可靠地工作。

**常用的复位信号为低电平有效信号，在应用时外部引脚接{% label primary @上拉电阻 %}，这样能增加复位电路的抗干扰性能。**

复位方式大致分为两类：同步复位和异步复位。这两种复位方式各有优缺点，因而应用场合不同。

### 3.9.1. 同步复位

所谓同步复位是指：当复位信号变化时，并**不立即生效，只有当有效时钟沿采样到已变化的复位信号后，才对所有寄存器复位**。同步复位的应用要点如下：

#### 3.9.1.1. 同步复位电路建模

1. 指定**同步复位时，`always` 的敏感表中仅有时钟沿信号**，仅仅当时钟沿采到同步复位的有效电平时，才会在时钟沿到达时刻进行复位操作。如果目标器件或可用库中的触发器本身包含同步复位端口，则在实现时可用直接调用同步复位端。然而很多目标器件（如 PLD）和 ASIC 库的触发器本身并不包含同步复位端口，则复位信号与输入信号组成某种组合逻辑（比如复位低电平有效，则复位与输入信号两者相与即可），然后输入至寄存器的输入端。为了提高复位电路的优先级，一般在电路描述时使用带有优先级的 `if ... else` 结构，复位电路在第一个 `if` 下描述，其他电路在 `else` 或 `else ... if` 分支中描述。

    ```verilog syn_rst.v
    /******************************
    同步复位电路建模。
    ******************************/

    module syn_rst (clk, rst_, cnt1, cnt2);

    input        clk;
    input        rst_;
    output [4:0] cnt1 , cnt2;
    reg    [4:0] cnt1 , cnt2;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    always @(posedge clk)
        if (!rst_) begin
            cnt1 <= 4'b0;
            cnt2 <= 4'b0;
        end
        else begin
            if (cnt1 < 2'b11)
                cnt1 <= cnt1 + 1;
            else
                cnt1 <= cnt1;
            cnt2 <= cnt1 - 1;
        end
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

2. 很多目标器件（如 FPGA 和 CPLD）和 ASIC 库的触发器本身并不包含同步复位端口，则同步复位会被实现为如[图 4-4](#图4-4) 所示的结构。

    <a name="图4-4"></a>
    <div align="center">
      <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-040-SynchronousReset-01.png!600px" alt="同步复位实现结构示意图"/>
    </div>

#### 3.9.1.2. 同步复位的优点

- 同步复位利于基于周期机制的仿真器进行仿真。

- 使用同步复位可以设计 100% 的同步时序电路，利于时序分析，其综合结果的频率往往较高。

- 同步复位仅在时钟的有效沿生效，可以**有效地避免因复位电路毛刺造成的亚稳态和错误**。在进行复位和释放复位信号时，都是仅当时钟沿采到复位信号电平变化时才进行相关操作，如果复位信号树的组合逻辑出现了某种毛刺，此时时钟沿采样到毛刺的概率非常低，通过时钟沿采样，可以十分有效地过滤复位电路组合逻辑产生的毛刺，增强电路稳定性。

  {% note info %}
  同步时序电路的概念参见 Part 5 2.1 小节；亚稳态的概念参见 Part 5 2.2 小节。
  {% endnote %}

#### 3.9.1.3. 同步复位的缺点

- 很多目标器件（如 FPGA 和 CPLD）和 ASIC 库的触发器本身并不包含同步复位端口，使用同步复位会增加更多逻辑资源。

- 同步复位的最大问题在于必须保证复位信号的有效时间足够长，从而才能保证所有触发器都有效地复位。由于仅当时钟沿采样到复位信号时才会进行复位操作，所以**同步复位信号的持续时间起码要大于设计的最长时钟周期**，以保证所有时钟的有效沿都能采样到同步复位信号。其实仅仅保证同步复位信号的持续时间大于最慢的时钟周期还是不够的，设计中**还要考虑到同步复位信号树通过所有相关组合逻辑路径的延时以及由于时钟布线产生的偏斜（skew）**，只有同步复位大于时钟最大周期加上同步信号穿过的组合逻辑路径延迟加上时钟偏斜时，才能保证同步复位可靠、彻底。同步复位实现结构如下图所示，假设同步复位逻辑树组合逻辑的延时为 $\mathtt{t1}$，复位信号传播路径的最大延迟为 $\mathtt{t2}$，最慢时钟的周期为 $\mathtt{Period max}$，时钟的 skew 为 $\mathtt{(clk2 - clk1)}$，则同步复位的周期 $\mathtt{Tsyn\_rst}$ 应该满足下述公式：
$$
\mathtt{Tsyn\_rst > Period max + \left(clk2 - clk1 \right) + t1 + t2}
$$

<a name="图4-5"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-050-SynchronousReset-02.png!800px" alt="同步复位实现结构示意图"/>
</div>

### 3.9.2. 异步复位

#### 3.9.2.1. 异步复位电路建模

1. 指定异步复位时，只需在 `always` 的敏感表中加入复位信号的有效沿即可，当复位信号有效沿到达时，无论时钟沿是否有效，复位立即发挥功能。

    ```verilog asyn_rst.v
    /******************************
    异步复位电路建模。
    ******************************/

    module asyn_rst (clk, rst_, cnt1, cnt2);

    input        clk;
    input        rst_;
    output [4:0] cnt1 , cnt2;
    reg    [4:0] cnt1 , cnt2;

    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
    always @(posedge clk or negedge rst_)
        if (!rst_) begin
            cnt1 <= 4'b0;
            cnt2 <= 4'b0;
        end
       else begin
        if (cnt1 < 2'b11)
            cnt1 <= cnt1 + 1;
        else
            cnt1 <= cnt1;
        cnt2 <= cnt1 - 1;
        end
    // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

    endmodule
    ```

2. 大多数目标器件（如 FPGA 和 CPLD）和 ASIC 库的触发器都包含异步复位端口，异步复位会被直接接到触发器的异步复位端口。同步复位实现结构如[图 4-6](#图4-6) 所示。

<a name="图4-6"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-060-AsynchronousReset-01.png!500px" alt="异步复位实现结构示意图"/>
</div>

#### 3.9.2.2. 异步复位的优点

- 由于多数目标器件（如FPGA 和 CPLD）和 ASIC 库的触发器都包含异步复位端口，异步复位会节约逻辑资源。

- 异步复位设计简单。

- 对于大多数 FPGA, 都有专用的全局异步复位/置位资源（GSR, Global Set Reset），使用GSR 资源，异步复位到达所有寄存器的偏斜（skew）最小。

#### 3.9.2.3. 异步复位的缺点

- 异步复位的作用和释放与时钟沿没有直接关系，在异步复位生效时问题并不明显，但是当异步复位释放时，如果异步复位信号释放时间和时钟的有效沿到达时间几乎一致，则容易造成触发器输出为**亚稳态**，造成逻辑错误。

- 另外，如果异步复位逻辑树的组合逻辑产生了**毛刺**，则毛刺的有效沿会使触发器误复位，造成逻辑错误。

#### 3.9.2.4. 推荐的复位电路设计方式

1. 推荐的复位电路设计方式是异步复位，同步释放。这种方式，可以有效地继承异步复位设计简单的优势，并克服异步复位的上述风险与缺陷。

2. 在 FPGA/CPLD 等可编程逻辑器件设计中，使用异步复位，同步释放可以节约器件资源，并获得稳定可靠地复位效果，是推荐的复位设计方式。

```verilog  asyn_rst_syn_release.v
/******************************
异步复位，同步释放电路建模。异步复位，同步释放的具体设计方法很多，关键是如何保证同步
地释放复位信号，本例举例的方法是在复位信号释放时，用系统时钟采样后再将复位信号送到寄
存器的异步复位端。
******************************/

module asyn_rst_syn_release(clk, rst_, cnt1, cnt2);

input        clk;
input        rst_;
output [4:0] cnt1 , cnt2;
reg    [4:0] cnt1 , cnt2;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
// reset release circuit
reg reset_reg;

always @(posedge clk)
     reset_reg <= rst_;

always @(posedge clk or negedge reset_reg)
    if (!rst_) begin
        cnt1 <= 4'b0;
        cnt2 <= 4'b0;
    end
    else begin
        if (cnt1 < 2'b11)
            cnt1 <= cnt1 + 1;
        else
            cnt1 <= cnt1;
        cnt2 <= cnt1 - 1;
    end
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

上例使用时钟**将外部输入的异步复位信号寄存一个节拍**后，再送到触发器异步复位端口的设计方法的另一个好处在于：做 STA（静态时序分析）分析时，时序工具会自动检查同步后的异步复位信号和时钟的到达（Recovery）/撤销（Removal）时间关系，如果因布线造成的 skew 导致该到达I撤销时间不能满足，STA工具会上报该路径，帮助设计者进一步分析问题。异步部位同步化电路如[图 4-7](#图4-7) 所示。

<a name="图4-7"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-070-AsynchronousReset-02.png!500px" alt="异步复位同步化"/>
</div>

## 3.10. 用 `case` 和 `if ... else` 建模

一般来说， `case` 语句是“平行”（Balance, Parallel）的结构，即每个 `case` 分支的条件判断和执行都是并行的，没有“优先级（Prior）” 。而 `if ... else`、`if ... else if ...` 语句可以建模**无优先级**的判断结构， `if ... if ... if ...` 结构可以建模具**有优先级**的判断结构。

一般来说，建立优先级结构（优先级树）会消耗组合逻辑资源，如果非设计需要，推荐使用 `case` 或 `if ... else` 建立无优先级的判断结构。但是某些设计中，有些信号要求先到达（如：关键使能信号、选择信号等），而有些信号需要后到达（如：慢速信号、有效时间较长的信号等），此时则需要使用 `if ... if ...` 结构建立具有优先级的判断结构。

目前综合工具的优化能力越来越强，大多数情况下可以将不必要的优先级树优化掉，综合结果是否具有优先级，很大的程度上取决于综合工具的类型、综合工具的版本、目标器件（目标库）的固有硬件结构。

为了形象地理解优先级判断结构建模的问题，下面使用几个简单的例子，分别使用业界最流行的两个综合工具 Synplify Pro 和 Precision RTL 分别综合，分析其综合结果的 RTL 视图和结构视图（初学者不用关心这两个综合工具的使用方法，而需要重点观察综合结果的 RTL 视图和结构视图，分析不同语句建模的区别）。

```verilog case1.v
/******************************
casex语句建模
******************************/

module case1(a, b, c, d, sel0, sel1, sel2, sel3, z);

input a, b, c, d;
input sel0, sel1, sel2, sel3;
output z;

reg z;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
always @(a or b or c or d or sel0, sel1, sel2, sel3) begin
    casex ({sel0, sel1, sel2, sel3})
        4'b1xxx: z = d;
        4'bx1xx: z = c;
        4'bxx1x: z = b;
        4'bxxx1: z = a;
        default: z = 1'b0;
    endcase
end
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

```verilog single_if.v
/******************************
单if语句(if ... else if ... else if ...)建模
******************************/

module single_if(a, b, c, d, sel0, sel1, sel2, sel3, z);

input a, b, c, d;
input sel0, sel1, sel2, sel3;
output z;

reg z;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
always @(a or b or c or d or sel0 or sel1 or sel2 or sel3) begin
    z = 0;
    if (sel3)
        z = d;
    else if (sel2)
        z = c;
    else if (sel1)
        z = b;
    else if (sel0)
        z = a;
end
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

```verilog mult_if.v
/******************************
多if语句(if ... if ... if ...)建模
******************************/

module mult_if(a, b, c, d, sel0, sel1, sel2, sel3, z);

input a, b, c, d;
input sel0, sel1, sel2, sel3;
output z;

reg z;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
always @(a or b or c or d or sel0 or sel1 or sel2 or sel3) begin
    z = 0;                // must add default value
    if (sel0) z = a;
    if (sel1) z = b;
    if (sel2) z = c;
    if (sel3) z = d;
end
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

上面的描述如果在 Synopsys 公司的 Design compiler 或 FPGA Compiler 等综合工具的较早版本下综合，多`if` 语句（`if ... if ... if ...`）综合结构如[图 4-8](#图4-8) 所示，而单 `if` 语旬（`if ... else if ... else if ...`）和 `casex` 语句综合结构如[图 4-9](#图4-9) 所示。从图中分析河以看到多if 语句建模结构带有优先级，这时最后一条 `if` 语句对应的 `sel3` 和 `d` 的优先级最高；而单 `if` 语句和 `casex` 语句建模时没有建立优先级。

<a name="图4-8"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-080-MultipleIf.png!800px" alt="多 if 语句 Design Compiler 综合结构视图"/>
</div>

<a name="图4-9"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-090-SingleIf.png!800px" alt="单 if 语句和 casex 语句建模使用 Design Compiler 综合结构视图"/>
</div>

但是由于综合工具的综合优化策略不同，即使对于相同的代码，其综合结果也不尽相同。为了加深理解，使用业界最流行的 PLD 综合工具 Synplify Pro 和 Precision RTL 对多 `if` 语句（`if ... if ... if ...`）、单 `if` 语句（`if ... else if ... else if ...`）和 `casex` 语句分别综合，其中多 `if` 语句的 Synplify Pro 综合结果对应的 RTL 视图和工艺结构视图分别如[图 4-10](#图4-10)、[图 4-11](#图4-11) 所示；多 `if` 语句的 Precision RTL 综合结果对应的 RTL 视图和工艺结构视图分别如[图 4-12](#图4-12)、[图 4-13](#图4-13) 所示。简单分析即可发现，这 3 种语句的 Synplify Pro 和 Precision RTL 综合结果基本一致，都没有明显的优先级结构。这是因为 Synplify Pro 和Precision RTL 这两种综合工具为了节约硬件资源，根据其优化算法．优化掉了冗余的优先级判断结构。

<a name="图4-10"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-100-MultipleIfSynplifyProRTLView.png!600px" alt="多 if 语句的 Synplify Pro 综合结果 RTL 视图"/>
</div>

<a name="图4-11"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-110-MultipleIfSynplifyProTechView.png!800px" alt="多 if 语句的 Synplify Pro 综合结果工艺结构视图"/>
</div>

<a name="图4-12"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-120-MultipleIfPrecisionRTLRTLView.png!800px" alt="多 if 语句的 Precision RTL 综合结果 RTL 视图"/>
</div>

<a name="图4-13"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-130-MultipleIfPrecisionRTLTechView.png!800px" alt="多 if 语句的 Precision RTL 综合结果工艺结构视图"/>
</div>

所以，从语法上讲，多 `if` 语句（`if ... if ... if ...`）可以建模具有优先级的条件判断结构，而单 `if` 语句（`if ... else if ... else if ...`）和 `case` 语旬用于建模不带优先级的条件判断。但是随着综合工具优化能力的不断增强，新型的综合工具，大多时候会自动优化掉优先级结构，以减少芯片面积，提高时序性能。条件结构的综合结果是否带有优先级不但取决于综合工具的类型和版本．还和目标器件或目标库有直接关系。

这里推荐初学者尽量使用 `case` 或单 `if` 语句（`if ... else if ... else if ...`）建模判断结构，这样不论何种综合工具， 一般情况下都不会产牛不必要的优先级结构。使用单 `if` 结构．如果没有为所有的 `if` 指定默认的输出，则会生成 Latch（锁存器），如将上例代码删除 `z=0` 这一默认输出，而改为下面的描述，则会生成 Latch。使用 Synplify Pro 综合的综合结果的RTL 视图和工艺结构视图分别如[图 4-14](#图4-14) 、[图 4-15](#图4-15) 所示。

```verilog  mult_if.v
/******************************
多if语句(if ... if ... if ...)建模, 未指定默认输出, 生成 Latch
******************************/

module mult_if(a, b, c, d, sel0, sel1, sel2, sel3, z);

input a, b, c, d;
input sel0, sel1, sel2, sel3;
output z;

reg z;

// ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
always @(a or b or c or d or sel0 or sel1 or sel2 or sel3) begin
    if (sel0) z = a;
    if (sel1) z = b;
    if (sel2) z = c;
    if (sel3) z = d;
end
// ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

endmodule
```

<a name="图4-14"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-140-MultipleIfSynplifyProRTLView.png!800px" alt="多 if 语句无默认输出时 Synplify Pro 综合结果 RTL 视图"/>
</div>

<a name="图4-15"></a>
<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-12-02-josh-verilog-part-4/2020-12-02-josh-verilog-part-4-150-MultipleIfSynplifyProTechView.png!800px" alt="多 if 语句无默认输出时 Synplify Pro 综合结果工艺结构视图"/>
</div>

如果生成的 Latch 并非设计者意愿，则会造成与设计意图的偏离甚至是错误。而使用完整的 `if. .. else` 或 `case`（全译码或加有 `default` 关键字）语句，则可以有效地避免无意之中生成的 Latch 。在[《Part 3 —— 描述方式和设计层次》的 3.4 小节](https://josh-gao.top/posts/fd117896.html#3-4-%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%E8%AF%AD%E6%97%AC)也有防止产生不必要 Latch 的相关描述，可以参考。

## 3.11. 可综合的 Verilog 语法子集

通过上节建模的感性认识，可以发现，在RTL 级建模时，使用的可综合的 Verilog 语法是整个 Verilog 语法的一个非常小的子集。其实可综合的 Verilog 常用的关键字非常有限，这恰恰体现了 Verilog 语言是硬件描述语言的本质。Verilog HDL 作为硬件描述语言，其本质在于把硬件电路流畅、合理地转换为语言形式，而使用较少的一些关键字就可以有效地将电路转换到可综合的RTL 语言结构。

常用的RTL 语法结构列举如下：

- 模块声明：`module ... endmodule`；
- 端口声明：`input`、`output`、`inout`；
- 信号类型： `wire`、`reg`、`tri`等， `integer` 通常用于 `for` 语句中的索引；
- 参数定义： `parameter`；
- 运算操作符：各种逻辑操作、移位操作、算术操作符，请参考[《Part 2 —— Verilog 语言基础》的第 10 小节](https://josh-gao.top/posts/fd2ca242.html#10-%E6%93%8D%E4%BD%9C%E6%95%B0%E3%80%81%E6%93%8D%E4%BD%9C%E7%AC%A6%E5%92%8C%E8%A1%A8%E8%BE%BE%E5%BC%8F)；
- 比较判断： `case [default] endcase (casex/casez)`、`if ... else ...`；
- 连续赋值： `assign`、问号表达式；
- always 模块：建模时序和组合逻辑（敏感表为电平或 `posedge` 或 `negedge` 的沿信号）；
- 语法分割符： `begin ... end`；
- 任务定义：`tast. .. end task`；
- 循环语句： `for`。

这些关键字的语法在[《Part 2 —— Verilog 语言基础》](https://josh-gao.top/posts/fd2ca242.html)和[《Part 3 —— 描述方式和设计层次》](https://josh-gao.top/posts/fd117896.html)中都有详细介绍，请参考这部分内容。

# 4. 小结

本篇笔记中首先介绍了 RTL 和综合的基本概念。然后摆脱长篇大论的理论说教模式，通过一个个具体范例，力图使初学者逐步建立起可综合 RTL 子集的概念。希望大家认真琢磨常用电路结构的建模方法，在实践中掌握 RTL 级设计的基本技巧。

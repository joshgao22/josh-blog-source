---
title: "Josh's Note — SystemVerilog 验证<br>Part 3 过程语句和子程序"
mathjax: true
comments: true
copyright: true
sticky: false
toc:
  enable: true
  number: false
  wrap: true
  expand_all: false
  max_depth: 4
categories:
  - - Josh 的学习笔记
    - SystemVerilog
abbrlink: '43879201'
date: 2022-06-02 13:24:54
tags:
---

在做设计验证时，需要写很多代码，其中大部分在任务和函数里面。SystemVerilog 在这方面增加了许多改进使得它更接近 C 语言，从而使代码的编写变得更加容易，尤其是在处理参数传递上。

<!-- more -->

# 1. 过程语句

SystemVerilog 从 C 和 C++ 中引入了很多操作符和语句：

- 可以在 `for` 循环中定义循环变量，它的作用范围仅限于循环内部，从而有助于避免一些 bug。
- 自动递增符 `++` 和自动递减符 `--` 既可以作为前缀，也可以作为后缀。
- 如果在 `begin` 或 `fork` 语句中使用标识符，那么在相对应的 `end` 或 `join` 语句中可以放置相同的标号，这使得程序块的首尾匹配更加容易。
- 也可以把标识符放在 SystemVerilog 的其他结束语句里，例如 `endmodule`、`endtask`、`endfunction` 以及本文将介绍的其他语句。

[例 3.1](#例3.1) 展示了一些新的语法结构。

<span id="例3.1"></span>

``` verilog 例 3.1 新的过程语句和操作符
initial
    begin : example
    integer array[10], sum, j;

    // 在 for 语句中声明 i
    for (int i=0; i<10; i++)       // i 递增
        array[i] = i;

    // Add up values in the array
    sum = array[9];
    j=8;
    do                            // do...while 循环
        sum += array[j];          // 累加
    while (j--);                  // 判断 j=0 是否成立
    $display("Sum=%4d", sum);     // %4d，指定宽度
end : example                     // 结束标识符
```

SystemVerilog 为循环功能增加了两个新语句：

- 第一个是 `continue`，用于在循环中跳过本轮循环剩下的语句而直接进入下一轮循环。
- 第二个是 `break`，用于终止并跳出循环。

[例 3.2](#例3.2) 中的循环使用 Verilog-2001 中的文件输入输出系统任务从一个文件中读取命令。如果读到的命令只是一个空行，则执行 `continue` 语句，跳过对这个命令的任何进一步处理。如果读到的命令是 “done”，代码将会执行 `break` 终止循环。

<span id="例3.2"></span>

``` verilog 例 3.2 在读取文件时使用 <code>break</code> 和 <code>continue</code>
initial begin
    bit [127:0] cmd;
    int file, c;

    file = $fopen("commands.txt", "r");
    while (!$feof(file)) begin
        c = $fscanf(file, "%s", cmd);
        case (cmd)
            "":     continue;    // 空行——跳到本轮循环的末尾
            "done": break;       // Done——终止并跳出循环
            ...                  // 处理其他命令
        endcase // case(cmd)
        end
    $fclose(file);
end
```

# 2. 任务、函数以及 `void` 函数

在 Verilog 中，任务 (task) 和函数 (function) 之间有很明显的区别，其中最重要的一点是，**任务可以消耗时间而函数不能**。函数里面不能带有诸如 `#100` 的时延语句或诸如 `@(posedge clock)`、`wait(ready)` 的阻塞语句，也不能调用任务。另外，Verilog 中的函数必须有返回值，并且返回值必须被使用，例如用到赋值语句中。

SystemVerilog 对这条限制稍有放宽，允许函数调用任务，但只能在由 `fork... join_none` 语句生成的线程中调用。

> 不消耗时间的 SystemVerilog 任务应该被定义成 `void` 函数，这种函数没有返回值，这样它就能被任何任务或函数所调用了。从最大灵活性的角度考虑，所有用于调试的子程序都应该定义成 `void` 函数而非任务，以便于被任何其他任务或函数所调用。[例 3.3](#例3.3) 可以输出状态机的当前状态值。

<span id="例3.3"></span>

``` verilog 例 3.3 用于调试的 <code>void</code> 函数
function void print_state(...);
    $display("@%0t: state = %s", $time, cur_state.name());
endfunction
```

在 SystemVerilog 中，如果想调用函数并且忽略它的返回值，可以使用 `void` 进行结果转换，如[例 3.4](#例3.4) 所示。有些仿真器，如 VCS，允许在不使用上述 void 语法的情况下忽略返回值。

<span id="例3.4"></span>

``` verilog 例 3.4 忽略函数的返回值
void'($fscanf(file, "%d", i));
```

# 3. 任务和函数概述

SystemVerilog 在任务和函数上做了一些小改进，使得它们看起来更像 C 或 C++ 的程序。一般情况下，不带参数的子程序在定义或调用时并不需要带空括号 `()`。为清楚起见，本文对此种情形的子程序将全部带括号。

## 3.1. 在子程序中移除 `begin...end`

在 SystemVerilog 中，你可能会注意到的第一个改进就是，`begin...end` 块变成可选了，而在 Verilog-1995 中则对单行以外的子程序都是必须的。如[例 3.5](#例3.5) 所示，`task/end­task` 和 `function/endfunction` 的关键词已经足以定义这些子程序的边界了。

<span id="例3.5"></span>

``` verilog 例 3.5 不带 <code>begin...end</code> 的简单任务
task multiple_lines;
    $display("First line");
    $display("Second line");
endtask : multiple_lines
```

# 4. 子程序参数

SystemVerilog 对子程序的很多改进使参数的声明变得更加方便，同时也扩展了参数的传递方式。

## 4.1. C 语言风格的子程序参数

System Verilog 和 Verilog-2001 在任务和函数参数的声明上更加简洁，更少重复。[例 3.6](#例3.6) 中的 Verilog 任务要求对一些参数进行两次声明，一次是方向声明，另一次是类型声明。

<span id="例3.6"></span>

``` verilog 例 3.6 Verilog-1995 的子程序参数
task mytask2;
    output [31:0] x;
    reg    [31:0] x;
    input         y;
    ...
endtask
```

而在 System Verilog 中，可以采用简明的 C 语言风格，如[例 3.7](#例3.7) 所示。但注意**必须使用通用的输入类型 `logic`**。

<span id="例3.7"></span>

``` verilog 例 3.7 C 语言风格的子程序参数
task mytask1 (output logic [31:0] x,
              input  logic y);
    ...
endtask
```

## 4.2. 参数的方向

在声明子程序参数方面还可以有更多的便捷。因为默认的类型和方向是 “logic 输入”，所以在声明类似参数时可不必重复。[例 3.8](#例3.8) 所示为采用 SystemVerilog 的数据类型，但以 Verilog-1995 的风格编写的一个子程序头。

<span id="例3.8"></span>

``` verilog 例 3.8 带 Verilog 风格的繁冗的子程序参数
task T3;
    input a, b;
    logic a, b;
    output [15:0] u, v;
    bit [15:0] u, v;
    ...
endtask
```

可以把它重写成[例 3.9](#例3.9) 的形式。

<span id="例3.9"></span>

``` verilog 例 3.9 使用默认类型的子程序参数
task T3(a, b, output bit [15:0] u, v);
```

参数 `a` 和 `b` 是 1 比特宽度的 logic 输入。参数 `u` 和 `v` 是 16 比特宽度的 bit 类型输出。尽管有这种简洁的编程方式，但不建议使用这种方式，因为这种方式将容易引入一些细小而难以发现的 bug。所以**建议对所有子程序参数的声明都带上类型和方向**。

## 4.3. 高级的参数类型

Verilog 对参数的处理方式很简单：在子程序的开头把 `input` 和 `inout` 的值复制给本地变量，在子程序退出时则复制 `output` 和 `inout` 的值。除了标量以外，没有任何把存储器传递给 Verilog 子程序的办法。

在 SystemVerilog 中，参数的传递方式可以指定为引用而不是复制。这种 `ref` 参数类型比 `input`、`output` 或 `inout` 更好用。首先，把数组传递给子程序。

<span id="例3.10"></span>

``` verilog 例 3.10 使用 <code>ref</code> 和 <code>const</code> 传递数组
function void print_checksum (const ref bit [31:0] a[]);
    bit [31:0] checksum = 0;
    for (int i=0; i<a.size(); i++)
        checksum ^= a[i];
    $display("The array checksum is %0d", checksum);
endfunction
```

SystemVerilog 允许不带 `ref` 进行数组参数的传递，这时数组会被复制到堆栈区里。这种操作的代价很高，除非是对特别小的数组。

SystemVerilog 的语言参考手册 (LRM) 规定了 `ref` 参数只能被用于带自动存储的子程序中。如果对程序或模块指明了 `automatic` 属性，则整个子程序内部都是自动存储的。

[例 3.10](#例3.10) 也用到了 `const` 修饰符，其结果是，**虽然数组变量 `a` 指向了调用程序中的数组，但子程序不能修改数组的值**。如果试图改变数组的值，编译器将报错。

> 向子程序传递数组时应尽量使用 `ref` 以获取最佳性能。如果不希望子程序改变数组的值，可以使用 `const ref` 类型。这种情况下，编译器会进行检查以确保数组不被子程序修改。

`ref` 参数的第二个好处是**可以在任务里修改变量，并且修改结果对调用它的函数随时可见**。当有若干并发执行的线程时，这样操作可以提供一种简单的信息传递方式。

在[例 3.11](#例3.11) 中，一旦 `bus.enable` 有效，初始化块中的 `thread2` 块马上就可以获取来自存储器的数据，而不用等到 `bus_read` 任务完成总线上的数据处理后返回，这可能需要若干个时钟周期。由于参数 `data` 是以 `ref` 方式传递的，所以只要任务里的 `data` 一有变化，`@data` 语句就会触发。如果你把 `data` 声明为 `output`，则 `@data` 语句就要等到总线处理完成后才能触发。

<span id="例3.11"></span>

``` verilog 例 3.11 在多线程间使用 <code>ref</code>
task bus_read(input logic [31:0] addr,
              ref   logic [31:0] data);

    // 请求总线并驱动地址
    bus.request = 1'b1;
    @(posedge bus.grant) bus.addr = addr;

    // 等待来自存储器的数据
    @(posedge bus.enable) data = bus.data;

    // 释放总线并等待许可
    bus.request = 1'b0;
    @(negedge bus.grant);
endtask

logic [31:0] addr, data;

initial
    fork
        bus_read(addr, data);
        thread2: begin
            @data;  // 在数据变化时触发
            $display("Read %h from bus", data);
        end
    join
```

## 4.4. 参数的默认值

当测试程序越来越复杂时，会希望在不破坏已有代码的情况下增加额外的控制。在[例 3.10](#例3.10) 的函数里，可能想把数组中间部分元素的校验和打印出来，但是又不希望改写代码，为每次函数调用增加额外的参数。在 SystemVerilog 中，可以为参数指定一个默认值，如果在调用时不指明参数，则使用默认值。[例 3.12](#例3.12) 为 `print_checksum` 函数增加了 `low` 和 `high` 两个参数，这样就能够打印出指定范围内的数组内容的校验和。

<span id="例3.12"></span>

``` verilog 例 3.12 带默认参数值的函数
function void print_checksum(ref bit [31:0] a[],
                             input bit [31:0] low = 0,
                             input int high = -1);
    bit [31:0] checksum = 0;

    if (high == -1 || high >= a.size())
        high = a.size()-1;

    for (int i=low; i<=high; i++)
        checksum += a[i];
    $display("The array checksum is %0d", sum);
endfunction
```

可以使用如[例 3.13](#例3.13) 所示的方式调用这个函数。注意，第一个调用对两种形式的 `print_checksum` 子程序都是可行的。

<span id="例3.13"></span>

``` verilog 例 3.13 使用参数的默认值
print_checksum(a);       // a[0:size()-1]中所有元素的校验和——默认情况
print_checksum(a, 2, 4); // a[2:4]中所有元素的校验和
print_checksum(a, 1);    // 从 1 开始
print_checksum(a,, 2);   // a[0:2]中所有元素的校验和
print_checksum();        // 编译错误：a 没有默认值
```

使用 -1 (或其他任何越界值) 作为默认值，对于获知调用时是否有指定值，不失为一个好方法。

Verilog 中的 `for` 循环总是在执行初始化 (`int i=low`) 和条件测试 (`i<=high`) 之后再开始循环。所以，如果不小心把一个大于 `high` 或数组宽度的数值传递给 `low`，那么 `for` 循环的循环体将不会被执行。

## 4.5. 采用名字进行参数传递

在 SystemVerilog 的语言参考手册 (LRM) 中，任务或函数的参数有时被称为端口 (port)，就跟模块的接口一样。如果有一个带着许多参数的任务或函数，其中一些参数有缺省值，而又只想对它们中的部分参数进行设置，那么可以通过采用类似 `port` 的语法指定子程序参数名字的方式来指定一个子集，如[例 3.14](#例3.14) 所示。

<span id="例3.14"></span>

``` verilog 例 3.14 采用名字进行参数传递
task many (input int a=1, b=2, c=3, d=4);
    $display("%0d %0d %0d %0d", a, b, c, d);
endtask

initial begin           // a  b  c  d
    many(6, 7, 8, 9);   // 6  7  8  9  指定所有值
    many();             // 1  2  3  4  使用默认值
    many(.c(5));        // 1  2  5  4  只指定 c
    many(, 6, .d(8));   // 1  6  3  8  混合方式
end
```

## 4.6. 常见的代码错误

> 在编写子程序代码时最容易犯的错误就是，往往会忘记，在默认情况下，参数的类型是与其前一个参数相同的，而第一个参数的默认类型是单比特输入。先看看[例 3.15](#例3.15) 所示的简单的任务头。

<span id="例3.15"></span>

``` verilog 例 3.15 原始的任务头
task sticky(int a, b);
```

这两个参数都是整型输入。在编写这个任务时，由于需要访问一个数组，因此又加入了一个新的数组参数，并且使用 `ref` 类型以便让数组值不被复制。修改后的子程序头如[例 3.16](#例3.16) 所示。

<span id="例3.16"></span>

``` verilog 例 3.16 加入额外数组参数的任务头
task sticky(ref int array[50],
            int a, b);      // 这些变量的方向是什么？
```

`a` 和 `b` 的参数类型是什么呢？它们在方向上实际采用的是与前一个参数一致的 `ref` 类型。对简单的 `int` 变量使用 `ref` 通常并无必要，但编译器不会对此做出任何反应，连警告都没有，所以你不会意识到正在使用一个错误的方向类型。

如果在子程序中使用了非默认输入类型的参数，应该明确指明所有参数的方向，如[例 3.17](#例3.17) 所示。

<span id="例3.17"></span>

``` verilog 例 3.17 加入额外数组参数的任务头
task sticky(ref   int array[50],
            input int a, b);  // 明确指定方向
```

# 5. 子程序的返回

Verilog 中子程序的结束方式比较简单；当执行完子程序的最后一条语句，程序就会返回到调用子程序的代码上。此外，函数还会返回一个值，该值被赋给与函数同名的变量。

## 5.1. 返回 (`return`) 语句

SystemVerilog 增加了 `return` 语句，使子程序中的流程控制变得更方便。[例 3.18](#例3.18) 中的任务由于发现错误而需要提前返回。如果不这样做，那么任务中剩下的部分就必须被放到一个 `else` 条件语句中，从而使得代码变得不规整，可读性也降低了。

<span id="例3.18"></span>

``` verilog 例 3.18 在任务中用 <code>return</code> 返回
task load_array(int len, ref int array[]);
    if (len <= 0) begin
        $display("Bad len");
        return;
    end

    // 任务中其余的代码
    ...
    endtask
```

`return` 语句也可以简化函数，如[例 3.19](#例3.19) 所示。

<span id="例3.19"></span>

``` verilog 例 3.19 在函数中用 <code>return</code> 返回
function bit transmit(...);
    // Send transaction
    ...
    return ~ifc.cb.error; // 返回状态：0=error
endfunction
```

## 5.2. 从函数中返回一个数组

Verilog 的子程序只能返回一个简单值，如比特、整数或是向量。如果想计算并返回一个数组，那就不是一件容易的事情了。在 SystemVerilog 中，函数可以采用多种方式返回一个数组。

**第一种方式是定义一个数组类型，然后在函数的声明中使用该类型**。[例 3.20](#例3.20) 使用了[例 2.35](https://josh-gao.top/posts/2a5db13d.html#%E4%BE%8B2.35) 的数组类型，并创建了一个函数来初始化数组。

<span id="例3.20"></span>

``` verilog 例 3.20 使用 <code>typedef</code> 从函数中返回一个数组
typedef int fixed_array5[5];
fixed_array5 f5;

function fixed_array5 init(int start);
    foreach (init[i])
        init[i] = i + start;
endfunction

initial begin
    f5 = init(5);
    foreach (f5[i])
        $display("f5[%0d] = %0d", i, f5[i]);
end
```

使用上述代码的一个问题是，函数 `init` 创建了一个数组，该数组的值被拷贝到数组 `f5` 中。如果数组很大，那么可能会引起性能上的问题。

**另一种方式是通过引用来进行数组参数的传递**。最简单的办法是以 ref 参数的形式将数组传递到函数里，如[例 3.21](#例3.21) 所示。

<span id="例3.21"></span>

``` verilog 例 3.21 把数组作为 <code>ref</code> 参数传递给函数
function void init(ref int f[5], input int start);
    foreach (f[i])
        f[i] = i + start;
endfunction

int fa[5];
initial begin
    init(fa, 5);
    foreach (fa[i])
        $display("fa[%0d] = %0d", i, fa[i]);
end
```

从函数中返回数组的最后一种方式是将数组包装到一个类中，然后返回对象的句柄。

# 6. 局部数据存储

Verilog 在 20 世纪 80 年代被创建时，最初的目的是用来描述硬件。因此，语言中的所有对象都是静态分配的。特别是，子程序参数和局部变量是被存放在固定位置的，而不像其他编程语言那样存放在堆栈区里。诸如递归子程序一类的动态代码没有对应的芯片实现方式，那还有什么必要为它们建模呢？对于那些做验证的软件工程师来说，使用 Verilog
可能会有些困难，他们已经习惯了像 C 一类的基于堆栈区 (stack-based) 的语言，因而在使用子程序库创建复杂 testbench 方面可能会显得力不从心。

## 6.1. 自动存储

在 Verilog-1995 里，如果试图在测试程序里的多个地方调用同一个任务，由于任务里的局部变量会使用共享的静态存储区，所以不同的线程之间会窜用这些局部变量。在Verilog-2001 里，可以指定任务、函数和模块使用自动存储，从而迫使仿真器使用堆栈区存储局部变量。

> 在 SystemVerilog 中，模块 (`module`) 和 `program` 块中的子程序默认情况下仍然使用静态存储。如果要使用自动存储，则必须在程序语句中加入 `automatic` 关键词。后续会详细讲解用于编写 testbench 代码的 `program` 块，以及如何在创建多线程时使用动态存储。

[例 3.22](#例3.22) 所示的是一个用于监测数据何时被写人存储器的任务。

<span id="例3.22"></span>

``` verilog 例 3.22 在 <code>program</code> 块中指定自动存储方式
program automatic test;
    task wait_for_mem(input [31:0] addr, expect_data,
                      output success);
        while (bus.addr !== addr)
            @(bus.addr);
        success = (bus.data == expect_data);
    endtask
    ...
endprogram
```

因为参数 `addr` 和 `expect_data` 在每次调用时都使用不同的存储空间，所以对这个任务同时进行多次调用是没有问题的。但如果没有修饰符 `automatic`，由于第一次调用的任务处于等待状态，所以对 `wait_for_mem` 的第二次调用会覆盖它的两个参数。

## 6.2. 变量的初始化

> 当试图在声明中初始化局部变量时，类似的问题也会出现，因为局部变量实际上在仿真开始前就被赋了初值。常规的解决方法是**避免在变量声明中赋予除常数以外的任何值**。对局部变量使用单独的赋值语句也会使控制变得更方便。

[例 3.23](#例3.23) 中的任务在检测总线五个周期以后，创建了一个局部变量并试图把当前地址总线的值作为初值赋给它。

<span id="例3.23"></span>

``` verilog 例 3.23 静态初始化的 bug
program initialization; // 有 bug 的版本
    task check_bus;
        repeat (5) @(posedge clock);
        if (bus_cmd == 'READ) begin
            // 何时对 local_addr 赋初值？
            logic [7:0] local_addr = addr<<2;  // Bug
            $display("Local Addr = %h", local_addr);
        end
    endtask
endprogram
```

存在的 bug 是，变量 `local_addr` 是静态分配的，所以实际上在仿真的一开始它就有了初值，而不是等到进入 `begin ... end` 块中才进行初始化。同样，解决的办法是把程序块声明为 `automatic`，如同[例 3.24](#例3.24) 所示。

<span id="例3.24"></span>

``` verilog 例 3.24 修复静态初始化的 bug：使用 <code>automatic</code>
program automatic initialization; // Bug 被修复
    ...
endprogram
```

此外，如果不在声明中初始化变量，那这个 bug 也可以避免，只是这种方式不太好记住，尤其是对习惯了 C 语言的程序员。[例 3.25](#例3.25) 给出了一种较为可取的编码风格，用于分离声明和初始化。

<span id="例3.25"></span>

``` verilog 例 3.25 修复静态初始化的 bug：把声明和初始化拆开
logic [7:0] local_addr
local_addr = addr << 2;  // Bug
```

# 7. 时间值

SystemVerilog 有几种新结构使你可以非常明确地在系统中指明时间值。

## 7.1. 时间单位和精度

当使用编译指示语句 `'timescale` 时，在编译文件时就必须按照适当的顺序以确保所有的时延都采用适宜的量程和精度。`timeunit` 和 `timeprecision` 声明语句可以明确地为每个模块指明时间值，从而避免歧义。[例 3.26](#例3.26) 展示了这些声明语句。注意，如果使用这些语句替代 `` `timescale``，则必须把它们放到每个带有时延的模块里。

## 7.2. 时间参数

SystemVerilog 允许使用数值和单位来明确指定一个时间值。代码里可以使用类似 `0.1ns` 和 `20ps` 的时延。只要记得使用 `timeunit` 和 `timeprecision`、或者 `` `timescale`` 即可。还可以通过使用经典的 Verilog 时间函数 `$timeformat`，`$time` 和 `$realtime` 来使代码在时间标度上更清楚。`$timeformat` 的四个参数分别是时间标度 (-9 代表纳秒，-12 代表皮秒)，小数点后的数据精度，时间值之后的后缀字符串，以及显示数值的最小宽度。

例 3.26 所示的是使用 `$tmeformat()` 和 `%t` 指定符进行格式化后的多种时延以及打印结果。

<span id="例3.26"></span>

``` verilog 例 3.26 时间参数和 <code>$timeformat</code>
module timing;
    timeunit 1ns;
    timeprecision 1ps;
    initial begin
        $timeformat(-9, 3, "ns", 8);
        #1     $display("%t", $realtime); // 1.000ns
        #2ns   $display("%t", $realtime); // 3.000ns
        #0.1ns $display("%t", $realtime); // 3.100ns
        #41ps  $display("%t", $realtime); // 3.141ns
    end
endmodule
```

## 7.3. 时间和变量

可以把时间值存放到变量里，并在计算和延时中使用它们。根据当前的时间量程和精度，时间值会被缩放或舍入。`time` 类型的变量不能保存小数时延，因为它们是 64 比特的整数，所以时延的小数部分会被舍入。如果不希望这样，那应该采用 `real` 变量。

[例 3.27](#例3.27) 使用实 (real) 变量保存精确的数值，它们只在用作时延量的时候才被舍入。

<span id="例3.27"></span>

``` verilog 例 3.27 时间变量及舍入
`timescale 1ps/1ps
module ps;
    initial begin
        real rdelay = 800fs;    // 以 0.800 存储
        time tdelay = 800fs;    // 舍入后得到 1
        $timeformat(-15, 0, "fs", 5);
        #rdelay;                // 时延舍入后得到 1ps
        $display("%t", rdelay); // "800fs"
        #tdelay;                // 再次延时 1ps
        $display("%t", tdelay); // "1000fs"
    end
endmodule
```

## 7.4. `$time` 与 `$realtime` 的对比

系统任务 `$time` 的返回值是一个根据所在模块的时间精度要求进行舍入的整数，不带小数部分，而 `$realtime` 的返回值则是一个带小数部分的完整实数。本文为简洁起见，所举例子中全部使用 `$time`，但请不要忘记，testbench 可能需要使用 `$realtime`。

# 8. 结束语

SystemVerilog 的程序化结构和任务、函数中的新特点使得它与诸如 C/C++ 一类的编程语言更加接近，从而也更便于编写 testbench。和 C/C++ 相比，SystemVerilog 还拥有额外的 HDL 结构，例如，时序控制、简单的线程控制和四态逻辑等。

# 参考文献

1. 克里斯·斯皮尔, 斯皮尔, 张春,等. SystemVerilog验证:测试平台编写指南[M]. 科学出版社, 2009.
2. Spear C. SystemVerilog for verification: a guide to learning the testbench language features[M]. Springer Science & Business Media, 2008.

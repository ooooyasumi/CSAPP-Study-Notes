## 英特尔X86架构

### 英特尔处理器历史

#### 英特尔的第一款16位微处理器：Intel 8086

![image-20241117135950043](https://lsky.ooooyasumi.com/i/2024/11/17/6739865c9313f.png)

它基于CISC设计风格

![image-20241117140627775](https://lsky.ooooyasumi.com/i/2024/11/17/673987e5586a3.png)

#### 英特尔芯片的历史年表：

![image-20241117141156977](https://lsky.ooooyasumi.com/i/2024/11/17/6739892f3965b.png)

2015年的英特尔芯片：

![image-20241117141516241](https://lsky.ooooyasumi.com/i/2024/11/17/673989f7010a1.png)

#### 英特尔的64位历史

![image-20241117142044820](https://lsky.ooooyasumi.com/i/2024/11/17/67398b3ee1d42.png)

#### 如今的操作系统架构

![image-20241117142311125](https://lsky.ooooyasumi.com/i/2024/11/17/67398bd0bb562.png)

现如今主流的64bit操作系统：**x86-64**和**ARM**

ARM基于RISC设计，更精简，更节能。

### 术语

以下定义引用至维基百科

#### 计算机架构（[Computer architecture](https://en.wikipedia.org/wiki/Computer_architecture)）

> ***Computer architecture** is a description of the structure of a computer system made from component parts.*
>
> 计算机体系结构是对由组成部分组成的计算机系统结构的描述。

关注计算机系统的设计和组织，包括处理器、存储器、总线、输入输出设备等在系统级别的互连和功能设计。

例如：指令集的规范

#### 指令集架构（[Instruction set architecture](https://en.wikipedia.org/wiki/Instruction_set_architecture)，ISA）

> *An **instruction set architecture** (**ISA**) is an [abstract model](https://en.wikipedia.org/wiki/Conceptual_model) that generally defines how [software](https://en.wikipedia.org/wiki/Software) controls the [CPU](https://en.wikipedia.org/wiki/Central_processing_unit) in a computer or a family of computers.*
>
> 在计算机科学中，指令集架构 （ISA） 是一种抽象模型，通常定义软件如何控制计算机或一系列计算机中的 CPU。

ISA关注的是处理器与软件之间的接口，定义了处理器能够执行的指令集，影响着软件的编写和运行方式。

#### 微架构（[Microarchitecture](https://en.wikipedia.org/wiki/Microarchitecture)）

> ***Microarchitecture**is the way a given [instruction set architecture](https://en.wikipedia.org/wiki/Instruction_set_architecture) (ISA) is implemented in a particular [processor](https://en.wikipedia.org/wiki/Central_processing_unit).*
>
> 微架构是在特定处理器中实现给定指令集架构 （ISA） 的方式。

微架构关注的是处理器内部的设计和实现细节，实现了ISA规定的指令集，包括处理器内部的数据流、控制逻辑、流水线设计等。

#### 机器代码（[Machine code](https://en.wikipedia.org/wiki/Machine_code)）

> **Machine code** is [computer code](https://en.wikipedia.org/wiki/Computer_program) consisting of **machine language** [instructions](https://en.wikipedia.org/wiki/Instruction_set_architecture), which are used to control a computer's [central processing unit](https://en.wikipedia.org/wiki/Central_processing_unit) (CPU).
>
> 机器代码是由机器语言指令组成的计算机代码，用于控制计算机的中央处理器 （CPU）。

#### 汇编语言（[Assembly language](https://en.wikipedia.org/wiki/Assembly_language)）

> **Assembly language**is any [low-level programming language](https://en.wikipedia.org/wiki/Low-level_programming_language) with a very strong correspondence between the instructions in the language and the [architecture's](https://en.wikipedia.org/wiki/Computer_architecture) [machine code](https://en.wikipedia.org/wiki/Machine_code) [instructions](https://en.wikipedia.org/wiki/Instruction_set_architecture).
>
> 汇编语言是任何低级编程语言语言中的指令与架构的机器代码指令之间具有非常强的对应关系。

## 程序编码

将C程序编译成可执行文件

![image-20241117150104167](https://lsky.ooooyasumi.com/i/2024/11/17/673994b52e074.png)

编译流程：

1. 预处理
2. 编译
3. 汇编
4. 链接

> 链接：[编译的四个流程](https://blog.csdn.net/m0_72484344/article/details/134952277)

我们在本章节主要研究汇编阶段。

### gcc编译器的使用

#### gcc命令

`-o`输出 `-E`加只进行预处理 `-S`只进行编译 `-c`只进行汇编 `-o`

`-Og`代表优化级别g。在gcc编译器中有很多种优化级别比如`-O1、-O2、-O3`，优化后的代码会很大程度的变形，但是会提高性能。Og级别是**代码变形最小的优化**，我们采用`-Og`级别易于理解。

```c
//-o是输出的意思 输出名为hello的可执行程序，然后可以执行./hello
gcc -Og -o hello hello.c
//gcc -o [输出可执行文件] [输入文件.c]
```

```c
//分步进行
//只进行预处理，生成.i文件
gcc -E -o hello.i hello.c
//只进行编译操作，将.i文件生成.s的汇编文件
gcc -S -o hello.s hello.i
//只进行汇编操作，将.s文件生成.o目标文件
gcc -c -o hello.o hello.s
//只进行链接操作，将.o文件进行链接，生成最终的可执行文件
gcc -o hello hello.o //后面加目标文件就是只进行链接
gcc -o hello hello1.c hello2.c //后面加.c文件就是输出可执行文件，也可以链接两个文件
//执行可执行文件
./hello
```

 #### 文件后缀

**.c 文件**：

- `.c` 文件是 C 源代码文件。它包含了用 C 语言编写的源代码。
- 通过 GCC 编译器编译 `.c` 文件可以生成目标文件（`.o` 文件）或者可执行文件。

**.i 文件**：

- `.i` 文件是预处理后的 C 源代码文件。在编译过程中，`.i` 文件是经过预处理器处理后的中间文件，展开了所有的宏定义。
- 你可以使用 `-E` 选项来让 GCC 生成 `.i` 文件，而不进行编译。

**.s 文件**：

- `.s` 文件是汇编代码文件，也称为汇编语言文件。它包含了汇编语言的代码，是由编译器将 C 源代码翻译成的汇编语言代码。
- 你可以使用 `-S` 选项来让 GCC 生成 `.s` 文件，而不进行汇编。

**.o 文件**（**二进制**）：

- `.o` 文件是目标文件，也称为目标代码文件。它包含了汇编代码的二进制表示形式，是由汇编器将汇编代码转换成的机器代码。
- 你可以使用 GCC 编译器将多个 `.o` 文件链接在一起，生成可执行文件。

**没有后缀的文件**（**二进制**）：

- 没有文件扩展名的文件通常是可执行文件。在 Unix/Linux 系统中，可执行文件通常没有扩展名，而在 Windows 系统中，可执行文件通常会有 `.exe` 扩展名。
- 可执行文件是由链接器将多个目标文件（`.o` 文件）链接在一起生成的，其中至少包含一个包含 `main` 函数的目标文件。

#### GDB调试工具

GDB调试工具是一个强大的古老的命令行调试工具。我们可以使用GDB学习编译过程。

```
//单步执行GDB命令:查看hello代码的前14字节的十六进制文件
(gdb) x/14xb hello
```

![image-20241118111503524](https://lsky.ooooyasumi.com/i/2024/11/18/673ab139245c8.png)

> GBD调试工具的简单使用方法
>
> **编译程序时添加调试信息**：
>
> - 在使用 GCC 编译程序时，可以通过添加 `-g` 选项来生成包含调试信息的可执行文件。示例：`gcc -g my_program.c -o my_program`
>
> **启动 GDB**：
>
> - 在命令行中输入 `gdb` 后跟上可执行文件的路径，或者直接在 GDB 中加载可执行文件。示例：`gdb my_program`
>
> **在 GDB 中设置断点**：
>
> - 可以通过在特定代码行上设置断点来中断程序执行。示例：`break line_number` 或 `break function_name`
>
> **运行程序**：
>
> - 在 GDB 中输入 `run` 或 `r` 命令来运行程序。
>
> **逐步执行程序**：
>
> - 使用 `step` 或 `s` 命令逐步执行程序，逐行执行代码。
>
> **查看变量值**：
>
> - 使用 `print variable_name` 命令来查看变量的值。
>
> **查看堆栈**：
>
> - 可以使用 `backtrace` 或 `bt` 命令查看函数调用堆栈。
>
> **观察程序状态**：
>
> - 在程序执行时，可以随时查看变量的值、内存内容等，以帮助定位问题。
>
> **控制程序执行**：
>
> - 可以使用 `continue` 命令继续程序执行，`finish` 命令让程序执行到当前函数结束等。
>
> **退出 GDB**：
>
> - 在调试结束后，可以使用 `quit` 或 `q` 命令退出 GDB。

#### 反汇编

```c
//反汇编目标程序,会生成反汇编文件.d
objdump -d hello.o
```

![image-20241118110806345](https://lsky.ooooyasumi.com/i/2024/11/18/673aaf97db0d6.png)

可以注意到：

![image-20241118111719522](https://lsky.ooooyasumi.com/i/2024/11/18/673ab1c140eea.png)

### 关于格式的注解

这是一个.s汇编文件的全部内容：

![image-20241118164045048](https://lsky.ooooyasumi.com/i/2024/11/18/673afd8eb4dfc.png)

以“.”开头的行是知道汇编器和连接器的伪指令，不是代码中的实际内容，所以在教学中我们以这样的形式展示汇编代码：

![image-20241118164153864](https://lsky.ooooyasumi.com/i/2024/11/18/673afdd373f90.png)

### 汇编语言

在汇编语言中，只有**数字**作为数据类型。比如一个int是一个4字节的数字，一个char是一个1字节的数字。也不区分是否是符号数。结构体、数组、指针的本质也是数字……

汇编指令的大小在**1-15字节**之间。

下面给出一个c语言指令转换为汇编代码的示例：

![image-20241118165915290](https://lsky.ooooyasumi.com/i/2024/11/18/673b01e736ae1.png)

将t的地址赋值给dest。就是将寄存器中的一个数的地址的数字，传送到另一个寄存器另一个位置完成这项指令。所以这条指令被简单地编码成了3字节的汇编指令。

### 寄存器（Register）

x86-64机器每个CPU包含一组**16个**储存64位值的**通用目的寄存器**。也叫做整数寄存器。

![image-20241118171045819](https://lsky.ooooyasumi.com/i/2024/11/18/673b0499c1ea6.png)

可以看到这就是16个寄存器的样子。每个寄存器都有一个**特定的，固定不变的名字**。并且每个寄存器可以再细分，可以调用它的低32位、16位和8位。

而这些寄存器的名称是因为历史遗留问题，导致他们有千奇百怪的命名。在IA32与8086时代这些寄存器的命名和他们的功能有关。而现在这些命名都被打乱了，没有特定含义。

而在这16个寄存器中，有8个由字母表示，8个由数字表示。

其中最特别的寄存器是%rsp，是**栈指针**。用来指明运行时栈的结束位置。其他的寄存器用法更加灵活。

### 操作数指示符

大多数指令有一个或者多个操作数（operand）

汇编中有三种操作数：

- 立即数（immediate）：表示常数值。书写方法$后面跟整数。
- 寄存器：表示寄存器中的值，可以是1、2、4、8字节。

注：我们书写寄存器的表示方法：

![image-20241118173409764](https://lsky.ooooyasumi.com/i/2024/11/18/673b0a13db004.png)

- 内存引用：根据我们计算出来的地址访问内存位置。

注：我们表示内存的方法：

![image-20241118173540109](https://lsky.ooooyasumi.com/i/2024/11/18/673b0a6de72ca.png)

#### 内存引用的寻址模式

![image-20241118173817207](https://lsky.ooooyasumi.com/i/2024/11/18/673b0b0cd498e.png)

上表给出了多种寻址模式，就是汇编中的指令**调用操作数的方法**。

### 数据传输指令（mov）

mov指令一共有五种

![image-20241118174452817](https://lsky.ooooyasumi.com/i/2024/11/18/673b0c96b718d.png)

其中的movq指令有五种传输路径。最特别的是不能从内存传输到内存使用单条指令执行。

![image-20241118175957271](https://lsky.ooooyasumi.com/i/2024/11/18/673b10200ae93.png)

![image-20241118180129602](https://lsky.ooooyasumi.com/i/2024/11/18/673b107bdee79.png)

这个例子就是使用了四个movq指令执行的。 

### 加载有效地址指令（lea）

load effective address 加载有效地址

是从内存读取地址存到寄存器。

leaq Src,Dest 将前面的内存中数据的**地址**放到后面的**寄存器**中。和movq的区别就是mov是将前面的数据放到后面的数据中，移动的是**数据**。

### 算数操作（Arithmetic Operations）

![image-20241119000134101](https://lsky.ooooyasumi.com/i/2024/11/19/673b64e05b09f.png)

![image-20241119000333038](https://lsky.ooooyasumi.com/i/2024/11/19/673b65564308f.png)


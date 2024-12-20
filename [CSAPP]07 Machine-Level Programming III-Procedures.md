## 过程：前言

### ABI

**Application Binary Interface（ABI）**，应用程序二进制接口。ABI定义了应用程序和操作系统之间或应用程序和硬件之间的接口标准，包括数据结构、寄存器用法、函数调用约定等内容。

这是机器级别的程序接口。基本上所有操作系统都会认同这套接口，在这套统一标准上发展自己的标准。

### 过程机制

- 转移控制（passing control）：如何开始一个函数，如何返回结果
- 数据传送（passing data）
- 管理局部存储（managing local data）

## 过程

### 运行时栈

%rsp寄存器（**栈指针**）会储存栈顶指针。

![image-20241120110537984](https://lsky.ooooyasumi.com/i/2024/11/20/673d520306c1e.png)

栈图是**倒着画**的，底下是栈顶。栈越堆越高，**地址越来越低**。

#### push

```assembly
pushq Src
```

压栈操作，单操作数。先读取Src的数据，再递减栈指针，再存放数据。

#### pop

```assembly
popq Dest
```

弹栈操作，单操作数。先读取数据，再增加栈指针，再将数据存放到Dest。**Dest必须是寄存器！**

注意：增加栈指针释放这个数据，只增加了栈指针，数据没有被抹去，只是不是栈的一部分了。

### 转移控制

转移控制的指令：

![image-20241120112342929](https://lsky.ooooyasumi.com/i/2024/11/20/673d563f9c8a1.png)

调用和返回使用了栈。

当我们call时，我们会压入栈一个**返回指针**。然后跳转到标签。

使用ret则相反，先弹栈，再跳转。

注：ret和rep可以看作为同一指令。callq和retq也与call与ret相同。

call和ret的流程：

![image-20241120114842084](https://lsky.ooooyasumi.com/i/2024/11/20/673d5c1b1543a.png)

注意：**%rip**是x64中的**程序计数器**（PC），其中存储了下一条给出的指令。

call：将%rsp栈指针递减，压入%rip的**下一条指令**（这个call指令的下一条指令）作为返回地址。

ret：将栈指针递增，弹栈，跳转到返回地址的代码。

### 数据传送

函数的前六个参数被这六个寄存器保存。（顺序很乱）

![image-20241120120008056](https://lsky.ooooyasumi.com/i/2024/11/20/673d5ec92aa30.png)

注：这里列的寄存器只能处理参数类型是整数或者指针类型。浮点数或其他数据类型由其他寄存器处理。

![image-20241120121513729](https://lsky.ooooyasumi.com/i/2024/11/20/673d6252a98e9.png)

根据参数大小更改参数存储的寄存器。

如果超出了6个参数怎么办？**多的参数会被放到栈上**。

在栈中先压入参数，从最后一个参数开始往前压，再压入返回地址。

函数的返回值由**%rax**保存。

​           ![image-20241120120138243](https://lsky.ooooyasumi.com/i/2024/11/20/673d5f2311ed4.png) 

### 管理局部存储

#### 栈上的局部存储

##### 栈帧

在程序执行过程中，每次函数调用都会创建一个栈帧，用于存储该函数的**局部变量、参数值、返回地址**以及其他与函数调用相关的信息。有些情况也不会分配栈帧。如下列情况：

![image-20241120212620351](https://lsky.ooooyasumi.com/i/2024/11/20/673de37d2fe8c.png)

栈帧有两个指针：

![image-20241120211100726](https://lsky.ooooyasumi.com/i/2024/11/20/673ddfe61964f.png)

%rbp基指针/帧指针 %rsp栈指针。%rbp是可选的，%rsp是必要的。

下面是一个函数运行的例子，yoo()调用who()，who调用多个amI()，amI()递归调用。

![image-20241120205805423](https://lsky.ooooyasumi.com/i/2024/11/20/673ddcdfec935.png)

![image-20241120210319865](https://lsky.ooooyasumi.com/i/2024/11/20/673dde1ac8574.png) 

这个就是一个一个压入栈的栈帧。

##### 递归

下面是一个递归的大例子：

![image-20241120220844874](https://lsky.ooooyasumi.com/i/2024/11/20/673ded6fd69c1.png)

![image-20241120222512721](https://lsky.ooooyasumi.com/i/2024/11/20/673df14b26b7b.png)

下面对第二个图的汇编代码按行说明：

1. 栈指针下移16，开辟两个空间
2. 将15213存到第一个空间中
3. 将3000放到寄存器%esi中（第二个参数）
4. 将栈中的15213的地址放到寄存器%rdi中（第一个参数）
5. 上面的操作创建好了要传递给incr函数的两个参数，调用incr函数。注意这里incr函数在编写的时候是**直接改变了原来15213地址存放的数据**，避免了新增栈空间造成的内存浪费。
6. 将15213作为返回值返回
7. 弹栈，释放栈空间

#### 寄存器中的局部存储

![image-20241120223130499](https://lsky.ooooyasumi.com/i/2024/11/20/673df2c4c4038.png)

在寄存器中存储数据会产生某些问题：嵌套使用的函数可能会覆写外层函数**正在使用的寄存器**，导致外层函数的寄存器无法使用。

所以就产生了一个新的约定：**寄存器使用惯例**来避免这个问题。这个惯例也在ABI中存在。

我们规定一个过程调用另一个过程的叫做**调用者**（caller）

被调用的叫**被调用者**（callee）

![image-20241120223445879](https://lsky.ooooyasumi.com/i/2024/11/20/673df386caf8f.png)

我们有两种方法可以管理寄存器：

- 调用者保存（caller saved）

调用者保存时，在**函数调用前**就直接将寄存器中的值保存了。提前假设了这个寄存器就会被其他过程使用。

- 被调用者保存（callee saved）

被调用者保存时，当**被调用者函数要使用调用者寄存器时**，才保存这个值。然后在这个函数返回之前，他会恢复到**调用之前**的状态。

下面是调用者保存和被调用者保存寄存器：

我们会发现除了返回值寄存器、6个参数寄存器、栈指针。其他的寄存器都是调用者或被调用者寄存器。

![image-20241120224427773](https://lsky.ooooyasumi.com/i/2024/11/20/673df5ce7ef21.png)

![image-20241120225943798](https://lsky.ooooyasumi.com/i/2024/11/20/673df96350769.png)

![image-20241120231229314](https://lsky.ooooyasumi.com/i/2024/11/20/673dfc5e3ab7e.png)

下面是一个被调用者保存的例子：

![image-20241120232457145](https://lsky.ooooyasumi.com/i/2024/11/20/673dff4a36d7f.png)

保存被调用者寄存器中值的方法是：将这个值压入栈中

> 引用自：https://blog.csdn.net/Edidaughter/article/details/122334074

 ### 递归过程

递归的过程可以参考CSAPP P174

良好的局部数据管理保证了递归的执行：

- **栈上的局部数据：栈帧**：每次调用函数都会创建一个栈帧，深层次调用的时候不会丢失前面的东西。并且递归返回的时候会依次返回。
- **寄存器中的局部数据：调用者和被调用者保存**：这使得一个过程调用另一个过程时，数据不会丢失，会很好的保存。

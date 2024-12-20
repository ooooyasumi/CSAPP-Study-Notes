## 二进制实数的表示

![image-20241116151931576](https://lsky.ooooyasumi.com/i/2024/11/16/6738478578abe.png)

例如：

5 3/4 -> 101.11

1 7/16 -> 1.0111

- 小数点向右移一位，这个**浮点数乘2**。反之向左移一位除二。
- 数0.11111……是无限接近于1的浮点数。用1.0-ε表示这种数。ε取决于小数点后的位数。

## 浮点数标准

在1985年以前浮点数的表示是一场灾难。在1985年，IEEE（电气工程师协会）统一了浮点数标准。被称为IEEE754标准。

![image-20241116154937124](https://lsky.ooooyasumi.com/i/2024/11/16/67384e932edac.png)

### 浮点数的结构

浮点数的表示公式：
$$
（-1）^sM2^E
$$


![image-20241116160226649](https://lsky.ooooyasumi.com/i/2024/11/16/6738519547973.png)

**s**：符号位

**exp**：阶码，表示E但不等同于E，可以看作为一个无符号整形

**frac**：尾数，表示M但不等同于M

![image-20241116160443802](https://lsky.ooooyasumi.com/i/2024/11/16/6738521dad009.png)

IEEE提供了两种标准的浮点数。

- **E = Exp - Bias** 

​	Exp = 指数

​	Bias（偏移值）= 2<sup>k-1</sup> - 1  k是阶码的位数

​	例：在单精度浮点数中，有8位阶码，所以应该减去127的偏移值。

​	Exp的范围(以float作为演示)：**0 ≤ Exp ≤ 255**

​	E的范围：**-127 ≤  E ≤ 128**

- **M = 1.xxx……x**

​	xxx……x是尾数域的数字，注意M会在前面加一个1. ，未填满对应位数的在后面补0

### 浮点数的表示

#### 规格化浮点数

![image-20241116170141049](https://lsky.ooooyasumi.com/i/2024/11/16/67385f8368ec0.png)

上图为一个表示的例子

十进制小数转换为浮点数步骤：

1. 先将十进制小数转换为二进制小数，并且用科学计数法表示
2. 取小数点后面的数为尾数
3. 用指数加上偏移值求得阶码
4.  将符号位、阶码、尾数拼接起来

规格化浮点数的Exp与E的范围：

**1 ≤ Exp ≤ 2<sup>n</sup>-2** n为阶码的位数

**1-Bias ≤  E ≤ 2<sup>n</sup>-2-Bias**

#### 非规格化浮点数

因为尾数自带一个隐含的1，当我们想要表示接近0的数不好表示。

这个时候：

**s**：不变

**exp**：**全为0** 此时**E = 1 - Bias**

**frac**：0.xxx……x 不包含隐含的1

使用非规格化浮点数有几点好处：

- 避免了尾数>1不能表示0.xx……
- 当符号位、阶码、尾数全部等于0，那么这个浮点数就为0。和int0对应。

但是有一个奇怪的地方是：符号位为0或1，其他全为0分别表示了0和-0。会有**正0和负0**的产生！

### 特殊的浮点数

 无穷大：当阶码为全1，尾数全为0时

![image-20241116173711691](https://lsky.ooooyasumi.com/i/2024/11/16/673867d342d7e.png)

有正无穷大和负无穷大。对无穷大做任何操作都是无穷大的。

NaN：阶码全1，尾数不为0

Not a Number，是一个没有结果的数

![image-20241116174202296](https://lsky.ooooyasumi.com/i/2024/11/16/673868eb2a5eb.png)

浮点数的可视化：

![image-20241116174246969](https://lsky.ooooyasumi.com/i/2024/11/16/67386917cb0f4.png)

## 分析浮点数

以一个小的浮点数举例，他的结构如下

![image-20241116174503918](https://lsky.ooooyasumi.com/i/2024/11/16/673869a0a1316.png)

简单分析：

这个小的浮点数0 ≤ Exp ≤ 15，-7 ≤ E ≤ 8

![image-20241116174751434](https://lsky.ooooyasumi.com/i/2024/11/16/67386a505a29f.png)

 这是他能表示的所有浮点数

当Exp取最大值和最小值时为特殊情况。当Exp为最小值0时为非标准浮点数，当Exp为最大值时为无穷大或者NaN。

所以不会出现E或者Exp取得最小值的情况，因为非标准数的E**被强制设置为1-Bias**。**偏移值**的设计使得非标准数过度到标准数是非常平滑的！当尾数加1刚好重置为0，阶码变为1，从非标准数变成标准数。

![image-20241116181656117](https://lsky.ooooyasumi.com/i/2024/11/16/6738711958cf3.png)

**上图是精彩的过度瞬间!**

![image-20241116183637695](https://lsky.ooooyasumi.com/i/2024/11/16/673875b9f076e.png)

上图为浮点数表示的数字在数轴上的可视化

![image-20241116183728711](https://lsky.ooooyasumi.com/i/2024/11/16/673875ea7563a.png)

## 浮点数的运算

### 舍入（Rounding）思想

#### 十进制情况

IEEE754规定了四种舍入

- 向零舍入
- 向下舍入
- 向零舍入
- 向偶数舍入（默认）

![image-20241116184613036](https://lsky.ooooyasumi.com/i/2024/11/16/673877f7e03a1.png)

向偶数舍入，就是舍入上或者下，使得**最低有效数字为偶数**。当被舍的数小于中间数，向下舍入。当它大于中间数，向上舍入。**当它等于中间数，向偶数舍入**。

采用这种舍入方式也有统计学原理考究，因为每一个数都有50%的概率被舍入上或者下，所以不会造成结果向某一种方向偏离。

#### 二进制情况

正如上面所说的向偶数舍入，二进制的中间值是**1000……**

所以当被舍入的数大于或者小于1000……，我们可以直接向上或者向下舍入。当它等于1000……时，向偶舍入。

例子：

![image-20241116192957265](https://lsky.ooooyasumi.com/i/2024/11/16/6738823864b01.png)

### 浮点数乘法

$$
（-1）^{s1}M1•2^{E1}×（-1）^{s2}M2•2^{E2}
$$

![image-20241116193713132](https://lsky.ooooyasumi.com/i/2024/11/16/673883ea47e9d.png) 

- 如果Ｍ≥２，将Ｍ又移动，确保Ｅ在1-2的范围内
- 如果Ｅ超出范围，溢出到无穷大
- 舍入Ｍ，修正尾数

### 浮点数运算的特性

![image-20241116194431486](https://lsky.ooooyasumi.com/i/2024/11/16/673885a138f3d.png)

![image-20241116194807641](https://lsky.ooooyasumi.com/i/2024/11/16/6738867c01154.png)

最需要注意：浮点数运算**不满足结合律**。

## C语言中的浮点数

### 类型转换

#### double/float -> int

当一个float或者double要转换为int类型时，整数部分会被留下，小数部分直接被抛弃（向0舍入）。

当浮点数为NaN或者无限大时，转换为TMin

#### int -> double

Int 32bit,double的frac域有53位，所以不会丢失任何东西。

#### int -> float

int的位数比float的frac域位数多，所以要做舍入，会丢失数据。
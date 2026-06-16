---
title: IEEE754 规范
date: 2026-06-16 18:59:34
author: 网络
tags: [计算机硬件]
categories: 组成原理
status: draft
published: false
---
# 程序员数学之 IEEE754 规范（整理版）
通用版：https://www.yuque.com/u2333-ohan5/gbpqhm/readme?singleDoc#

> 来源：博客园 sureZ_ok《程序员数学之-IEEE754规范》。本文是学习整理版，保留核心知识点，并将图片放入 `images/` 目录，Markdown 中使用相对路径引用。

## 1. 定点数与浮点数

计算机表示小数主要有两类方法：**定点数**和**浮点数**。

**定点数（Fixed Point Number）**：小数点位置固定。例如常见的 `Qm.n` 表示法，一般可以理解为：

```text
1 个符号位 + m 个整数位 + n 个小数位
```

优点是计算速度快；缺点是表示范围较小，不适合同时表示特别大和特别小的数。

**浮点数（Floating Point Number）**：使用类似科学计数法的形式表示小数。优点是表示范围广、精度较高；缺点是计算过程比定点数复杂。

## 2. IEEE754 规范

IEEE754 规定了常见浮点数的存储格式，例如 `float16`、`float32`、`float64`。它们都可以拆成三部分：

```text
S：符号位 Sign
E：阶码 / 指数 Exponent
M：尾数 / 小数部分 Mantissa / Fraction
```

### 2.1 浮点数存储格式

#### float16：半精度浮点数

![float16 layout](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/01_float16_layout.png)

`float16` 一共 16 bit：

```text
1 bit 符号位 + 5 bit 阶码 + 10 bit 尾数
```

#### float32：单精度浮点数

![float32 layout](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/02_float32_layout.png)

`float32` 一共 32 bit：

```text
1 bit 符号位 + 8 bit 阶码 + 23 bit 尾数
```

#### float64：双精度浮点数

![float64 layout](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/03_float64_layout.png)

`float64` 一共 64 bit：

```text
1 bit 符号位 + 11 bit 阶码 + 52 bit 尾数
```

## 3. 规格数、非规格数与特殊数

IEEE754 主要根据阶码 `E` 的状态，把浮点数分成三类：

| 类型                      | E 的状态               | M 的状态             | 含义                   |
| ------------------------- | ---------------------- | -------------------- | ---------------------- |
| 规格数 Normal Number      | E 不是全 0，也不是全 1 | 任意                 | 正常表示绝大多数浮点数 |
| 非规格数 Subnormal Number | E 全 0                 | 通常非 0，也可表示 0 | 表示 0 附近很小的数    |
| 特殊数 Special Number     | E 全 1                 | M=0 或 M≠0           | 表示 Infinity 或 NaN   |

### 3.1 规格数 Normal Number

![normal number](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/04_normal_number.png)

规格数的特点是：**阶码 E 不为 0，也不为全 1**。

它的计算公式是：

![normal formula](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/05_normal_formula.png)

其中 `bias` 是偏置值：

| 类型    | 阶码位数 | bias |
| ------- | -------: | ---: |
| float16 |        5 |   15 |
| float32 |        8 |  127 |
| float64 |       11 | 1023 |

比如对于 `float32` 的 `-3.456`：

```text
符号位：负数，所以 S = 1
3.456 ≈ (1 + 0.728) × 2^1
所以 M ≈ 0.728
真实指数 = 1
存储阶码 E = 1 + 127 = 128
```

注意：规格数不能表示 0，也不能很好地表示非常靠近 0 的数。

### 3.2 非规格数 Subnormal Number

![subnormal number](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/07_subnormal_number.png)

非规格数的特点是：**阶码 E 全 0**。它主要用于表示 0 以及非常接近 0 的小数。

它的计算公式是：

![subnormal formula](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/08_subnormal_formula.png)

这里要特别注意：非规格数的指数是 `1 - bias`，而不是 `0 - bias`。

规格数和非规格数在数轴上的关系大致如下：

![number axis](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/06_number_axis.png)

可以这样理解：

```text
规格数：负责表示“正常范围”的数
非规格数：负责填补 0 附近特别小的数
特殊数：负责表示无穷大和非法结果
```

### 3.3 特殊数 Special Number

特殊数的特点是：**阶码 E 全 1**。

#### Infinity：无穷大

![infinity](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/09_infinity.png)

当：

```text
E 全 1，M = 0
```

表示正无穷或负无穷。符号位 `S` 决定是 `+Infinity` 还是 `-Infinity`。

#### NaN：非数值

![nan](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/10_nan.png)

当：

```text
E 全 1，M ≠ 0
```

表示 `NaN`，也就是 `Not A Number`。常见于非法数学运算，比如某些情况下的 `0/0`。

## 4. 浮点数表示范围与精度

| 类型    | 近似表示范围               | 精度理解             |
| ------- | -------------------------- | -------------------- |
| float16 | 约 `(-65536, 65536)`       | 约 3～4 位有效数字   |
| float32 | 约 `(-3.4e38, 3.4e38)`     | 约 6～7 位有效数字   |
| float64 | 约 `(-1.79e308, 1.79e308)` | 约 15～16 位有效数字 |

这里的“精度”可以先粗略理解为：

```text
尾数位越多，小数部分能分得越细，有效数字越多。
```

例如：

```text
float16 的尾数是 10 位，所以有效数字较少。
float32 的尾数是 23 位，所以有效数字更多。
float64 的尾数是 52 位，所以有效数字最多。
```

## 5. 浮点数之间相互转换

以 `float16` 转 `float32` 为例，本质不是简单“数值复制”，而是要按照 IEEE754 格式重新扩展：

```c
float16_t val = 1.5;
printf("val = %f
", (float32_t)val);
```

在转换过程中，需要处理：

```text
符号位 S：从 float16 放到 float32 对应位置
阶码 E：从 5 位扩展为 8 位，并重新计算 bias
尾数 M：从 10 位扩展为 23 位，通常向高位对齐并补 0
```

示意代码：

```c
float32_t f16Tof32(float16_t halfFloat) {
    union {
        uint32_t Uint32;
        float32_t F32;
    } val;

    val.Uint32 = *((uint32_t *)(&halfFloat));

    // S + E + M
    val.Uint32 = ((val.Uint32 & 0x8000) << 16) |
                 (((((val.Uint32 >> 10) & 0x1f) - 15 + 127) & 0xff) << 23) |
                 ((val.Uint32 & 0x03FF) << 13);

    return val.F32;
}
```

这段代码的核心逻辑是：

```text
float16 的 bias 是 15
float32 的 bias 是 127
所以指数部分要从 “E16 - 15” 转成 “真实指数”，再加上 127 存入 float32
```

## 6. 快速计算 2 的指数次幂

IEEE754 的结构可以用来快速构造 `2^x`。

对于 `float32`：

![power2](https://raw.githubusercontent.com/cmijohnson/cmijohnson-imagebed/main/11_power2.png)

因为：

```text
2^x = 1.0 × 2^x
```

所以可以设置：

```text
S = 0
M = 0
E = x + 127
```

示意代码：

```c
uint32_t power2(uint32_t x) {
    union {
        uint32_t Uint32;
        float_t F32;
    } val;

    val.Uint32 = (127 + x) << 23;
    return (uint32_t)val.F32;
}
```

同理，也可以通过读取浮点数中的阶码来快速近似计算 `log2`：

```c
uint32_t log2(float_t y) {
    union {
        uint32_t Uint32;
        float_t F32;
    } val;

    val.F32 = y;
    return ((val.Uint32) >> 23) - 127;
}
```

## 7. 考试速记版

### 7.1 三段结构

```text
浮点数 = S + E + M
S：正负
E：数量级 / 指数
M：精细程度 / 有效数字
```

### 7.2 三种状态

```text
E 不是全 0，也不是全 1：规格数
E 全 0：非规格数 / 0 附近
E 全 1：特殊数
```

### 7.3 特殊数判断

```text
E 全 1，M = 0：Infinity
E 全 1，M ≠ 0：NaN
```

### 7.4 bias 记忆

```text
float16：bias = 15
float32：bias = 127
float64：bias = 1023
```

### 7.5 规格数公式

```text
V = (-1)^S × (1.M) × 2^(E - bias)
```

### 7.6 非规格数公式

```text
V = (-1)^S × (0.M) × 2^(1 - bias)
```

## 8. 一句话理解

IEEE754 就是在用三块信息表示一个小数：

```text
S 决定正负，E 决定大小级别，M 决定这个数在该级别里的精细位置。
```


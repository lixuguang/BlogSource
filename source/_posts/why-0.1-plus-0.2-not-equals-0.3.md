---
title: 为什么 0.1 + 0.2 != 0.3
date: 2020-01-03 12:10:00
categories: 
	- 前端技术
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# Proxy
因为 JS 采用 IEEE 754 双精度版本（64位），并且只要采用 IEEE 754 的语言都有该问题。

我们都知道计算机表示十进制是采用二进制表示的，所以 0.1 在二进制表示为

```
// js代码

// (0011) 表示循环
0.1 = 2^-4 * 1.10011(0011)
```
那么如何得到这个二进制的呢，我们可以来演算下

小数算二进制和整数不同。乘法计算时，只计算小数位，整数位用作每一位的二进制，并且得到的第一位为最高位。所以我们得出 `0.1 = 2^-4 * 1.10011(0011)`，那么 `0.2` 的演算也基本如上所示，只需要去掉第一步乘法，所以得出 `0.2 = 2^-3 * 1.10011(0011)`。

回来继续说 `IEEE 754` 双精度。六十四位中符号位占一位，整数位占十一位，其余五十二位都为小数位。因为 `0.1` 和 `0.2` 都是无限循环的二进制了，所以在小数位末尾处需要判断是否进位（就和十进制的四舍五入一样）。

所以 `2^-4 * 1.10011...001` 进位后就变成了 `2^-4 * 1.10011(0011 * 12次)010` 。那么把这两个二进制加起来会得出 `2^-2 * 1.0011(0011 * 11次)0100` , 这个值算成十进制就是 `0.30000000000000004`

下面说一下原生解决办法，如下代码所示
```
// js代码

parseFloat((0.1 + 0.2).toFixed(10))
```
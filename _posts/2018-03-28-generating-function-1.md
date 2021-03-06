---
layout: post
title: '多项式与生成函数'
date: 2018-03-28
author: Deluxurous
cover: '/assets/img/mathematics.png'
categories: OI
tags: 数学 数论
excerpt: '有关生成函数的一些问题。'
---

**以下大部分资料来源于集训队论文与讲课课件。**

# 多项式

定义一个关于 $x$ 个多项式 $A(x)$：

$$\displaystyle A(x) = \sum_{i=0}^{n-1} a_i x^i$$

其中系数 $a_i$ 均为某个环 $R$ 中的元素。这些多项式组成了多项式环 $R[i]$ 。

非零多项式 $A(x)$ 的**次数**定义为其最高次项的次数，记作 $\mathrm{deg} A(x)$ 。

## 多项式运算

### 加减法

$$A(x) \pm B(x) = \sum_{i=0}^{n-1} (a_i \pm b_i) x^i$$

运算复杂度 $O(n)$ 。

### 乘法

$$A(x)B(x) = \sum_{i=0}^{n-1} (\sum_{j+k=i} a_jb_k) x^i$$

朴素做法 $O(n^2)$ ，无法接受。

考虑分治乘法：

$$(Ax^m + B)(Cx^m + D) = ACx^{2m} + ((A+B)(C+D) - AC - BD)x^m + BD$$

时间复杂度 $O(n^{\log_2{3}})$

更快的做法是利用**快速傅里叶变换 (Fast Fourier Transform)**，将多项式在系数表达与点值表达间化费 $O(n \log n)$ 的时间互相转换。转化为点值表达后就可以 $O(n)$ 相乘。总复杂度 $O(n \log n)$。

快速傅里叶变换通常使用复数进行运算，精度有时会存在问题。那么在模意义下的乘法应如何计算呢？我们可以对 FFT 稍稍进行一些变化，变为使用**快速数论变换 (Number Theory Transform)** 。实现时，单位根应取模数 $P$ 的原根 $g$ 的 $\frac{P-1}{\displaystyle 2^k}$ 次方。当然，这时的模数 $P$ 需要满足 $P = x \times 2^y + 1$ ，并使其中的 $y$ 大于所需的 $k$ （也就是要大于需要 NTT 的多项式次数）。可是如果模数不满足这种情况呢？一种做法是三模数 NTT ，也就是找三个满足以上性质的模数做三遍 NTT 分别求出结果，再用 CRT 取得在所需模数下的值。然而 @orbitingflea 讲了一种更高明的做法：拆系数 FFT 。以下是 zrf 课件原话：

> 设 $M_0 = 2^{15} \approx \sqrt{M}$，把要做卷积的两个序列写成 $x M_0 + y$ 的形式，并设 $a = a_2 \cdot M_0 + a_1, b = b_2 \cdot M_0 + b_1$ 。求出 $a_1, a_2, b_1, b_2$ 并 DFT，之后两两相乘。然后我们得到了四个序列，其中 $a_1b_1$ 对答案的贡献是 $1$ ，$a_1b_2 + a_2b_1$ 对答案的贡献是 $M_0$ ，$a_2b_2$ 对答案的贡献是 $M_0^2$ 。分别求出这三个序列的 DFT，分别 IDFT 并四舍五入到整数，即可得到答案序列。

%%%orbitingflea

### 除法

对于多项式 $A(x), B(x)$ ，存在唯一的 $Q(x), R(x)$ 满足 $A(x) = Q(x)B(x) + R(x)$ ，其中 $\mathrm{deg} R < \mathrm{deg} B$ 。其中 $Q(x)$ 为 $A(x)$ 除以 $B(x)$ 的**商**，$R(x)$ 为 $A(x)$ 除以 $B(x)$ 的**余数**。可以写作：

$$R(x) \equiv A(x) \mod B(x)$$

计算方法先鸽着233333

<hline/>

# 形式幂级数

一个多项式只有有限项的系数非零。若将其扩展为无限项，即可得到**形式幂级数**：

$$A(x) = \sum_{i \geq 0} a_i x^i$$

在形式幂级数中，$x$ 从来不指定一个数值，且对收敛和发散的问题不感兴趣，感兴趣的是系数序列。可以把 $x$ 看做是一个符号，将其代入求值是没有意义的。

同时，这些形式幂级数构成了形式幂级数环 $R[[x]]$ 。

通常我们会在模 $x^n$ 意义下进行运算，即只保留次数小于 $n$ 的项，将多余的项舍去。

## 形式幂级数运算

### 加减乘

形同多项式。

$$A(x) \pm B(x) = \sum_{i \geq 0} (a_i \pm b_i) x^i$$

$$A(x)B(x) = \sum_{i \geq 0} (\sum_{j+k=i} a_jb_k) x^i$$

### 逆元


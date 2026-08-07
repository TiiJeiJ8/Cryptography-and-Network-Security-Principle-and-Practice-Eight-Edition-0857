---
title: 第 2 章 数论基础 - Miller-Rabin 两条核心性质笔记
aliases:
  - 密码编码学与网络安全 第 2 章 Note
  - 第二章 数论基础 Miller-Rabin 两条核心性质笔记
tags: [miller_rabin, primality_test, number_theory, prime]
category: note
source: 密码编码学与网络安全 原理与实践 第八版
source_pdf: Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf
chapter: 2
document_type: note-note
created: 2026-07-30
updated: 2026-07-30
---

# Miller-Rabin 算法两大核心性质

## 一、两大性质
设 $p$ 为奇素数，$p-1=2^k \cdot q$，其中 $q$ 为奇数，$1<a<p$。
### 性质1：平方根性质
同余方程 $x^2 \equiv 1 \pmod{p}$ 仅有两个解：
$$x\equiv 1\pmod{p},\quad x\equiv -1\pmod{p}$$

### 性质2：平方数列性质
考察数列（后一项 = 前一项平方，末项为 $a^{p-1}$）：
$$a^q,\ a^{2q},\ a^{4q},\dots,a^{2^k q} \pmod{p}$$
若 $p$ 为素数，则仅存在两种合法情况：
1. 首项满足：$a^q \equiv 1 \pmod{p}$；
2. 数列中存在某一项满足：$a^{2^{j-1}q}\equiv -1 \pmod{p}$。

---

## 二、性质证明
### 性质1 证明
$$
x^2\equiv 1 \pmod{p} \iff (x-1)(x+1)\equiv 0 \pmod{p}
$$
$p$ 是素数，素数整除乘积则必整除其中一个因子：
$p\mid (x-1)$ 或 $p\mid (x+1)$
得到：$x\equiv 1$ 或 $x\equiv -1 \pmod{p}$。

### 性质2 证明
由费马小定理：$a^{p-1}=a^{2^k q}\equiv 1\pmod{p}$，数列最后一项恒等于1。
- 若首项 $a^q \equiv 1$，所有平方项均为1，条件成立；
- 若首项不为1，不断向前开平方。根据**性质1**，平方结果为1时，它的平方根只能是 $-1$。
因此数列中途必然出现 $-1$。

---

## 三、两条性质对 Miller-Rabin(MR) 算法的作用
1. **提供素数必要条件，合数判定标准**
素数必须满足数列性质；若待测数 $n$ 在迭代平方过程中，既未出现 $1$，也未出现 $-1$，**一定是合数**。

2. **弥补费马素性测试的漏洞**
费马测试仅检验 $a^{n-1}\equiv1\pmod{n}$，无法识别卡迈克尔数、强伪素数。
MR依靠平方根性质逐层开平方，增加中间校验，有效筛除伪素数。

3. **定义算法终止规则**
- 计算 $x = a^q \bmod n$，若 $x=1$，本轮测试通过；
- 循环平方，一旦 $x=n-1$（等价于 $-1 \pmod{n}$），测试通过；
- 循环结束仍未出现 $-1$，直接判定 $n$ 为合数。

> 总结：两条性质构成 Miller-Rabin 素性测试整套判定逻辑的理论基础。
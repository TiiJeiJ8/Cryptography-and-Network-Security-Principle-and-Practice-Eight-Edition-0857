---
title: 第 2 章 数论基础 - CRT 求解同余方程组
aliases:
  - 密码编码学与网络安全 第 2 章 Note
  - 第二章 数论基础 CRT 求解同余方程组
tags:
  - course
  - cryptography
  - number_theory
  - note
  - "#prime_number"
  - "#中国剩余定理"
  - "#同余方程组"
category: note
source: 密码编码学与网络安全 原理与实践 第八版
source_pdf: Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf
chapter: 2
document_type: note-note
created: 2026-07-30
updated: 2026-07-30
---
# 一、CRT 两种学习视角

## 1）做题视角

CRT = **用来解同余方程组**

已知余数 → 求未知数 x（只用到单向）

**缺点**：只懂解方程，不懂密码学真实用途。

## 2）密码学/教材视角

CRT = **整数空间的等价拆分结构**

$$\mathbb Z_M \cong \mathbb Z_{m_1} \times \mathbb Z_{m_2} \dots \times \mathbb Z_{m_k}$$

在 **模数两两互质** 条件下：

**一个大数 ⇋ 唯一一组余数**（双向无损）

这是比“解方程”更本质、更重要的定义。

---

# 二、教材 CRT 核心两条定理

## 性质 1：双向一一映射（双射）

设 $M=m _1 m_ 2\cdots m_k$，模数两两互质。

1. 正向（拆分）：一个大数唯一对应一组余数

$$A \rightarrow (A\bmod m_1,\;A\bmod m_2,\dots)$$

2. 反向（重构）：一组余数唯一对应一个大数

$$(a_1,a_2\dots) \rightarrow A \in \mathbb Z_M$$

**反向重构 = 就是你学的解同余方程组**

## 性质 2：运算可完全拆分（密码学核心）

大数模 M 的加减乘幂，**等价于各余数分量独立运算后再合并**。

若：

$$A\leftrightarrow(a_1,a_2),\quad B\leftrightarrow(b_1,b_2)$$

则：

$$AB \bmod M \leftrightarrow (a_1b_1\bmod m_1,\;a_2b_2\bmod m_2)$$

**通俗理解：**

整体相乘再取模 = 拆分后分别相乘再合并

**这是 RSA 加速解密的核心原理**，普通解方程完全不学这条。

---

# 三、教材标准 CRT 公式

条件：$(m_1\dots m_k)$ 两两互质

$$M=\prod m_i,\quad M_i=\frac{M}{m_i}$$

定义基底系数：

$$c_i = M_i \cdot (M_i^{-1} \bmod m_i)$$

重构唯一大数：

$$A = \left(\sum a_i c_i \right) \bmod M$$

**这就是课本给的式 (2.16)(2.17)**

---

# 四、经典例题对照（贴合教材）

$$x\equiv3\pmod7,\;x\equiv4\pmod{13}$$

\($M=91,\;M _1=13,\;M_ 2=7$\)

逆元：$13^{-1}\equiv 6\pmod 7, \;7^{-1}\equiv 2\pmod{13}$

基底：$c _1=78, c_ 2=14$

$$A=(3\times78+4\times14)\bmod91 = 17$$

映射关系：$\boldsymbol{17 \leftrightarrow (3,4)}$

## 演示运算拆分

求 $17^2 \bmod 91$

拆分计算：$3^2\equiv 2\pmod 7,\;4^2\equiv 3\pmod{13}$

再 CRT 合并 $(2,3)$，结果 = $\boldsymbol{16}$

和直接计算 $17^2\bmod 91$ 完全一致。

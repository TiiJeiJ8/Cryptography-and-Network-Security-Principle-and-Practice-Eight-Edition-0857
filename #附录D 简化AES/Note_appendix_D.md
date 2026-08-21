---
title: 附录D 简化AES - 学习目标笔记
aliases:
  - 密码编码学与网络安全 附录D Note
tags: [simplified_aes, aes, finite_field, spn]
source: 密码编码学与网络安全 原理与实践 第八版
chapter: null
appendix: D
type: note
status: maintained
created: 2026-08-09
updated: 2026-08-09
---

# 附录D 简化AES

## 0. 内容总览

简化AES（S-AES）是圣塔·克拉拉大学 Edward Schaefer 教授开发的教育用加密算法，与 AES 性质结构相同但参数极小。教材强调：**深入了解 S-AES 可帮助读者更容易掌握 AES**。本附录包含三个附件：算术运算、列混淆函数详细推导及 S-AES 双轮完整计算示例。

### S-AES vs AES-128

| 参数 | S-AES | AES-128 |
|---|---|---|
| 明文分组 | 16 位 | 128 位 |
| 密钥 | 16 位 | 128 位 |
| 轮数 | 2 轮 | 10 轮 |
| 状态矩阵 | $2 \times 2$ nibble | $4 \times 4$ byte |
| 有限域 | $GF(2^4)$ | $GF(2^8)$ |
| 不可约多项式 | $x^4 + x + 1$ | $x^8 + x^4 + x^3 + x + 1$ |

操作单位为 **nibble**（4 位，半字节）。

## 1. S-AES 整体结构

![[attachments/fig_D_1.png|450]]

*图 D.1 S-AES 加密和解密*

**加密函数复合表示**：

$$A_{K_2} \circ \text{SR} \circ \text{NS} \circ A_{K_1} \circ \text{MC} \circ \text{SR} \circ \text{NS} \circ A_{K_0}$$

即：第 0 轮只做密钥加；第 1 轮执行完整四函数（NS → SR → MC → AK）；第 2 轮不含 MC（NS → SR → AK）。

**解密函数复合表示**：

$$A_{K_0} \circ \text{INS} \circ \text{ISR} \circ \text{IMC} \circ A_{K_1} \circ \text{INS} \circ \text{ISR} \circ A_{K_2}$$

### 1.1 数据结构

![[attachments/fig_D_2.png]]

*图 D.2 S-AES 数据结构*

状态矩阵是一个 $2 \times 2$ 的 nibble 矩阵（按列存储），密钥也类似组织：

| 标记 | 含义 |
|---|---|
| $S_{0,0}, S_{1,0}$ | 列 0（前 8 位明文） |
| $S_{0,1}, S_{1,1}$ | 列 1（后 8 位明文） |
| $K_0 = k_0 \dots k_{15}$ | 第 0 轮轮密钥（即原始密钥） |
| $K_1 = k_{16} \dots k_{31}$ | 第 1 轮轮密钥 |
| $K_2 = k_{32} \dots k_{47}$ | 第 2 轮轮密钥 |

![[attachments/fig_D_3.png|400]]

*图 D.3 S-AES 加密轮*

## 2. S-AES 四个基本操作

### 2.1 密钥加（AddKey）

16 位状态矩阵与 16 位轮密钥逐位异或。由于异或是自逆，密钥加的逆函数即自身。

![[attachments/fig_D_3-4_.png|350]]

*密钥加示例*

### 2.2 半字节代替（SubNibbles）

![[attachments/fig_D_4.png|450]]

*图 D.4 S-AES 变换（含 SubNibbles 和 MixColumns）*

查 S 盒表操作：nibble 的高 2 位选行，低 2 位选列。

**S 盒**：

| | 00 | 01 | 10 | 11 |
|---|---|---|---|---|
| **00** | 9 | 4 | A | B |
| **01** | D | 1 | 8 | 5 |
| **10** | 6 | 2 | 0 | 3 |
| **11** | C | E | F | 7 |

**逆 S 盒**：

| | 00 | 01 | 10 | 11 |
|---|---|---|---|---|
| **00** | A | 5 | 9 | B |
| **01** | 1 | 7 | 8 | F |
| **10** | 6 | 0 | 2 | 3 |
| **11** | C | 4 | D | E |

S 盒操作示例——输入 nibble `A`（1010，即行=10=2，列=10=2）：$S[2][2] = 0$，因此 `A` 映射为 `0`。

![[attachments/fig_D_4-5.png|350]]

*SubNibbles 变换示例*

S 盒是 S-AES/AES 唯一提供**非线性**的组件。

### 2.3 行移位（ShiftRows）

对状态矩阵的第 2 行进行 nibble 移位（第 1 行不变）：

$$\begin{pmatrix} S_{0,0} & S_{0,1} \\ S_{1,0} & S_{1,1} \end{pmatrix} \xrightarrow{\text{SR}} \begin{pmatrix} S_{0,0} & S_{0,1} \\ S_{1,1} & S_{1,0} \end{pmatrix}$$

逆行移位（ISR）将第 2 行移回原位。

### 2.4 列混淆（MixColumns）

![[attachments/fig_D_4-5_2.png|400]]

*MixColumns 示例*

在 $GF(2^4)$ 上执行矩阵乘法：

$$\begin{pmatrix} 1 & 4 \\ 4 & 1 \end{pmatrix} \begin{pmatrix} S_{0,0} & S_{0,1} \\ S_{1,0} & S_{1,1} \end{pmatrix} \bmod{(x^4+x+1)}$$

其中 $4$（二进制 `0100`）对应多项式 $x^2$。MixColumns 提供**扩散**效果——每列的两个 nibble 被混合为一个新的 nibble 对。

详细推导见附件 D.2。

逆列混淆（IMC）使用逆矩阵 $\begin{pmatrix} 9 & 2 \\ 2 & 9 \end{pmatrix}$。

## 3. 密钥扩展

16 位密钥 $k_0 \dots k_{15}$ 扩展为 48 位（3 个 16 位轮密钥）。

![[attachments/fig_D_5.png|400]]

*图 D.5 S-AES 密钥扩展*

**算法**：

1. $w_0 = (k_0 \dots k_7), w_1 = (k_8 \dots k_{15})$
2. $w_2 = w_0 \oplus \text{RCON}(1) \oplus \text{SubNib}(\text{RotNib}(w_1))$
3. $w_3 = w_2 \oplus w_1$
4. $w_4 = w_2 \oplus \text{RCON}(2) \oplus \text{SubNib}(\text{RotNib}(w_3))$
5. $w_5 = w_4 \oplus w_3$

其中：
- **RotNib**：将 nibble 对调位置
- **SubNib**：应用 S 盒代替
- **RCON(1) = `10000000`**，**RCON(2) = `00110000`**

## 4. S 盒的设计

S-AES S 盒基于 $GF(2^4)$ 上的求逆映射：$x \to x^{-1}$（对 $x=0$ 特殊处理）。该求逆运算经仿射变换后生成最终 S 盒值。这种基于有限域逆元的结构是 AES S 盒设计的核心——提供了经过验证的**抗差分和线性密码分析**能力。

## 5. S-AES 的结构分析

S-AES 体现了 AES 的四个关键设计思想：

| 设计原则 | S-AES 体现 | AES 对应 |
|---|---|---|
| **SPN 结构** | 代替-置换网络 | 相同（非 Feistel） |
| **字节代替** | SubNibbles（基于 $GF(2^4)$ 逆元） | SubBytes（基于 $GF(2^8)$ 逆元） |
| **行移位** | ShiftRows（$2 \times 2$ 矩阵） | ShiftRows（$4 \times 4$ 矩阵，不同行不同偏移） |
| **列混淆** | MixColumns（$GF(2^4)$ 上 $2 \times 2$ 矩阵乘） | MixColumns（$GF(2^8)$ 上 $4 \times 4$ 矩阵乘） |
| **密钥扩展** | $g$ 函数含 RotWord/SubWord/Rcon | $g$ 函数含 RotWord/SubWord/Rcon |

## 6. 附件解读

### 附件 D.1：$GF(2^4)$ 上的算术

教材提供了 $GF(2^4)$ 上的完整加法和乘法表（模 $x^4 + x + 1$）。加法即按位异或；乘法为多项式乘再取模。完整表格在教材原文中。

### 附件 D.2：列混淆函数的详细推导

MixColumns 的矩阵乘法展开后以位运算表达。以 $s'_{0,0}$ 为例：

$$s'_{0,0} = [(b_0 \oplus b_6), (b_1 \oplus b_4 \oplus b_7), (b_2 \oplus b_4 \oplus b_5), (b_3 \oplus b_5)]$$

推导关键步骤：
1. 将 nibble 表示为多项式（$b_0x^3 + b_1x^2 + b_2x + b_3$）
2. 执行 $GF(2^4)$ 矩阵乘法
3. 利用 $x^5 \bmod{(x^4 + x + 1)} = x^2 + x$ 和 $x^4 \bmod{(x^4 + x + 1)} = x + 1$ 化简

此推导展示了有限域上多项式运算如何转化为简单位运算——这是理解 AES MixColumns 实现的关键。

## 本章关键术语整理

| 类别 | 术语 |
|---|---|
| S-AES 基础 | nibble、SPN 结构、$GF(2^4)$、$2 \times 2$ 状态矩阵、2 轮 |
| 加密操作 | AddKey、SubNibbles（S 盒）、ShiftRows、MixColumns、$A_{K_2} \circ \text{SR} \circ \text{NS} \circ A_{K_1} \circ \text{MC} \circ \text{SR} \circ \text{NS} \circ A_{K_0}$ |
| 解密操作 | InvSubNibbles、InvShiftRows、InvMixColumns、$\text{IMC} = \begin{pmatrix} 9 & 2 \\ 2 & 9 \end{pmatrix}$ |
| 密钥扩展 | $w_0 \sim w_5$、RotNib、SubNib、RCON(1)/(2) |
| 有限域 | 不可约多项式 $x^4 + x + 1$、多项式表示、$GF(2^4)$ 加/乘表 |

## 学习目标自检

1. 能否列出 S-AES 的四个基本操作并说明各自作用？
2. 能否根据 S 盒完成任意 nibble 的 SubNibbles 查表操作？
3. 能否描述 S-AES 加密的完整复合函数（含轮数）并指出第 2 轮缺少哪个操作？
4. 能否描述密钥扩展中 $g$ 函数的三步操作（RotNib + SubNib + RCON）？
5. 能否说明 S-AES 如何体现 AES 的四个核心设计原则（SPN、S 盒、ShiftRows、MixColumns）？
6. 能否解释 MixColumns 在 $GF(2^4)$ 上的矩阵乘法为何可化简为位异或运算？

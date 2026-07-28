---
title: "附录D 简化AES - 导学讲义"
aliases:
  - "密码编码学与网络安全 附录D 导学讲义"
tags:
  - course
  - cryptography
  - network_security
  - simplified_aes
  - study_guide
category: textbook
source: "密码编码学与网络安全 原理与实践 第八版"
source_pdf: "Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf"
chapter: null
section: appendix
appendix: D
pdf_pages: "523-530"
book_pages: "501-508"
document_type: study-guide
status: complete
review_status: pending
created: 2026-07-24
updated: 2026-07-24
---

# 附录D 简化AES - 导学讲义

## 本附录导学

### 学习目标

1. 说明S-AES的教学目的、分组长度、密钥长度和三轮结构。
2. 把16位输入正确装填为按列排列的 $2\times2$ 半字节状态。
3. 手算密钥加、半字节代替、行移位和列混淆。
4. 使用S盒与逆S盒，并解释其有限域逆元和仿射变换来源。
5. 从16位主密钥扩展出三个16位轮密钥。
6. 解释加密、直接解密与等价逆解密之间的关系。
7. 在 $GF(2^4)$ 上进行异或加法、多项式乘法和模约减。

### 核心问题

- S-AES如何用小参数重现AES的核心结构？
- 为什么状态按列装填，而不是按通常阅读顺序逐行装填？
- 哪些变换提供非线性，哪些变换提供扩散？
- 列混淆矩阵为什么存在逆矩阵？
- 密钥扩展中的RotNib、SubNib和RCON分别做什么？
- 为什么最后一轮省略列混淆？

> [!warning] 安全边界
> S-AES是教学算法，不是安全的实际加密算法。它用于理解AES结构，不能保护真实数据。

## D.1 概述

S-AES使用16位明文分组和16位密钥，输出16位密文。

![[attachments/fig_D_1.png]]

*图D.1 S-AES加密和解密*

加密复合函数：

$$
A_{K_2}\circ\mathrm{SR}\circ\mathrm{NS}\circ A_{K_1}
\circ\mathrm{MC}\circ\mathrm{SR}\circ\mathrm{NS}\circ A_{K_0}
$$

函数从右向左执行，因此先执行 $A_{K_0}$。

| 轮 | 变换 |
|---|---|
| 第0轮 | $A_{K_0}$ |
| 第1轮 | NS、SR、MC、$A_{K_1}$ |
| 第2轮 | NS、SR、$A_{K_2}$ |

四个基本变换：

- $A_K$：密钥加。
- NS：半字节代替。
- SR：行移位。
- MC：列混淆。

![[attachments/fig_D_2.png]]

*图D.2 S-AES数据结构*

16位状态由4个半字节按列装入：

$$
(N_0,N_1,N_2,N_3)
\longleftrightarrow
\begin{pmatrix}
N_0&N_2\\
N_1&N_3
\end{pmatrix}
$$

前8位构成第一列，后8位构成第二列。这个顺序会影响行移位与列混淆，不能按行装填。

![[attachments/fig_D_3.png]]

*图D.3 S-AES加密轮*

直接解密是加密的逆：

$$
A_{K_0}\circ\mathrm{INS}\circ\mathrm{ISR}\circ\mathrm{IMC}
\circ A_{K_1}\circ\mathrm{INS}\circ\mathrm{ISR}\circ A_{K_2}
$$

## D.2 S-AES加密与解密

### D.2.1 密钥加

状态与16位轮密钥逐位异或：

$$
A_K(S)=S\oplus K
$$

![[attachments/fig_D_3-4_.png]]

异或具有自反性：

$$
(S\oplus K)\oplus K=S
$$

所以密钥加的逆就是它自身。

### D.2.2 半字节代替

状态中的每个4位半字节独立查S盒。最左2位选行，最右2位选列。

![[attachments/fig_D_4.png]]

*图D.4 S-AES变换*

S盒：

| 行\列 | 00 | 01 | 10 | 11 |
|---|---:|---:|---:|---:|
| 00 | 9 | 4 | A | B |
| 01 | D | 1 | 8 | 5 |
| 10 | 6 | 2 | 0 | 3 |
| 11 | C | E | F | 7 |

逆S盒：

| 行\列 | 00 | 01 | 10 | 11 |
|---|---:|---:|---:|---:|
| 00 | A | 5 | 9 | B |
| 01 | 1 | 7 | 8 | F |
| 10 | 6 | 0 | 2 | 3 |
| 11 | C | 4 | D | E |

例如A即二进制1010，行10、列10，查S盒得到0。因此NS将A映射为0，INS将0映射回A。

![[attachments/fig_D_4-5.png]]

*半字节代替示例*

> [!tip] 记忆
> DES S盒用外侧两位选行；S-AES S盒用左两位选行。两者不要混淆。

### D.2.3 行移位

第一行不变，第二行循环移位一个半字节：

$$
\begin{pmatrix}
N_0&N_2\\
N_1&N_3
\end{pmatrix}
\xrightarrow{\mathrm{SR}}
\begin{pmatrix}
N_0&N_2\\
N_3&N_1
\end{pmatrix}
$$

![[attachments/fig_D_4-5_2.png]]

因为第二行只有两个元素，左移一次与右移一次结果相同，所以ISR与SR相同。

### D.2.4 列混淆

列混淆在 $GF(2^4)$ 上做矩阵乘法：

$$
\begin{bmatrix}1&4\\4&1\end{bmatrix}
\begin{bmatrix}
s_{0,0}&s_{0,1}\\
s_{1,0}&s_{1,1}
\end{bmatrix}
=
\begin{bmatrix}
s'_{0,0}&s'_{0,1}\\
s'_{1,0}&s'_{1,1}
\end{bmatrix}
$$

逐列展开：

$$
s'_{0,j}=s_{0,j}\oplus(4*s_{1,j})
$$

$$
s'_{1,j}=(4*s_{0,j})\oplus s_{1,j}
$$

教材例：

$$
\begin{bmatrix}1&4\\4&1\end{bmatrix}
\begin{bmatrix}6&4\\C&0\end{bmatrix}
=
\begin{bmatrix}3&4\\7&3\end{bmatrix}
$$

逆列混淆矩阵：

$$
\begin{bmatrix}9&2\\2&9\end{bmatrix}
$$

二者在 $GF(2^4)$ 上相乘为单位矩阵：

$$
\begin{bmatrix}9&2\\2&9\end{bmatrix}
\begin{bmatrix}1&4\\4&1\end{bmatrix}
=
\begin{bmatrix}1&0\\0&1\end{bmatrix}
$$

注意这里的加法是异或，乘法是有限域乘法，不是普通整数运算。例如：

$$
9+(2\cdot4)=9+8=1
$$

$$
(9\cdot4)+2=2+2=0
$$

## D.3 密钥扩展

16位主密钥拆成两个8位字 $w_0,w_1$，扩展为 $w_0$ 到 $w_5$：

$$
\begin{aligned}
w_2&=w_0\oplus\mathrm{RCON}(1)
\oplus\mathrm{SubNib}(\mathrm{RotNib}(w_1))\\
w_3&=w_2\oplus w_1\\
w_4&=w_2\oplus\mathrm{RCON}(2)
\oplus\mathrm{SubNib}(\mathrm{RotNib}(w_3))\\
w_5&=w_4\oplus w_3
\end{aligned}
$$

轮密钥：

$$
K_0=w_0\|w_1,
\qquad
K_1=w_2\|w_3,
\qquad
K_2=w_4\|w_5
$$

轮常数：

$$
\mathrm{RCON}(1)=10000000
$$

$$
\mathrm{RCON}(2)=00110000
$$

对主密钥 $2\mathrm D55$：

$$
w_0=00101101,
\qquad
w_1=01010101
$$

教材结果：

$$
w_2=10111100,
\quad
w_3=11101001,
\quad
w_4=10100011,
\quad
w_5=01001010
$$

![[attachments/fig_D_5.png]]

*图D.5 S-AES密钥扩展*

RotNib交换一个字节的两个半字节；SubNib分别查S盒；RCON避免各轮以完全相同方式生成。

## D.4 S盒

S盒构造分四步：

1. 用0到F初始化16个半字节。
2. 将每个值解释为 $GF(2^4)$ 中模 $x^4+x+1$ 的多项式。
3. 求乘法逆元，0映射为自身。
4. 对位向量做仿射变换：

$$
\begin{bmatrix}
b'_0\\b'_1\\b'_2\\b'_3
\end{bmatrix}
=
\begin{bmatrix}
1&0&1&1\\
1&1&0&1\\
1&1&1&0\\
0&1&1&1
\end{bmatrix}
\begin{bmatrix}
b_0\\b_1\\b_2\\b_3
\end{bmatrix}
\oplus
\begin{bmatrix}
1\\0\\0\\1
\end{bmatrix}
$$

所有加法和乘法在模2下进行。有限域求逆带来非线性，仿射变换重新混合各位，最终得到表D.1的可逆代替。

## D.5 S-AES的结构

### 为什么首尾都有密钥加

若算法以不依赖密钥且可逆的变换开始或结束，攻击者也能直接撤销该变换，不增加安全性。因此第0轮只做密钥加，最终一步也以密钥加结束。

### 为什么最后一轮没有MC

省略最后一轮MC有助于让加密与等价逆解密使用同一类变换顺序。直接解密严格按加密逆序执行，但实现上希望加解密结构更相似。

### INS与ISR可交换

NS逐半字节独立代替，SR只移动半字节位置，所以先代替再移动与先移动再代替结果相同：

$$
\mathrm{INS}(\mathrm{ISR}(N))
=
\mathrm{ISR}(\mathrm{INS}(N))
$$

### IMC与密钥加的线性关系

有限域矩阵乘法对异或是线性的：

$$
\mathrm{IMC}(A_{K_1}(N))
=\mathrm{IMC}(K_1)\oplus\mathrm{IMC}(N)
$$

把中间轮解密密钥改为 $\mathrm{IMC}(K_1)$，就可调整解密运算顺序，形成等价逆结构。

## 附件D.1 $\mathrm{GF}(2^4)$上的算术

### 加法

有限域加法就是逐位异或：

$$
a+b=a\oplus b
$$

因此每个元素都是自己的加法逆元。

### 乘法

把4位值视为次数不超过3的多项式，先做多项式乘法，再模不可约多项式：

$$
m(x)=x^4+x+1
$$

教材例：

$$
4\cdot C
=(0100)_2\cdot(1100)_2
$$

$$
x^2(x^3+x^2)
=x^5+x^4
\equiv x^2+1
\pmod{x^4+x+1}
$$

故：

$$
4\cdot C=0101_2=5
$$

完整加法、乘法表：[[附录D_简化AES_教材原文#附件D.1 $\mathrm{GF}(2^4)$上的算术|教材原文附件D.1]]。

## 附件D.2 列混淆函数

把半字节写成多项式后，列混淆矩阵中的4对应 $x^2$：

$$
\begin{bmatrix}1&x^2\\x^2&1\end{bmatrix}
\begin{bmatrix}
S_{0,0}&S_{0,1}\\
S_{1,0}&S_{1,1}
\end{bmatrix}
$$

模 $x^4+x+1$ 有：

$$
x^5\equiv x^2+x,
\qquad
x^4\equiv x+1
$$

因此第一列左上输出的比特关系是：

$$
s'_{0,0}
=
[
b_0\oplus b_6,
b_1\oplus b_4\oplus b_7,
b_2\oplus b_4\oplus b_5,
b_3\oplus b_5
]
$$

其余三个输出的完整位关系见 [[附录D_简化AES_教材原文#附件D.2 列混淆函数|教材原文附件D.2]]。

## 常见误区与学习建议

- 状态按列装填，错一次会让后续所有步骤错位。
- S盒索引是左2位选行、右2位选列。
- 有限域中的加法是异或，不产生进位。
- $4\cdot C=5$ 是有限域乘法结果，不是十六进制整数乘法。
- 复合函数从右向左执行。
- S-AES与AES结构相似，但参数、轮数和安全性不同。

建议用固定表格记录每一步的状态：输入、$A_{K_0}$、NS、SR、MC、$A_{K_1}$、NS、SR、$A_{K_2}$，并在每一步检查仍为4个半字节。

## 与正文的联系

- [[第五章_有限域_导学讲义|第5章]]：有限域加法、乘法和逆元。
- [[第六章_高级加密标准_导学讲义|第6章]]：AES的字节代替、行移位、列混淆和密钥扩展。
- [[附录A_线性代数的基本概念_导学讲义|附录A]]：矩阵乘法与逆矩阵。


---
title: "附录D 简化AES - 复习笔记"
aliases:
  - "密码编码学与网络安全 附录D 复习笔记"
tags: [simplified_aes]
category: textbook
source: "密码编码学与网络安全 原理与实践 第八版"
source_pdf: "Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf"
chapter: null
section: appendix
appendix: D
pdf_pages: "523-530"
book_pages: "501-508"
document_type: review-notes
status: complete
review_status: pending
created: 2026-07-24
updated: 2026-07-24
---


# 附录D 简化AES - 复习笔记

## 本附录复习

| 参数 | S-AES |
|---|---:|
| 分组 | 16位 |
| 主密钥 | 16位 |
| 状态 | $2\times2$ 半字节 |
| 轮密钥 | $K_0,K_1,K_2$，各16位 |
| 轮次 | 第0轮、完整第1轮、无MC第2轮 |
| 用途 | 教学，不提供实际安全性 |

## D.1 概述

![[attachments/fig_D_1.png]]

*图D.1 S-AES加密与解密总体结构*

$$
E
=
A_{K_2}\circ\mathrm{SR}\circ\mathrm{NS}\circ A_{K_1}
\circ\mathrm{MC}\circ\mathrm{SR}\circ\mathrm{NS}\circ A_{K_0}
$$

$$
D
=
A_{K_0}\circ\mathrm{INS}\circ\mathrm{ISR}\circ\mathrm{IMC}
\circ A_{K_1}\circ\mathrm{INS}\circ\mathrm{ISR}\circ A_{K_2}
$$

![[attachments/fig_D_2.png]]

*图D.2 状态按列装填*

$$
(N_0,N_1,N_2,N_3)
\leftrightarrow
\begin{pmatrix}N_0&N_2\\N_1&N_3\end{pmatrix}
$$

![[attachments/fig_D_3.png]]

*图D.3 一个完整加密轮*

## D.2 S-AES加密与解密

### D.2.1 密钥加

$$
A_K(S)=S\oplus K
$$

$$
A_K^{-1}=A_K
$$

![[attachments/fig_D_3-4_.png]]

### D.2.2 半字节代替

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

![[attachments/fig_D_4.png]]

*图D.4 四类S-AES变换*

$$
A=(1010)_2
\xrightarrow{\mathrm{NS}}
S[10,10]=0
$$

![[attachments/fig_D_4-5.png]]

*半字节代替示例*

### D.2.3 行移位

$$
\begin{pmatrix}N_0&N_2\\N_1&N_3\end{pmatrix}
\xrightarrow{\mathrm{SR}}
\begin{pmatrix}N_0&N_2\\N_3&N_1\end{pmatrix}
$$

$$
\mathrm{ISR}=\mathrm{SR}
$$

![[attachments/fig_D_4-5_2.png]]

### D.2.4 列混淆

$$
\mathrm{MC}:
\begin{bmatrix}1&4\\4&1\end{bmatrix}
$$

$$
\mathrm{IMC}:
\begin{bmatrix}9&2\\2&9\end{bmatrix}
$$

$$
\begin{bmatrix}9&2\\2&9\end{bmatrix}
\begin{bmatrix}1&4\\4&1\end{bmatrix}
=I
$$

$$
s'_{0,j}=s_{0,j}\oplus4s_{1,j},
\qquad
s'_{1,j}=4s_{0,j}\oplus s_{1,j}
$$

教材例：

$$
\begin{bmatrix}1&4\\4&1\end{bmatrix}
\begin{bmatrix}6&4\\C&0\end{bmatrix}
=
\begin{bmatrix}3&4\\7&3\end{bmatrix}
$$

## D.3 密钥扩展

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

$$
\mathrm{RCON}(1)=10000000,
\qquad
\mathrm{RCON}(2)=00110000
$$

主密钥 $2\mathrm D55$：

$$
(w_0,w_1,w_2,w_3,w_4,w_5)
=
(2D,55,BC,E9,A3,4A)
$$

![[attachments/fig_D_5.png]]

*图D.5 S-AES密钥扩展*

## D.4 S盒

构造：

1. 初始化0到F。
2. 解释为 $GF(2^4)$ 中模 $x^4+x+1$ 的元素。
3. 求乘法逆，0保持为0。
4. 施加仿射变换。

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

## D.5 S-AES的结构

- 首尾使用密钥加；不依赖密钥的可逆首尾变换可被攻击者直接撤销。
- 最后一轮省略MC。
- INS与ISR可交换：

$$
\mathrm{INS}(\mathrm{ISR}(N))
=\mathrm{ISR}(\mathrm{INS}(N))
$$

- IMC对异或线性：

$$
\mathrm{IMC}(A_{K_1}(N))
=\mathrm{IMC}(K_1)\oplus\mathrm{IMC}(N)
$$

- 等价逆解密可使用变换后的中间轮密钥 $\mathrm{IMC}(K_1)$。

## 附件D.1 $\mathrm{GF}(2^4)$上的算术

$$
a+b=a\oplus b
$$

模多项式：

$$
m(x)=x^4+x+1
$$

教材例：

$$
4\cdot C
=x^2(x^3+x^2)
=x^5+x^4
\equiv x^2+1
=5
$$

完整算术表：[[附录D_简化AES_教材原文#附件D.1 $\mathrm{GF}(2^4)$上的算术|教材原文附件D.1]]。

## 附件D.2 列混淆函数

$$
\begin{bmatrix}1&x^2\\x^2&1\end{bmatrix}
\begin{bmatrix}
S_{0,0}&S_{0,1}\\
S_{1,0}&S_{1,1}
\end{bmatrix}
$$

$$
x^5\equiv x^2+x,
\qquad
x^4\equiv x+1
\pmod{x^4+x+1}
$$

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

完整四组位关系：[[附录D_简化AES_教材原文#附件D.2 列混淆函数|教材原文附件D.2]]。

## 重点、难点与易错点

| 类型 | 内容 |
|---|---|
| 重点 | 状态装填、四变换、密钥扩展、S盒、[有限域](<../../../../Course Notes/编码论（Coding Theory）/基于多个共轭根构造循环码的例题解析/基于多个共轭根构造循环码的例题解析.md>)列混淆 |
| 难点 | $GF(2^4)$乘法、逆列混淆、等价逆结构 |
| 易错 | 按行装填；把有限域乘法当整数乘法；从左向右读复合函数 |

## 教材内容与补充理解

| 类型 | 内容 |
|---|---|
| 教材内容 | S-AES全部变换、密钥扩展、S盒构造、结构分析、有限域算术和列混淆位表达式 |
| 补充理解 | 用“代替提供非线性、移位和列混淆提供扩散”组织各变换作用 |

## 关键词索引

| 关键词 | 定位 |
|---|---|
| 状态矩阵、三轮结构 | D.1 |
| 密钥加、NS、SR、MC | D.2 |
| RotNib、SubNib、RCON | D.3 |
| S盒仿射变换 | D.4 |
| 等价逆解密 | D.5 |
| $GF(2^4)$ | 附件D.1 |
| 列混淆位表达式 | 附件D.2 |

## Related

- [[第五章_有限域_复习笔记|第5章 有限域]]
- [[第六章_高级加密标准_复习笔记|第6章 AES]]
- [[附录A_线性代数的基本概念_复习笔记|附录A 线性代数]]

## 复盘问题

- 16位输入如何映射到 $2\times2$ 状态？
- 为什么SR与ISR相同，而MC与IMC不同？
- 如何从 $w_0,w_1$ 计算 $w_2$ 到 $w_5$？
- 为什么 $4\cdot C$ 在 $GF(2^4)$ 中等于5？
- 等价逆解密为什么要变换中间轮密钥？

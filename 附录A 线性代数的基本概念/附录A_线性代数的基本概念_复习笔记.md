---
title: "附录A 线性代数的基本概念 - 复习笔记"
aliases:
  - "密码编码学与网络安全 附录A 复习笔记"
tags:
  - course
  - cryptography
  - network_security
  - linear_algebra
  - review_notes
category: textbook
source: "密码编码学与网络安全 原理与实践 第八版"
source_pdf: "Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf"
chapter: null
section: appendix
appendix: A
pdf_pages: "503-507"
book_pages: "481-485"
document_type: review-notes
status: complete
review_status: pending
created: 2026-07-24
updated: 2026-07-24
---

# 附录A 线性代数的基本概念 - 复习笔记

## 本附录复习

### 一页主线

$$
\text{向量与矩阵运算}
\longrightarrow
\det(A)
\longrightarrow
A^{-1}
\longrightarrow
Z_n\text{上的逆矩阵}
\longrightarrow
\text{Hill加解密}
$$

| 类型 | 内容 |
|---|---|
| 重点 | 矩阵乘法、余子式展开、逆矩阵、模逆矩阵 |
| 难点 | 余子式符号与转置、复合模数下的可逆条件 |
| 易错 | 行列方向交换、把矩阵逆理解为逐元素倒数、忘记模归约 |

## A.1 向量和矩阵运算

- $a_{ij}$：第 $i$ 行、第 $j$ 列元素。
- $m\times n$：$m$ 行、$n$ 列。
- 行向量横写，列向量竖写。

### A.1.1 算术

同维矩阵加法：

$$
C=A+B\Rightarrow c_{ij}=a_{ij}+b_{ij}
$$

标量乘法：

$$
C=kA\Rightarrow c_{ij}=ka_{ij}
$$

向量内积：

$$
(x_1\ \cdots\ x_m)
\begin{pmatrix}y_1\\\vdots\\y_m\end{pmatrix}
=\sum_{k=1}^{m}x_ky_k
$$

矩阵乘法：

$$
A_{m\times n}B_{n\times p}=C_{m\times p},
\qquad
c_{ij}=\sum_{k=1}^{n}a_{ik}b_{kj}
$$

速记：内侧维数相等，外侧维数留下。

> [!warning] 易错点
> $AB$ 一般不等于 $BA$；后者甚至可能无定义。

### A.1.2 行列式

二阶：

$$
\det(A)=a_{11}a_{22}-a_{21}a_{12}
$$

三阶：

$$
\begin{aligned}
\det(A)
={}&a_{11}a_{22}a_{33}+a_{12}a_{23}a_{31}+a_{13}a_{21}a_{32}\\
&-a_{31}a_{22}a_{13}-a_{32}a_{23}a_{11}-a_{33}a_{21}a_{12}
\end{aligned}
$$

余子式：

$$
\operatorname{cof}_{ij}(A)
=(-1)^{i+j}\det(A_{ij})
$$

展开：

$$
\det(A)=\sum_{j=1}^{n}a_{ij}\operatorname{cof}_{ij}(A)
=\sum_{i=1}^{n}a_{ij}\operatorname{cof}_{ij}(A)
$$

教材示例：

$$
\det
\begin{pmatrix}
2&4&3\\6&1&5\\-2&1&3
\end{pmatrix}
=-92
$$

### A.1.3 逆矩阵

普通数域上的存在条件：

$$
\det(A)\ne0
$$

$$
AA^{-1}=A^{-1}A=I
$$

$$
b_{ij}
=\frac{\operatorname{cof}_{ji}(A)}{\det(A)}
$$

> [!important] 难点
> 余子式矩阵需要转置，因此 $b_{ij}$ 使用 $\operatorname{cof}_{ji}$。

教材示例：

$$
A^{-1}
=
\begin{pmatrix}
1/46&9/92&-17/92\\
7/23&-3/23&-2/23\\
-2/23&5/46&11/46
\end{pmatrix}
$$

## A.2 $Z_n$上的线性代数运算

### 基本规则

- 加、减、乘均模 $n$。
- 除以 $b$ 等于乘以 $b^{-1}$。
- $b^{-1}$ 存在当且仅当 $\gcd(b,n)=1$。
- 矩阵可逆当且仅当 $\gcd(\det(A),n)=1$。

### 模26逆元表

| 值 | 逆元 | 值 | 逆元 |
|---|---:|---|---:|
| 1 | 1 | 15 | 7 |
| 3 | 9 | 17 | 23 |
| 5 | 21 | 19 | 11 |
| 7 | 15 | 21 | 5 |
| 9 | 3 | 23 | 17 |
| 11 | 19 | 25 | 25 |

### 二阶模逆矩阵例

$$
A=\begin{pmatrix}4&3\\9&6\end{pmatrix},
\quad
\det(A)\equiv23,
\quad
23^{-1}\equiv17\pmod{26}
$$

$$
A^{-1}
=
\begin{pmatrix}
24&1\\3&16
\end{pmatrix}
\pmod{26}
$$

### Hill密码完整例

$$
P=(15\ 0\ 24),
\qquad
K=
\begin{pmatrix}
17&17&5\\
21&18&21\\
2&2&19
\end{pmatrix}
$$

$$
C=PK\bmod26=(17\ 17\ 11)
$$

$$
\det(K)\equiv23,
\qquad
(\det K)^{-1}\equiv17\pmod{26}
$$

$$
K^{-1}
=
\begin{pmatrix}
4&9&15\\
15&17&6\\
24&0&17
\end{pmatrix}
$$

$$
P=CK^{-1}\bmod26=(15\ 0\ 24)
$$

### 求逆检查表

1. 计算 $\det(K)$ 并模 $n$ 归约。
2. 检查 $\gcd(\det(K),n)=1$。
3. 求 $(\det K)^{-1}$。
4. 计算余子式矩阵并转置。
5. 乘行列式的模逆，逐元素模 $n$。
6. 验证 $KK^{-1}\equiv K^{-1}K\equiv I$。

## 易混淆知识点

| 概念A | 概念B | 区别 |
|---|---|---|
| 元素倒数 | 逆矩阵 | 逆矩阵由行列式和转置余子式矩阵构成 |
| $\det(A)\ne0$ | 模 $n$ 可逆 | 后者要求 $\det(A)$ 与 $n$ 互素 |
| $\operatorname{cof}_{ij}$ | $b_{ij}$ | $b_{ij}$ 使用转置位置的 $\operatorname{cof}_{ji}$ |
| 行向量右乘 | 矩阵左乘列向量 | 维数、组合方向和结果形状不同 |

## 教材内容与补充理解

| 类型 | 内容 |
|---|---|
| 教材内容 | 矩阵算术、行列式、余子式、逆矩阵、$Z_{26}$逆元表、Hill密码完整算例 |
| 补充理解 | 用 $\gcd(\det(A),n)=1$ 汇总模矩阵可逆条件；用信息是否被压缩解释可逆性 |

## 关键词索引

| 关键词 | 定位 |
|---|---|
| 行向量、列向量 | A.1 |
| 矩阵乘法 | A.1.1 |
| 行列式、余子式 | A.1.2 |
| 逆矩阵、单位矩阵 | A.1.3 |
| 模逆元、$Z_n$ | A.2 |
| Hill密码 | A.2 |

## Related

- [[第二章_数论基础_复习笔记|第2章 数论基础]]
- [[第三章_传统加密技术_复习笔记|第3章 传统加密技术]]
- [[第五章_有限域_复习笔记|第5章 有限域]]
- [[第六章_高级加密标准_复习笔记|第6章 高级加密标准]]

## 复盘建议

- 能否判断两个矩阵能否相乘并写出结果维数？
- 能否解释余子式符号和转置位置？
- 能否说明“行列式非零”在模26中为什么还不够？
- 能否独立复算教材 Hill 密码中的 $K^{-1}$ 并验证解密结果？

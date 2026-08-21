---
title: "附录A 线性代数的基本概念 - 教材原文"
aliases:
  - "密码编码学与网络安全 第八版 附录A 教材原文"
tags: [linear_algebra, matrix]
category: textbook
source: "密码编码学与网络安全 原理与实践 第八版"
source_pdf: "Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf"
chapter: null
section: appendix
appendix: A
pdf_pages: "503-507"
book_pages: "481-485"
document_type: textbook-source
status: complete
review_status: pending
created: 2026-07-23
updated: 2026-07-23
---



# 附录A 线性代数的基本概念

## A.1 向量和矩阵运算

本书使用如下约定：

$$
(x_1\quad x_2\quad \cdots\quad x_m) \qquad \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_n \end{pmatrix} \qquad \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \end{pmatrix}
$$

注意，在矩阵中，元素的第一个下标指的是行，第二个下标指的是列。

### A.1.1 算术

维数相同的两个矩阵可以逐个元素地加或减。因此，对$C=A+B$，$C$的元素是$c_{ij} = a_{ij} + b_{ij}$。例如，

$$
\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} + \begin{pmatrix} 3 & 0 & -6 \\ 2 & -3 & 1 \\ 9 & 6 & 3 \end{pmatrix} = \begin{pmatrix} 4 & -2 & -3 \\ 2 & 1 & 6 \\ 12 & 12 & 12 \end{pmatrix}
$$

标量乘以矩阵时，将矩阵中的每个元素乘以该标量即可。因此，对$C=kA$有$c_{ij} = k \times a_{ij}$。例如，

$$
3\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} = \begin{pmatrix} 3 & -6 & 9 \\ 0 & 12 & 15 \\ 9 & 18 & 27 \end{pmatrix}
$$

$m$维行向量和$m$维列向量的积是一个标量：

$$
(x_1\quad x_2\quad \cdots\quad x_m) \times \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_m \end{pmatrix} = x_1y_1 + x_2y_2 + \cdots + x_my_m
$$

若矩阵$A$的列数和矩阵$B$的行数相同，则按照上述顺序，矩阵$A$和$B$适合于做乘法。令$A$是一个阶为$m \times n$的矩阵，$B$是一个阶为$n \times p$的矩阵，得到乘法的步骤如下：按照刚定义的行向量和列向量的乘积规则，用$A$的每行乘以$B$的每列。因此，对$C=AB$，有$c_{ij} = \sum_{k=1}^n a_{ik}b_{kj}$，且结果矩阵的阶为$m \times p$。注意，按照这些规则，可以让一个行向量与一个其行数与向量维数相同的矩阵相乘。若矩阵的列数与向量的维数相同，则可让矩阵乘以列向量。因此，运用本节开始的记号，对$D=XA$，得到一个行向量，其元素为$d_j = \sum_{k=1}^m x_k a_{kj}$。对$E=AY$，得到一个列向量，其元素为$e_i = \sum_{k=1}^n a_{ik} y_k$。例如，

$$
(2\quad -5\quad 3)\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} = \left(2\times1+(-5)\times0+3\times3\quad 2\times(-2)+(-5)\times4+3\times6\quad 2\times3+(-5)\times5+3\times9\right) = (11\quad -6\quad 8)
$$

又如，

$$
\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix}\begin{pmatrix} 2 \\ -5 \\ 3 \end{pmatrix} = \begin{pmatrix} 1\times2+(-2)\times(-5)+3\times3 \\ 0\times2+4\times(-5)+5\times3 \\ 3\times2+6\times(-5)+9\times3 \end{pmatrix} = \begin{pmatrix} 21 \\ -5 \\ 3 \end{pmatrix}
$$

### A.1.2 [[证明_三角形行列式|行列式]]

由$\det(A)$表示的方阵$A$的行列式是一个标量，代表矩阵元素的和与积。详见关于线性代数的教材，这里只给出相关的结果。

对$2 \times 2$矩阵$A$，$\det(A) = a_{11}a_{22} - a_{21}a_{12}$。

对$3 \times 3$矩阵$A$，$\det(A) = a_{11}a_{22}a_{33} + a_{12}a_{23}a_{31} + a_{13}a_{21}a_{32} - a_{31}a_{22}a_{13} - a_{32}a_{23}a_{11} - a_{33}a_{21}a_{12}$。

一般来说，方阵的行列式可用其余子式进行计算。$A$的余子式记为$\text{cof}_{ij}(A)$，它定义为约化矩阵的行列式，其中约化矩阵是通过删除$A$的第$i$行和第$j$列得到的，且$i+j$为偶数时取正号，为奇数时取负号。例如，

$$
\text{cof}_{23}\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix} = -\det\begin{pmatrix} 2 & 4 \\ -2 & 1 \end{pmatrix} = -10
$$

任意$n \times n$方阵的行列式可以如下计算：

$$
\det(A) = \sum_{j=1}^n \left[a_{ij} \cdot \text{cof}_{ij}(A)\right], \quad \text{对任意} \ i
$$

或

$$
\det(A) = \sum_{i=1}^n \left[a_{ij} \cdot \text{cof}_{ij}(A)\right], \quad \text{对任意} \ j
$$

例如，

$$
\begin{aligned}
\det\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix} &= a_{21}\text{cof}_{21} + a_{22}\text{cof}_{22} + a_{23}\text{cof}_{23} \\
&= 6 \times \left(-\det\begin{pmatrix}4 & 3 \\ 1 & 3\end{pmatrix}\right) + 1 \times \det\begin{pmatrix}2 & 3 \\ -2 & 3\end{pmatrix} + 5 \times \left(-\det\begin{pmatrix}2 & 4 \\ -2 & 1\end{pmatrix}\right) \\
&= 6 \times (-9) + 1 \times 12 + 5 \times (-10) = -92
\end{aligned}
$$

### A.1.3 [[证明_逆矩阵乘积公式|逆矩阵]]

若矩阵$A$有一个非零行列式，则它有一个逆矩阵，记为$A^{-1}$。逆矩阵的性质是$AA^{-1} = A^{-1}A = I$，其中$I$是除主对角线元素为1外其余元素都为0的矩阵。任何向量或矩阵乘以$I$的结果仍然是原向量或原矩阵，因此$I$也称单位矩阵。逆矩阵的计算如下。对$B = A^{-1}$，有

$$
b_{ij} = \frac{\text{cof}_{ji}(A)}{\det(A)}
$$

例如，若$A$是上例中的矩阵，则对逆矩阵$B$，可以计算：

$$
b_{32} = \frac{\text{cof}_{23}(A)}{\det(A)} = \frac{-10}{-92} = \frac{10}{92}
$$

以此类推，可以得到$B$的所有9个元素。运用Sage开源数学软件，很容易计算逆矩阵：

```
sage: A = Matrix([[2,4,3],[6,1,5],[-2,1,3]])
sage: A
[ 2  4  3]
[ 6  1  5]
[-2  1  3]
sage: A^-1
[ 1/46   9/92 -17/92]
[  7/23  -3/23  -2/23]
[ -2/23  5/46  11/46]
```

我们有

$$
\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix}\begin{pmatrix} 2/92 & 9/92 & -17/92 \\ 28/92 & -12/92 & -8/92 \\ -8/92 & 10/92 & 22/92 \end{pmatrix} = \begin{pmatrix} 2/92 & 9/92 & -17/92 \\ 28/92 & -12/92 & -8/92 \\ -8/92 & 10/92 & 22/92 \end{pmatrix}\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix} = \begin{pmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix}
$$

## A.2 $Z_n$上的线性代数运算

向量和矩阵的算术运算可在$Z_n$上进行，即所有运算都可以模$n$执行。唯一的限制是，只有除数在$Z_n$上存在逆元时才能进行除运算。下面分析$Z_{26}$上的运算。因为26不是素数，所以不是$Z_{26}$上的所有数都有乘法逆元。表A.1中列出了模26的所有乘法逆元。例如，$3 \times 9 = 1 \mod 26$，所以3和9互为乘法逆元。

*表A.1 模26的乘法逆元*

| 值   | 逆元  |     | 值   | 逆元  |
| --- | --- | --- | --- | --- |
| 1   | 1   |     | 15  | 7   |
| 3   | 9   |     | 17  | 23  |
| 5   | 21  |     | 19  | 11  |
| 7   | 15  |     | 21  | 5   |
| 9   | 3   |     | 23  | 17  |
| 11  | 19  |     | 25  | 25  |

例如，$Z_{26}$上的矩阵$A = \begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix}$，于是有

$$
\det(A) = (4 \times 6) - (3 \times 9) \mod 26 = -3 \mod 26 = 23
$$

由表A.1，有$(\det(A))^{-1} = 17$。下面计算逆矩阵：

$$
A^{-1} = (\det(A))^{-1}\begin{pmatrix} \text{cof}_{11}(A) & \text{cof}_{21}(A) \\ \text{cof}_{12}(A) & \text{cof}_{22}(A) \end{pmatrix} = 17 \times \begin{pmatrix}6 & -3 \\ -9 & 4\end{pmatrix} \mod 26 = \begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix}
$$

验证如下：

$$
AA^{-1} = \begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix}\begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix} \mod 26 = \begin{pmatrix}105 & 52 \\ 234 & 105\end{pmatrix} \mod 26 = \begin{pmatrix}1 & 0 \\ 0 & 1\end{pmatrix}
$$

$$
A^{-1}A = \begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix}\begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix} \mod 26 = \begin{pmatrix}105 & 78 \\ 156 & 105\end{pmatrix} \mod 26 = \begin{pmatrix}1 & 0 \\ 0 & 1\end{pmatrix}
$$

下面详细说明3.2节中Hill密码的计算细节。首先使用下面的加密密钥来加密明文$(15\ 0\ 24)$：

$$
K = \begin{pmatrix}17 & 17 & 5 \\ 21 & 18 & 21 \\ 2 & 2 & 19\end{pmatrix}
$$

加密公式为$C = PK \mod 26$，因此有

$$
\begin{aligned}
C &= (15\quad 0\quad 24)\begin{pmatrix}17 & 17 & 5 \\ 21 & 18 & 21 \\ 2 & 2 & 19\end{pmatrix} \mod 26 \\
&= \left(15\times17+0\times21+24\times2\quad 15\times17+0\times18+24\times2\quad 15\times5+0\times21+24\times19\right) \mod 26 \\
&= (303\quad 303\quad 531) \mod 26 \\
&= (17\quad 17\quad 11)
\end{aligned}
$$

解密时使用公式$P = CK^{-1} \mod 26$。首先计算矩阵$K$的逆矩阵。根据行列式的定义，有

$$
\begin{aligned}
\det(K) &= k_{11}k_{22}k_{33} + k_{12}k_{23}k_{31} + k_{13}k_{21}k_{32} - k_{31}k_{22}k_{13} - k_{32}k_{23}k_{11} - k_{33}k_{21}k_{12} \mod 26 \\
\det(K) &= (17\times18\times19) + (17\times21\times2) + (5\times21\times2) - (2\times18\times5) - (2\times21\times17) - (19\times21\times17) \mod 26 \\
\det(K) &= 5814 + 714 + 210 - 180 - 714 - 6783 \mod 26 \\
\det(K) &= -939 \mod 26 = (-37\times26) + 23 \mod 26 = 23
\end{aligned}
$$

根据表A.1可以得到$(\det(K))^{-1} = 17$。下面就可开始计算逆矩阵。为方便起见，将$K$的逆矩阵表示为$B = K^{-1}$。使用表A.1中的结果，矩阵$B$的元素计算如下：

$$
b_{ij} = \frac{\text{cof}_{ji}(K)}{\det(K)} \mod 26 = 17 \times \text{cof}_{ji}(K) \mod 26
$$

对本例中的矩阵，有

$$
b_{11} = \begin{vmatrix}18 & 21 \\ 2 & 19\end{vmatrix} \times 17 \mod 26 = (18\times19 - 21\times2) \times 17 \mod 26 = 5100 \mod 26 = 4
$$

$$
b_{12} = -\begin{vmatrix}17 & 5 \\ 2 & 19\end{vmatrix} \times 17 \mod 26 = -(17\times19 - 5\times2) \times 17 \mod 26 = -5321 \mod 26 = 9
$$

$$
b_{13} = \begin{vmatrix}17 & 5 \\ 18 & 21\end{vmatrix} \times 17 \mod 26 = (17\times21 - 5\times18) \times 17 \mod 26 = 4539 \mod 26 = 15
$$

$$
b_{21} = -\begin{vmatrix}21 & 21 \\ 2 & 19\end{vmatrix} \times 17 \mod 26 = -(21\times19 - 21\times2) \times 17 \mod 26 = -6069 \mod 26 = 15
$$

$$
b_{22} = \begin{vmatrix}17 & 5 \\ 2 & 19\end{vmatrix} \times 17 \mod 26 = (17\times19 - 5\times2) \times 17 \mod 26 = 5321 \mod 26 = 17
$$

$$
b_{23} = -\begin{vmatrix}17 & 5 \\ 21 & 21\end{vmatrix} \times 17 \mod 26 = -(17\times21 - 5\times21) \times 17 \mod 26 = -4284 \mod 26 = 6
$$

$$
b_{31} = \begin{vmatrix}21 & 18 \\ 2 & 2\end{vmatrix} \times 17 \mod 26 = (21\times2 - 18\times2) \times 17 \mod 26 = 102 \mod 26 = 24
$$

$$
b_{32} = -\begin{vmatrix}17 & 17 \\ 2 & 2\end{vmatrix} \times 17 \mod 26 = -(17\times2 - 17\times2) \times 17 \mod 26 = 0 \mod 26 = 0
$$

$$
b_{33} = \begin{vmatrix}17 & 17 \\ 21 & 18\end{vmatrix} \times 17 \mod 26 = (17\times18 - 17\times21) \times 17 \mod 26 = -867 \mod 26 = 17
$$

于是得到矩阵$K$的逆矩阵为

$$
K^{-1} = \begin{pmatrix}4 & 9 & 15 \\ 15 & 17 & 6 \\ 24 & 0 & 17\end{pmatrix}
$$

根据解密公式$P = CK^{-1} \mod 26$，可得明文为

$$
\begin{aligned}
P &= (17\quad 17\quad 11)\begin{pmatrix}4 & 9 & 15 \\ 15 & 17 & 6 \\ 24 & 0 & 17\end{pmatrix} \mod 26 \\
&= \left(17\times4+17\times15+11\times24\quad 17\times9+17\times17+11\times0\quad 17\times15+17\times6+11\times17\right) \mod 26 \\
&= (587\quad 442\quad 544) \mod 26 \\
&= (15\quad 0\quad 24)
\end{aligned}
$$

---
title: 附录A 线性代数的基本概念 - 学习目标笔记
aliases:
  - 密码编码学与网络安全 附录A Note
tags: [linear_algebra, math, textbook_chapter]
source: 密码编码学与网络安全 原理与实践 第八版
chapter: null
appendix: A
type: note
status: maintained
created: 2026-08-09
updated: 2026-08-09
---

# 附录A 线性代数的基本概念

## 0. 内容总览

本附录回顾密码学中涉及的线性代数基础，涵盖向量和矩阵运算、行列式与逆矩阵的计算、以及在模 $n$ 有限域上的矩阵运算。教材给出 Hill 密码的完整加密/解密实例，演示矩阵运算在实际密码算法中的应用。

## 1. 向量和矩阵运算

### 1.1 基本表示

本书使用的约定如下：

- 行向量：$(x_1 \quad x_2 \quad \cdots \quad x_m)$
- 列向量：$\begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_n \end{pmatrix}$
- 矩阵（$m \times n$）：$\begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \end{pmatrix}$

矩阵元素中，第一个下标指行，第二个下标指列。

### 1.2 加法和减法

维数相同的两个矩阵可以逐个元素地加减。对 $C = A + B$，有 $c_{ij} = a_{ij} + b_{ij}$。

**示例**：

$$\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} + \begin{pmatrix} 3 & 0 & -6 \\ 2 & -3 & 1 \\ 9 & 6 & 3 \end{pmatrix} = \begin{pmatrix} 4 & -2 & -3 \\ 2 & 1 & 6 \\ 12 & 12 & 12 \end{pmatrix}$$

### 1.3 标量乘法

标量乘以矩阵：对 $C = kA$，$c_{ij} = k \cdot a_{ij}$。

**示例**：

$$3\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} = \begin{pmatrix} 3 & -6 & 9 \\ 0 & 12 & 15 \\ 9 & 18 & 27 \end{pmatrix}$$

### 1.4 向量乘法

$m$ 维行向量和 $m$ 维列向量的积是一个标量（点积）：

$$(x_1 \quad x_2 \quad \cdots \quad x_m) \times \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_m \end{pmatrix} = x_1y_1 + x_2y_2 + \cdots + x_my_m$$

### 1.5 矩阵乘法

若矩阵 $A$ 的列数等于矩阵 $B$ 的行数，则 $A$ 和 $B$ 可以相乘。令 $A$ 为 $m \times n$ 矩阵，$B$ 为 $n \times p$ 矩阵，则 $C = AB$ 中：

$$c_{ij} = \sum_{k=1}^{n} a_{ik} \cdot b_{kj}$$

结果 $C$ 的阶为 $m \times p$。

**关键规律**：
- 行向量 $\times$ 矩阵 → 行向量：$D = XA$，$d_j = \sum_{k=1}^{m} x_k \cdot a_{kj}$
- 矩阵 $\times$ 列向量 → 列向量：$E = AY$，$e_i = \sum_{k=1}^{n} a_{ik} \cdot y_k$

**示例——行向量乘矩阵**：

$$(2 \quad -5 \quad 3) \begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix}$$

$$= (2 \times 1 + (-5) \times 0 + 3 \times 3 \quad 2 \times (-2) + (-5) \times 4 + 3 \times 6 \quad 2 \times 3 + (-5) \times 5 + 3 \times 9) = (11 \quad -6 \quad 8)$$

**示例——矩阵乘列向量**：

$$\begin{pmatrix} 1 & -2 & 3 \\ 0 & 4 & 5 \\ 3 & 6 & 9 \end{pmatrix} \begin{pmatrix} 2 \\ -5 \\ 3 \end{pmatrix} = \begin{pmatrix} 1 \times 2 + (-2) \times (-5) + 3 \times 3 \\ 0 \times 2 + 4 \times (-5) + 5 \times 3 \\ 3 \times 2 + 6 \times (-5) + 9 \times 3 \end{pmatrix} = \begin{pmatrix} 21 \\ -5 \\ 3 \end{pmatrix}$$

### 1.6 转置

矩阵 $A$ 的转置 $A^{T}$ 满足 $(A^{T})_{ij} = a_{ji}$——行和列互换。

## 2. 行列式

### 2.1 定义

方阵 $A$ 的**行列式** $\det(A)$ 是一个标量，代表矩阵元素的组合。

$2 \times 2$ 矩阵：

$$\det\begin{pmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{pmatrix} = a_{11}a_{22} - a_{21}a_{12}$$

$3 \times 3$ 矩阵：

$$\det(A) = a_{11}a_{22}a_{33} + a_{12}a_{23}a_{31} + a_{13}a_{21}a_{32} - a_{31}a_{22}a_{13} - a_{32}a_{23}a_{11} - a_{33}a_{21}a_{12}$$

### 2.2 余子式展开法

一般方阵的行列式可通过**余子式（cofactor）**计算。$A$ 的余子式 $\text{cof}_{ij}(A)$ 定义为删除 $A$ 的第 $i$ 行和第 $j$ 列后剩余矩阵的行列式，其符号取决于 $i+j$ 的奇偶性（偶为正，奇为负）。

**示例**——计算 $\text{cof}_{23}$：

$$\text{cof}_{23}\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix} = -\det\begin{pmatrix} 2 & 4 \\ -2 & 1 \end{pmatrix} = -(2 \times 1 - (-2) \times 4) = -10$$

按任意行 $i$ 展开：

$$\det(A) = \sum_{j=1}^{n} [a_{ij} \cdot \text{cof}_{ij}(A)]$$

或按任意列 $j$ 展开：

$$\det(A) = \sum_{i=1}^{n} [a_{ij} \cdot \text{cof}_{ij}(A)]$$

**完整示例**——按第 2 行展开计算 $3 \times 3$ 矩阵的行列式：

$$\begin{aligned} \det\begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix} &= a_{21}\text{cof}_{21} + a_{22}\text{cof}_{22} + a_{23}\text{cof}_{23} \\ &= 6 \times (-\det\begin{pmatrix}4 & 3 \\ 1 & 3\end{pmatrix}) + 1 \times \det\begin{pmatrix}2 & 3 \\ -2 & 3\end{pmatrix} + 5 \times (-\det\begin{pmatrix}2 & 4 \\ -2 & 1\end{pmatrix}) \\ &= 6 \times (-9) + 1 \times 12 + 5 \times (-10) = -92 \end{aligned}$$

## 3. 逆矩阵

### 3.1 定义与公式

方阵 $A$ 的**逆矩阵** $A^{-1}$ 满足 $AA^{-1} = A^{-1}A = I$（单位矩阵）。$A$ 可逆当且仅当 $\det(A) \neq 0$。

逆矩阵的计算公式（基于余子式）：

$$A^{-1} = \frac{1}{\det(A)} \begin{pmatrix} \text{cof}_{11}(A) & \text{cof}_{21}(A) & \cdots & \text{cof}_{n1}(A) \\ \text{cof}_{12}(A) & \text{cof}_{22}(A) & \cdots & \text{cof}_{n2}(A) \\ \vdots & \vdots & \ddots & \vdots \\ \text{cof}_{1n}(A) & \text{cof}_{2n}(A) & \cdots & \text{cof}_{nn}(A) \end{pmatrix}$$

注意：伴随矩阵的行列是余子式的**转置**排列——$\text{cof}_{ji}$ 位于位置 $(i,j)$。

### 3.2 示例——普通算术上的逆矩阵

求 $A = \begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix}$ 的逆矩阵。

首先计算 $\det(A) = -92$（由上例）。余子式矩阵为：

| cof | 值 |
|---|---|
| $\text{cof}_{11}$ | $\det\begin{pmatrix}1 & 5 \\ 1 & 3\end{pmatrix} = -2$ |
| $\text{cof}_{12}$ | $-\det\begin{pmatrix}6 & 5 \\ -2 & 3\end{pmatrix} = -28$ |
| $\text{cof}_{13}$ | $\det\begin{pmatrix}6 & 1 \\ -2 & 1\end{pmatrix} = 8$ |
| $\text{cof}_{21}$ | $-\det\begin{pmatrix}4 & 3 \\ 1 & 3\end{pmatrix} = -9$ |
| $\text{cof}_{22}$ | $\det\begin{pmatrix}2 & 3 \\ -2 & 3\end{pmatrix} = 12$ |
| $\text{cof}_{23}$ | $-\det\begin{pmatrix}2 & 4 \\ -2 & 1\end{pmatrix} = -10$ |
| $\text{cof}_{31}$ | $\det\begin{pmatrix}4 & 3 \\ 1 & 5\end{pmatrix} = 17$ |
| $\text{cof}_{32}$ | $-\det\begin{pmatrix}2 & 3 \\ 6 & 5\end{pmatrix} = 8$ |
| $\text{cof}_{33}$ | $\det\begin{pmatrix}2 & 4 \\ 6 & 1\end{pmatrix} = -22$ |

转置后乘以 $1/\det(A) = -1/92$ 得：

$$A^{-1} = \frac{1}{92}\begin{pmatrix} 2 & 9 & -17 \\ 28 & -12 & -8 \\ -8 & 10 & 22 \end{pmatrix}$$

## 4. $Z_n$ 上的线性代数运算

密码学中的许多运算在模 $n$ 的整数环 $Z_n$ 上进行。所有运算均可模 $n$ 执行，唯一的限制是只有除数在 $Z_n$ 中存在乘法逆元时才可进行除法。

### 4.1 模 26 的乘法逆元

因为 26 不是素数，$Z_{26}$ 中并非所有数都有乘法逆元。教材表 A.1 列出模 26 的所有乘法逆元：

| 值 | 逆元 | | 值 | 逆元 |
|---|---|---|---|---|
| 1 | 1 | | 15 | 7 |
| 3 | 9 | | 17 | 23 |
| 5 | 21 | | 19 | 11 |
| 7 | 15 | | 21 | 5 |
| 9 | 3 | | 23 | 17 |
| 11 | 19 | | 25 | 25 |

例如 $3 \times 9 = 27 \equiv 1 \pmod{26}$，所以 3 和 9 互为逆元。

### 4.2 模 26 上 $2 \times 2$ 矩阵求逆示例

求 $A = \begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix}$ 在 $Z_{26}$ 上的逆矩阵。

$$\det(A) = (4 \times 6) - (3 \times 9) \bmod{26} = 24 - 27 \bmod{26} = -3 \bmod{26} = 23$$

查表得 $(\det(A))^{-1} = 23^{-1} \bmod{26} = 17$。

应用伴随矩阵公式：

$$A^{-1} = (\det(A))^{-1}\begin{pmatrix} \text{cof}_{11} & \text{cof}_{21} \\ \text{cof}_{12} & \text{cof}_{22} \end{pmatrix} \bmod{26}$$

其中 $\text{cof}_{11} = 6$，$\text{cof}_{21} = -3$，$\text{cof}_{12} = -9$，$\text{cof}_{22} = 4$。

$$A^{-1} = 17 \times \begin{pmatrix}6 & -3 \\ -9 & 4\end{pmatrix} \bmod{26} = \begin{pmatrix}102 & -51 \\ -153 & 68\end{pmatrix} \bmod{26} = \begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix}$$

**验证**：

$$AA^{-1} = \begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix}\begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix} \bmod{26} = \begin{pmatrix}105 & 52 \\ 234 & 105\end{pmatrix} \bmod{26} = \begin{pmatrix}1 & 0 \\ 0 & 1\end{pmatrix}$$

### 4.3 Hill 密码的完整计算实例

教材以 3.2 节的 Hill 密码为例，演示 $3 \times 3$ 矩阵在 $Z_{26}$ 上的加密和解密全过程。

**加密密钥**：

$$K = \begin{pmatrix}17 & 17 & 5 \\ 21 & 18 & 21 \\ 2 & 2 & 19\end{pmatrix}$$

**加密**：明文 $P = (15 \ 0 \ 24)$，加密公式 $C = PK \bmod{26}$。

$$\begin{aligned} C &= (15 \ 0 \ 24)\begin{pmatrix}17 & 17 & 5 \\ 21 & 18 & 21 \\ 2 & 2 & 19\end{pmatrix} \bmod{26} \\ &= (15 \times 17 + 0 \times 21 + 24 \times 2 \quad 15 \times 17 + 0 \times 18 + 24 \times 2 \quad 15 \times 5 + 0 \times 21 + 24 \times 19) \bmod{26} \\ &= (255 + 0 + 48 \quad 255 + 0 + 48 \quad 75 + 0 + 456) \bmod{26} \\ &= (303 \quad 303 \quad 531) \bmod{26} = (17 \quad 17 \quad 11) \end{aligned}$$

**解密密钥**（$K^{-1}$ 的计算）：

首先计算行列式：

$$\begin{aligned} \det(K) &= 17 \times 18 \times 19 + 17 \times 21 \times 2 + 5 \times 21 \times 2 - 2 \times 18 \times 5 - 2 \times 21 \times 17 - 19 \times 21 \times 17 \bmod{26} \\ &= 5814 + 714 + 210 - 180 - 714 - 6783 \bmod{26} = -939 \bmod{26} = 23 \end{aligned}$$

$(\det(K))^{-1} = 23^{-1} \bmod{26} = 17$（查表）。

计算余子式（转置排列）得到：

$$K^{-1} = \begin{pmatrix}4 & 9 & 15 \\ 15 & 17 & 6 \\ 24 & 0 & 17\end{pmatrix}$$

**解密**：密文 $C = (17 \ 17 \ 11)$，解密公式 $P = CK^{-1} \bmod{26}$。

$$\begin{aligned} P &= (17 \ 17 \ 11)\begin{pmatrix}4 & 9 & 15 \\ 15 & 17 & 6 \\ 24 & 0 & 17\end{pmatrix} \bmod{26} \\ &= (17 \times 4 + 17 \times 15 + 11 \times 24 \quad 17 \times 9 + 17 \times 17 + 11 \times 0 \quad 17 \times 15 + 17 \times 6 + 11 \times 17) \bmod{26} \\ &= (68 + 255 + 264 \quad 153 + 289 + 0 \quad 255 + 102 + 187) \bmod{26} \\ &= (587 \quad 442 \quad 544) \bmod{26} = (15 \quad 0 \quad 24) \end{aligned}$$

成功恢复明文 $(15 \ 0 \ 24)$。

## 本章关键术语整理

| 类别 | 术语 |
|---|---|
| 基础概念 | 行向量、列向量、$m \times n$ 矩阵、转置、单位矩阵 $I$ |
| 矩阵运算 | 矩阵加法、矩阵乘法、标量乘法、点积 |
| 行列式 | 行列式 $\det(A)$、余子式 $\text{cof}_{ij}(A)$、按行/列展开 |
| 逆矩阵 | 逆矩阵 $A^{-1}$、伴随矩阵、$AA^{-1} = I$ |
| 模运算 | $Z_n$、模 $n$ 运算、模 $n$ 乘法逆元、Hill 密码 |

## 学习目标自检

1. 能否说明矩阵乘法的维度条件：$(m \times n) \cdot (n \times p) = (m \times p)$？
2. 能否用余子式展开法计算 $3 \times 3$ 矩阵的行列式？
3. 能否写出逆矩阵的伴随矩阵公式（注意余子式转置）？
4. 能否在 $Z_{26}$ 上用查表法求乘法逆元并完成矩阵求逆？
5. 能否按照 Hill 密码的加密公式 $C = PK \bmod{26}$ 完整执行一次加密和解密？
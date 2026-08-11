---
title: 第 10 章 其他公钥密码体制 - 学习目标笔记
aliases:
  - 密码编码学与网络安全 第 10 章 Note
  - 第十章 其他公钥密码体制 学习目标笔记
tags:
  - course
  - cryptography
  - public_key
  - diffie_hellman
  - elgamal
  - elliptic_curve
  - textbook_source
  - note
category: note
source: 密码编码学与网络安全 原理与实践 第八版
chapter: 10
section: 10.1-10.4
document_type: textbook-note
basis: strictly_textbook_original
created: 2026-08-08
updated: 2026-08-08
---
# 第 10 章 其他公钥密码体制

## 0. 学习目标总览

教材给出本章六个学习目标：

1. 定义 Diffie-Hellman 密钥交换。
2. 理解中间人攻击。
3. 概述 ElGamal 密码系统。
4. 理解椭圆曲线算术。
5. 概述椭圆曲线密码。
6. 介绍使用非对称密码生成伪随机数的两种技术。

本章介绍 RSA 以外的三类重要公钥体制：[[../../../../../Course Notes/密码学 (Cipher)/Key Management and the Public-Key Revolution/Key Management and the Public-Key Revolution#Diffie-Hellman密钥交换协议|Diffie-Hellman]] （最早、最简单的公钥算法之一），[[../../../../../Course Notes/密码学 (Cipher)/Public-Key Encryption (I)/Public-Key Encryption (I)#ElGamal公钥加密方案|ElGamal 密码体制]]（基于离散对数），以及椭圆曲线密码学（ECC，可在更短密钥下提供与 RSA 相当的安全性）。另外的，公钥算法的目的是让两个用户安全地交换密钥，以便在后续通信中使用该密钥对称加密消息。公钥算法本身只限于交换密钥值。

```text
其他公钥密码体制
  |
  |-- Diffie-Hellman 密钥交换
  |     |-- 离散对数困难问题
  |     |-- 算法：α^(X_A X_B) mod q
  |     |-- 中间人攻击：缺乏认证
  |
  |-- ElGamal 密码体制
  |     |-- 基于离散对数
  |     |-- 加密使用随机一次密钥 k
  |     `-- 密文为一对值 (C1, C2)
  |
  `-- 椭圆曲线密码学
        |-- 交换群回顾
        |-- 实数域上椭圆曲线：几何加法
        |-- Z_p 上椭圆曲线：模运算
        |-- GF(2^m) 上椭圆曲线
        |-- ECC 密钥交换
        `-- ECC 加密/解密
```

## 1. 学习目标 1：Diffie-Hellman 密钥交换

### 1.1 离散对数问题

Diffie-Hellman 的有效性依赖于**计算离散对数的困难性**。对于素数 $p$ 的本原根 $a$，幂 $a \bmod p, a^2 \bmod p, \ldots, a^{p-1} \bmod p$ 各不相同，恰是 $1$ 到 $p-1$ 所有整数的一个排列。

对任意整数 $b$，存在唯一指数 $i$（$0 \leqslant i \leqslant p-1$）使

$$
b \equiv a^i \pmod{p}
$$

$i$ 称为 $b$ 以 $a$ 为底模 $p$ 的**离散对数**，记为 $\operatorname{dlog}_{a,p}(b)$。

### 1.2 算法

Diffie-Hellman 密钥交换使用两个公开参数：素数 $q$ 及其本原根 $\alpha$。

| 步骤 | Alice | Bob |
| --- | --- | --- |
| 1 | 选随机私钥 $X_A < q$ | 选随机私钥 $X_B < q$ |
| 2 | 计算公钥 $Y_A = \alpha^{X_A} \bmod q$ | 计算公钥 $Y_B = \alpha^{X_B} \bmod q$ |
| 3 | 交换公钥 | 交换公钥 |
| 4 | 计算 $K = (Y_B)^{X_A} \bmod q$ | 计算 $K = (Y_A)^{X_B} \bmod q$ |

两者得到相同的共享密钥，因为

$$
K = (Y_B)^{X_A} \bmod q = (\alpha^{X_B})^{X_A} \bmod q = \alpha^{X_B X_A} \bmod q = (\alpha^{X_A})^{X_B} \bmod q = (Y_A)^{X_B} \bmod q
$$

敌手只能观察到 $q, \alpha, Y_A, Y_B$。要确定 $K$，必须求解离散对数，如 $X_B = \operatorname{dlog}_{\alpha,q}(Y_B)$。

![[attachments/fig_10_1_pdf220.png|620]]

*图 10.1 Diffie-Hellman 密钥交换*

### 1.3 教材示例

取 $q = 353,\ \alpha = 3$，Alice 选 $X_A = 97$，Bob 选 $X_B = 233$：

| 计算 | Alice | Bob |
| --- | --- | --- |
| 公钥 | $Y_A = 3^{97} \bmod 353 = 40$ | $Y_B = 3^{233} \bmod 353 = 248$ |
| 共享密钥 | $K = 248^{97} \bmod 353 = 160$ | $K = 40^{233} \bmod 353 = 160$ |

敌手只能看到 $(353, 3, 40, 248)$，要破解需解 $3^a \bmod 353 = 40$ 或 $3^b \bmod 353 = 248$。对于大数，这类计算不可行。

### 1.4 密钥交换协议

图 10.1 中给出的简单协议使用了 Diffie-Hellman 计算方法。

假设用户 A 希望与用户 B 建立连接，并使用密钥对这次连接上的消息加密：
1. 用户 A 生成一次私钥 $X_A$、计算 $Y_A$，并将 $Y_A$ 发给用户 B；
2. 用户 B 也生成私钥 $X_B$、计算 $Y_B$，并将 $Y_B$ 发给用户 A。

这样，用户 A 和 B 现在都可算出密钥。当然，在通信前用户 A 和 B 都应已知公开值 $q$ 和 $\alpha$。用户 A 也可选择 $q$ 和 $\alpha$，并将 $q$ 和 $\alpha$ 放入第一条消息。

下面是使用 Diffie-Hellman 算法的另一个例子。
假设有一组用户（如 LAN 上的所有用户），每个用户都生成一个在较长时间内有效的秘钥 $X_i$（对于用户 i），并计算公开值 $Y_i$。这些公开值与公开全局量 $q$ 和 $\alpha$ 都存储在某个中心目录中。在任何时刻，用户 j 都可访问用户 i 的公开值、算出密钥，并用密钥将消息加密后发给用户 i。若中心目录可信，则这种形式的通信既可保证保密性，又可保证某种程度的认证。因为只有 i 和 j 能够确定密钥，所以其他用户均不能读取消息（保密性）；接收方 i 知道只有用户 j 能用该密钥生成消息（认证）。但是，这种方法不能抗重放攻击。

## 2. 学习目标 2：中间人攻击

### 2.1 攻击过程

Diffie-Hellman 简单协议无法抵抗中间人攻击，因为它不认证通信参与方。设 Darth 位于 Alice 和 Bob 之间，攻击步骤如下：

1. Darth 生成两对私钥-公钥 $(X_{D1}, Y_{D1})$ 和 $(X_{D2}, Y_{D2})$。
2. Alice 发送 $Y_A$，被 Darth 截获；Darth 将 $Y_{D1}$ 发给 Bob，并计算 $K_2 = (Y_A)^{X_{D2}} \bmod q$。
3. Bob 依 $Y_{D1}$ 计算 $K_1 = (Y_{D1})^{X_B} \bmod q$。
4. Bob 发送 $Y_B$，被 Darth 截获；Darth 将 $Y_{D2}$ 发给 Alice，并计算 $K_1 = (Y_B)^{X_{D1}} \bmod q$。
5. Alice 依 $Y_{D2}$ 计算 $K_2 = (Y_{D2})^{X_A} \bmod q$。

结果：Bob 与 Darth 共享 $K_1$，Alice 与 Darth 共享 $K_2$。此后 Darth 可解密 Alice 发来的任何消息，并可任意修改后以 $K_1$ 重新加密发给 Bob。

![[attachments/fig_10_2_pdf222.png|620]]

*图 10.2 中间人攻击*

### 2.2 防御方法

这种缺陷可通过数字签名和公钥证书克服，详见第 13 章和第 14 章。

## 3. 学习目标 3：ElGamal 密码体制

### 3.1 算法描述

ElGamal 由 T. ElGamal 于 1984 年提出，基于离散对数。全局元素为素数 $q$ 及其本原根 $\alpha$。

**密钥生成（Alice）：**

1. 选随机私钥 $X_A$，$0 < X_A < q-1$。
2. 计算公钥 $Y_A = \alpha^{X_A} \bmod q$。
3. 公钥为 $\{q, \alpha, Y_A\}$，私钥为 $X_A$。

**加密（Bob）：**

1. 将消息表示为整数 $M$（$0 \leqslant M \leqslant q-1$）。
2. 选随机整数 $k$（$1 \leqslant k \leqslant q-1$）。
3. 计算一次密钥 $K = (Y_A)^k \bmod q$。
4. 计算密文对：

$$
C_1 = \alpha^k \bmod q,\qquad C_2 = KM \bmod q
$$

**解密（Alice）：**

1. 恢复 $K = (C_1)^{X_A} \bmod q$。
2. 恢复 $M = (C_2 K^{-1}) \bmod q$。

> $C_1$​ 的数学作用正是将随机数 $k$ 以离散指数形式传递给接收方，使得接收方能够利用自身私钥 $x$ 计算出与加密端相同的临时共享秘密，从而解开掩码。

### 3.2 解密正确性

恢复密钥的等价性：$K = (Y_A)^k \bmod q = (\alpha^{X_A})^k \bmod q = \alpha^{kX_A} \bmod q = (C_1)^{X_A} \bmod q$。

恢复明文：$C_2 K^{-1} \bmod q = KMK^{-1} \bmod q = M \bmod q = M$。

### 3.3 过程概要

1. Bob 生成随机数 $k$；
2. Bob 用 Alice 的公钥生成一次密钥 $K$；
3. Bob 在 $C_1$ 中发送"用 $\alpha$ 加密的 $k$"，为 Alice 提供足够信息恢复 $K$；
4. Bob 用 $K$ 加密消息（$C_2$）；
5. Alice 用私钥由 $C_1$ 恢复 $K$；
6. Alice 用 $K^{-1}$ 解密 $C_2$ 得到明文。

![[attachments/fig_10_3_pdf223.png|620]]

*图 10.3 ElGamal 密码体制*

### 3.4 教材示例

取 $q = 19$、$\alpha = 10$。Alice 选 $X_A = 5$，计算 $Y_A = 10^5 \bmod 19 = 3$。公钥为 $\{19, 10, 3\}$，私钥为 $5$。

Bob 要发送 $M = 17$：选 $k = 6$，计算 $K = 3^6 \bmod 19 = 7$；$C_1 = 10^6 \bmod 19 = 11$；$C_2 = 7 \times 17 \bmod 19 = 5$。密文为 $(11, 5)$。

Alice 解密：$K = 11^5 \bmod 19 = 7$；$K^{-1} \bmod 19 = 11$（因为 $7 \times 11 = 77 \equiv 1 \pmod{19}$）；$M = 5 \times 11 \bmod 19 = 17$。

## 4. 学习目标 4：椭圆曲线算术

### 4.1 交换群回顾

Diffie-Hellman 使用非零整数模 $q$ 的乘法群（幂运算是重复乘）；[[../../../../../Course Notes/密码学 (Cipher)/Number Theory and Cryptographic Hardness Assumptio 2bea-79ab/Number Theory and Cryptographic Hardness Assumptions (II)#4. 素数阶群II：椭圆曲线群|ECC]] 使用椭圆曲线上的加法群（乘法是重复加）。两者都归结为离散对数问题：前者求 $\alpha^k \bmod q$ 中的 $k$，后者求 $kP$ 中的 $k$。

交换群 $G$ 的公理：封闭性（A1）、结合律（A2）、单位元（A3）、逆元（A4）、交换律（A5）。

### 4.2 实数域上的椭圆曲线

椭圆曲线并不是椭圆，而是三次方程描述的曲线。一般来说，椭圆曲线的 **三次方程** 为

$$
y^2 +axy +by = x^3 +cx^2 +dx +e
$$

教材使用简化形式：

$$
y^2 = x^3 + ax + b \tag{10.1}
$$

定义中还包含**无穷远点** $O$。集合 $E(a,b)$ 由满足方程的所有点 $(x,y)$ 和 $O$ 组成。条件

$$
4a^3 + 27b^2 \neq 0 \tag{10.2}
$$

确保可在 $E(a,b)$ 上定义群。

![[attachments/fig_10_4_pdf226.png|620]]

*图 10.4 椭圆曲线示例*

加法的几何描述可以证明，若式（10.1）中的参数 a 和 b 满足条件

$$
4a^3 + 27b^2 \neq 0 \tag{10.2}
$$

则可基于集合 $E(a,b)$ 定义一个群。要在 $E(a,b)$ 上定义一个群，必须定义一个称为加法的运算，并用+表示，其中 a 和 b 满足式（10.2）。

**几何加法规则：** 若椭圆曲线上三个点共线，则它们的和为 $O$。

1. $O$ 是加法单位元。任意点 $P$，$P + O = P$。
2. 点 $P = (x_P, y_P)$ 的负元 $-P = (x_P, -y_P)$，满足 $P + (-P) = O$。
3. 不同点 $P \neq Q$ 相加：画直线交曲线于第三点 $R$，则 $P + Q = -R$。
4. 同一点自加（倍点）：画切线交曲线于第二点 $R$，则 $2P = -R$。

代数上，$P = (x_P, y_P),\ Q = (x_Q, y_Q),\ P \neq -Q$ 时，$P + Q = (x_R, y_R)$：

$$
x_R = \lambda^2 - x_P - x_Q,\qquad y_R = \lambda(x_P - x_R) - y_P
$$

其中当 $P \neq Q$ 时 $\lambda = (y_Q - y_P)/(x_Q - x_P)$；当 $P = Q$ 时 $\lambda = (3x_P^2 + a)/(2y_P)$。

### 4.3 $Z_p$ 上的椭圆曲线（素数域）

将曲线限制在有限域 $Z_p$ 上，系数和变量取值于 $\{0, 1, \ldots, p-1\}$，所有运算模 $p$。方程形式不变：

$$
y^2 \bmod p = (x^3 + ax + b) \bmod p \tag{10.5}
$$

$E_p(a,b)$ 由满足方程的 $(x,y)$ 对和 $O$ 组成，$0 \leqslant x, y < p$。

加法规则与实数域完全相同，只是以模 $p$ 运算执行。$\lambda$ 计算中涉及分数时，需要在 $Z_p$ 中求乘法逆元。

![[attachments/fig_10_5_pdf228.png|620]]

*图 10.5 $Z_p$ 上的椭圆曲线*

定义在 $E_p(a,b)$ 上的加法运算规则，与定义在实数域上的椭圆曲线的运算规则一致。对任何点 $P, Q \in E_p(a,b)$：

1. $P + O = P$。

2. 若 $P=(x_P,y_P)$，则 $P + (x_P, -y_P \bmod p) = O$。点 $(x_P, -y_P \bmod p)$ 是 P 的负元，记为 $-P$。例如，对 $E_{23}(1,1)$ 中的点 $P=(13,7)$，有 $-P=(13,-7)$，而 $-7 \bmod 23 = 16$。因此，$-P=(13,16)$，该点也在 $E_{23}(1,1)$ 中。

3. 若 $P=(x_P,y_P),\ Q=(x_Q,y_Q)$，且 $P \neq -Q$，则 $R = P + Q = (x_R,y_R)$ 由下列规则确定：

$$
x_R = (\lambda^2 - x_P - x_Q) \bmod p,\quad y_R = (\lambda(x_P - x_R) - y_P) \bmod p
$$

式中，

$$
\lambda = \begin{cases}
\left(\dfrac{y_Q - y_P}{x_Q - x_P}\right) \bmod p, & P \neq Q \\
\left(\dfrac{3x_P^2 + a}{2y_P}\right) \bmod p, & P = Q
\end{cases}
$$

4. 将乘法定义为重复相加，如 $4P = P + P + P + P$。

**教材示例：** $E_{23}(1,1)$ 即 $y^2 = x^3 + x + 1 \pmod{23}$。

- $P = (3,10),\ Q = (9,7)$：$\lambda = (-3/6) \bmod 23 = (-1/2) \bmod 23 = 11$，得 $P+Q = (17,20)$。
- $2P$：$\lambda = (3(3^2)+1)/(2 \times 10) \bmod 23 = (5/20) \bmod 23 = (1/4) \bmod 23 = 6$，得 $2P = (7,12)$。

有限群 $E_p(a,b)$ 中点数 $N$ 的范围为

$$
p + 1 - 2\sqrt{p} \leqslant N \leqslant p + 1 + 2\sqrt{p}
$$

注意到 $E_p(a,b)$ 中的点数约等于 $Z_p$ 中元素的个数，即 $p$ 个元素。

### 4.4 $\mathrm{GF}(2^m)$ 上的椭圆曲线

有限域 $\mathrm{GF}(2^m)$ 上适合 ECC 的三次方程不同于 $Z_p$ 上的形式：

$$
y^2 + xy = x^3 + ax^2 + b \tag{10.7}
$$

其中变量和系数均为 $\mathrm{GF}(2^m)$ 元素。只要 $b \neq 0$，$E_{2^m}(a,b)$ 构成有限交换群。

加法规则与素数域类似但具体公式不同。对 $P \neq Q$：

$$
x_R = \lambda^2 + \lambda + x_P + x_Q + a,\quad y_R = \lambda(x_P + x_R) + x_R + y_P
$$

其中 $\lambda = (y_Q + y_P)/(x_Q + x_P)$。倍点 $2P$：

$$
x_R = \lambda^2 + \lambda + a,\quad y_R = x_P^2 + (\lambda + 1)x_R
$$

其中 $\lambda = x_P + y_P/x_P$。负元为 $-P = (x_P, x_P + y_P)$。

![[attachments/fig_10_6_pdf229.png|620]]

*图 10.6 $E_{2^4}(g^4, 1)$ 上的点*

> 相关椭圆曲线辨析见[[attachments/椭圆曲线有限域运算基础理论辨析|椭圆曲线有限域运算基础理论辨析]]。

## 5. 学习目标 5：椭圆曲线密码学

### 5.1 椭圆曲线离散对数问题

椭圆曲线密码的安全性基于椭圆曲线离散对数问题：已知 $Q$ 和 $P$（$Q, P \in E_p(a,b)$），求 $k < p$ 使 $Q = kP$。给定 $k$ 和 $P$ 计算 $Q$ 容易；给定 $Q$ 和 $P$ 求 $k$ 困难。

教材例：$E_{23}(9,17)$，$P = (16,5)$，$Q = (4,5)$。由 $P, 2P, \ldots$ 计算至 $9P = (4,5) = Q$，故 $k = 9$。实际使用的 $k$ 极大，穷举不可行。

### 5.2 ECC Diffie-Hellman 密钥交换

参数：椭圆群 $E_q(a,b)$、基点 $G$（其阶 $n$ 为大值，即 $nG = O$）。

| 步骤 | Alice | Bob |
| --- | --- | --- |
| 1 | 选私钥 $n_A < n$ | 选私钥 $n_B < n$ |
| 2 | 计算公钥 $P_A = n_A \times G$ | 计算公钥 $P_B = n_B \times G$ |
| 3 | 交换公钥 | 交换公钥 |
| 4 | 计算 $K = n_A \times P_B$ | 计算 $K = n_B \times P_A$ |

因为 $n_A \times P_B = n_A \times (n_B \times G) = n_B \times (n_A \times G) = n_B \times P_A$，双方得到同一秘密点。

**教材示例：** $p=211$，$E_p(0,-4)$（即 $y^2 = x^3 - 4$），$G=(2,2)$。$n_A=121$，$P_A = 121(2,2) = (115,48)$；$n_B=203$，$P_B = 203(2,2) = (130,203)$。共享密钥 $121(130,203) = 203(115,48) = (161,69)$。通常取 $x$ 坐标作为对称会话密钥。

![[attachments/fig_10_7_pdf231.png|620]]

*图 10.7 ECC Diffie-Hellman 密钥交换*

### 5.3 ECC 加密/解密

首先将消息 $m$ 编码为 $E_q(a,b)$ 中的点 $P_m$。用户 B 有私钥 $n_B$ 和公钥 $P_B = n_B \times G$。

> 注意，不能简单地将消息编码为一点的 $x$ 坐标或 $y$ 坐标，因为并非所有坐标都在 $E_q(a,b)$ 中。

**加密（Alice）：** 选随机整数 $k$，计算密文 $C_m = \{kG,\ P_m + kP_B\}$。

**解密（Bob）：** $P_m = (P_m + kP_B) - n_B(kG) = P_m + k(n_B G) - n_B(kG) = P_m$。

**教材示例：** $q=257$，$E_{257}(0,-4)$（即 $y^2 = x^3 - 4$），$G=(2,2)$。Bob 私钥 $n_B=101$，公钥 $P_B=101(2,2)=(197,167)$。Alice 选 $k=41$，计算 $kG=(136,128)$，$kP_B=(68,84)$，$P_m+kP_B=(246,174)$。密文为 $\{(136,128), (246,174)\}$。Bob 解密：$(246,174) - 101(136,128) = (246,174) - (68,84) = P_m$。

### 5.4 ECC 的安全性

ECC 的优点在于可比 RSA 使用短得多的密钥获得相同安全性，从而降低处理开销。ECC 正在被纳入公钥密码学标准（如 IEEE P1363）。ECC 的安全性取决于椭圆曲线离散对数问题的困难性；对适当大小的参数，目前没有比穷举搜索更有效的通用攻击方法。

## 本章关键术语整理

| 术语 | 教材含义 |
| --- | --- |
| Diffie-Hellman 密钥交换 | 两方在不共享预先秘密的条件下，通过交换公钥生成共享秘密密钥的协议。 |
| 离散对数 | 使 $b \equiv a^i \pmod{p}$ 成立的指数 $i$；求离散对数在计算上被认为困难。 |
| 本原根 | 其幂模 $p$ 能生成 $1$ 到 $p-1$ 全部整数的整数。 |
| 中间人攻击 | 攻击者截获双方通信，分别与双方建立独立密钥以窃听或篡改。 |
| ElGamal | 基于离散对数的公钥加密体制，使用随机一次密钥 $k$。 |
| 椭圆曲线 | 由三次方程 $y^2 = x^3 + ax + b$ 定义、可构成交换群的点的集合。 |
| 椭圆曲线离散对数 | 已知 $Q$ 和 $P$ 求 $k$ 使 $Q = kP$；这是 ECC 安全性的基础。 |
| 无穷远点 $O$ | 椭圆曲线群的单位元。 |
| ECDH | 用椭圆曲线实现的 Diffie-Hellman 密钥交换。 |

## 学习目标自检

1. 能写出 DH 密钥交换的全过程，并说明为何 $\alpha^{X_A X_B} \bmod q$ 是双方共享的密钥。
2. 能解释离散对数的定义，以及它为何是 DH 安全性的基础。
3. 能逐步骤描述中间人攻击如何通过两对密钥分别欺骗 Alice 和 Bob。
4. 能写出 ElGamal 的密钥生成、加密（含一次密钥 $k$）和解密公式，并解释 $K^{-1}$ 的作用。
5. 能在实数域椭圆曲线上用几何方式说明点加和倍点的规则，并写出代数公式。
6. 能在 $Z_p$ 椭圆曲线上完成模 $p$ 的点加运算（含求模逆元），并理解 $E_p(a,b)$ 的点数范围。
7. 能区分 $Z_p$ 和 $\mathrm{GF}(2^m)$ 上椭圆曲线方程和加法公式的不同。
8. 能写出 ECC Diffie-Hellman 密钥交换的私钥-公钥-共享密钥三段计算。
9. 能说明 ECC 加密/解密中 $kG$ 和 $P_m + kP_B$ 的作用，以及 Bob 如何用自己的私钥恢复 $P_m$。
10. 能概括 ECC 相比 RSA 的主要优势（更短密钥实现同等安全性）。

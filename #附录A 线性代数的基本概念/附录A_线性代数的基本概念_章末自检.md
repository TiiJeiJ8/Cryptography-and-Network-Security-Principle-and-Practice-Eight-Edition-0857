# 附录A 线性代数的基本概念 — 章末学习目标自检

---

## 1. 能说明矩阵乘法的维度条件：$(m \times n) \cdot (n \times p) = (m \times p)$。

矩阵 $A$ 和 $B$ 可乘的充要条件是：**$A$ 的列数等于 $B$ 的行数**。

设 $A$ 为 $m \times n$ 矩阵，$B$ 为 $n \times p$ 矩阵，则 $C = AB$ 为 $m \times p$ 矩阵，其元素：

$$c_{ij} = \sum_{k=1}^{n} a_{ik} \cdot b_{kj}$$

即 $C$ 的第 $i$ 行第 $j$ 列元素等于 $A$ 的第 $i$ 行与 $B$ 的第 $j$ 列的点积。

两个特殊情形：
- 行向量 $\times$ 矩阵 $\to$ 行向量：$D = XA$，$d_j = \sum_{k=1}^{m} x_k a_{kj}$；
- 矩阵 $\times$ 列向量 $\to$ 列向量：$E = AY$，$e_i = \sum_{k=1}^{n} a_{ik} y_k$。

---

## 2. 能用余子式展开法计算 $3 \times 3$ 矩阵的行列式。

**余子式** $\text{cof}_{ij}(A)$：删去 $A$ 的第 $i$ 行第 $j$ 列后剩余子矩阵的行列式，乘以符号 $(-1)^{i+j}$（$i+j$ 偶为正，奇为负）。

按任意行 $i$ 展开：

$$\det(A) = \sum_{j=1}^{n} a_{ij} \cdot \text{cof}_{ij}(A)$$

或按任意列 $j$ 展开：

$$\det(A) = \sum_{i=1}^{n} a_{ij} \cdot \text{cof}_{ij}(A)$$

**教材示例**：$A = \begin{pmatrix} 2 & 4 & 3 \\ 6 & 1 & 5 \\ -2 & 1 & 3 \end{pmatrix}$，按第 2 行展开：

$$\begin{aligned} \det(A) &= 6 \cdot \text{cof}_{21} + 1 \cdot \text{cof}_{22} + 5 \cdot \text{cof}_{23} \\ &= 6 \cdot \left(-\det\begin{pmatrix}4 & 3 \\ 1 & 3\end{pmatrix}\right) + 1 \cdot \det\begin{pmatrix}2 & 3 \\ -2 & 3\end{pmatrix} + 5 \cdot \left(-\det\begin{pmatrix}2 & 4 \\ -2 & 1\end{pmatrix}\right) \\ &= 6 \times (-9) + 1 \times 12 + 5 \times (-10) = -92 \end{aligned}$$

---

## 3. 能写出逆矩阵的伴随矩阵公式（注意余子式转置）。

方阵 $A$ 可逆当且仅当 $\det(A) \neq 0$，其逆矩阵满足 $AA^{-1} = A^{-1}A = I$。

$$A^{-1} = \frac{1}{\det(A)} \cdot \text{adj}(A)$$

其中**伴随矩阵** $\text{adj}(A)$ 是余子式矩阵的**转置**——注意 $\text{cof}_{ji}$ 位于位置 $(i,j)$：

$$\text{adj}(A) = \begin{pmatrix} \text{cof}_{11} & \text{cof}_{21} & \cdots & \text{cof}_{n1} \\ \text{cof}_{12} & \text{cof}_{22} & \cdots & \text{cof}_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ \text{cof}_{1n} & \text{cof}_{2n} & \cdots & \text{cof}_{nn} \end{pmatrix}$$

**教材示例**：对上述 $A$，$\det(A) = -92$，计算全部 9 个余子式并转置后：

$$A^{-1} = \frac{1}{92}\begin{pmatrix} 2 & 9 & -17 \\ 28 & -12 & -8 \\ -8 & 10 & 22 \end{pmatrix}$$

---

## 4. 能在 $Z_{26}$ 上用查表法求乘法逆元并完成矩阵求逆。

$Z_n$ 上矩阵求逆与普通算术相同，只是所有运算模 $n$，且除法通过乘以模逆元实现。$Z_{26}$ 中只有与 26 互素的数才有乘法逆元：

| 值 | 1 | 3 | 5 | 7 | 9 | 11 | 15 | 17 | 19 | 21 | 23 | 25 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 逆元 | 1 | 9 | 21 | 15 | 3 | 19 | 7 | 23 | 11 | 5 | 17 | 25 |

**教材示例**：$A = \begin{pmatrix}4 & 3 \\ 9 & 6\end{pmatrix}$ 在 $Z_{26}$ 上求逆。

$$\det(A) = (4 \times 6 - 3 \times 9) \bmod 26 = -3 \bmod 26 = 23$$

查表得 $23^{-1} \equiv 17 \pmod{26}$。计算余子式并转置：

$$A^{-1} = 17 \times \begin{pmatrix}6 & -3 \\ -9 & 4\end{pmatrix} \bmod 26 = \begin{pmatrix}102 & -51 \\ -153 & 68\end{pmatrix} \bmod 26 = \begin{pmatrix}24 & 1 \\ 3 & 16\end{pmatrix}$$

验证：$AA^{-1} \equiv I \pmod{26}$。

---

## 5. 能按 Hill 密码的加密公式 $C = PK \bmod 26$ 完整执行一次加密和解密。

**加密**：明文行向量 $P$ 乘以密钥矩阵 $K$，模 26 得密文 $C$：

$$C = PK \bmod 26$$

**教材示例**：$K = \begin{pmatrix}17 & 17 & 5 \\ 21 & 18 & 21 \\ 2 & 2 & 19\end{pmatrix}$，明文 $P = (15\ 0\ 24)$：

$$C = (15\ 0\ 24)K \bmod 26 = (303\ 303\ 531) \bmod 26 = (17\ 17\ 11)$$

**解密**：先求 $K$ 在 $Z_{26}$ 上的逆矩阵。$\det(K) = -939 \bmod 26 = 23$，$23^{-1} = 17$，计算余子式并转置得：

$$K^{-1} = \begin{pmatrix}4 & 9 & 15 \\ 15 & 17 & 6 \\ 24 & 0 & 17\end{pmatrix}$$

然后 $P = CK^{-1} \bmod 26$：

$$P = (17\ 17\ 11)K^{-1} \bmod 26 = (587\ 442\ 544) \bmod 26 = (15\ 0\ 24)$$

成功恢复明文。Hill 密码的安全性依赖矩阵运算提供的扩散——每个明文字符影响多个密文字符。

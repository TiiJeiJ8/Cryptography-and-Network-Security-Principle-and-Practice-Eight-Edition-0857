# 附录D 简化AES — 章末学习目标自检

---

## 1. 能列出 S-AES 的四个基本操作并说明各自作用。

S-AES 操作单位为 nibble（4 位），状态为 $2 \times 2$ nibble 矩阵，在 $GF(2^4)$（不可约多项式 $x^4 + x + 1$）上运算。

| 操作 | 作用 | 逆操作 |
|------|------|--------|
| **密钥加 AddKey** | 状态与 16 位轮密钥逐位异或；混淆密钥与数据 | 自身（异或自逆） |
| **半字节代替 SubNibbles** | 每个 nibble 查 S 盒（高 2 位选行、低 2 位选列）；提供**非线性** | InvSubNibbles 查逆 S 盒 |
| **行移位 ShiftRows** | 第 1 行不变，第 2 行两个 nibble 交换；提供行间**扩散** | 再交换一次 |
| **列混淆 MixColumns** | 在 $GF(2^4)$ 上用矩阵 $\begin{pmatrix}1 & 4 \\ 4 & 1\end{pmatrix}$ 乘状态列；提供列内**扩散** | 用逆矩阵 $\begin{pmatrix}9 & 2 \\ 2 & 9\end{pmatrix}$ |

---

## 2. 能根据 S 盒完成任意 nibble 的 SubNibbles 查表操作。

S 盒（nibble 高 2 位选行，低 2 位选列）：

| | 00 | 01 | 10 | 11 |
|---|---|---|---|---|
| **00** | 9 | 4 | A | B |
| **01** | D | 1 | 8 | 5 |
| **10** | 6 | 2 | 0 | 3 |
| **11** | C | E | F | 7 |

**教材示例**：输入 nibble `A`（二进制 1010），高 2 位 = 10（行 2），低 2 位 = 10（列 2），查得 $S[2][2] = 0$，即 `A` 映射为 `0`。

逆 S 盒用于解密时的 InvSubNibbles，是 S 盒的逆映射。S 盒基于 $GF(2^4)$ 上的求逆映射 $x \to x^{-1}$ 再经仿射变换生成，这是其抗差分和线性密码分析的结构基础。

---

## 3. 能描述 S-AES 加密的完整复合函数（含轮数）并指出第 2 轮缺少哪个操作。

S-AES 共 2 轮，加密复合函数为：

$$A_{K_2} \circ \text{SR} \circ \text{NS} \circ A_{K_1} \circ \text{MC} \circ \text{SR} \circ \text{NS} \circ A_{K_0}$$

从右到左执行：
1. **第 0 轮（初始轮）**：仅 AddKey（$A_{K_0}$）；
2. **第 1 轮**：SubNibbles $\to$ ShiftRows $\to$ MixColumns $\to$ AddKey（$A_{K_1}$）；
3. **第 2 轮（末轮）**：SubNibbles $\to$ ShiftRows $\to$ AddKey（$A_{K_2}$）。

**末轮缺少 MixColumns**——这与 AES 末轮省略列混淆的设计一致，原因是末轮再加列混淆不增加安全性且增加解密复杂度。

解密复合函数：

$$A_{K_0} \circ \text{INS} \circ \text{ISR} \circ \text{IMC} \circ A_{K_1} \circ \text{INS} \circ \text{ISR} \circ A_{K_2}$$

---

## 4. 能描述密钥扩展中 $g$ 函数的三步操作（RotNib + SubNib + RCON）。

16 位密钥分为两个 8 位字 $w_0$、$w_1$，扩展为 $w_0 \dots w_5$（组成 $K_0 = w_0 w_1$，$K_1 = w_2 w_3$，$K_2 = w_4 w_5$）：

$$w_2 = w_0 \oplus \text{RCON}(1) \oplus \text{SubNib}(\text{RotNib}(w_1))$$

$$w_3 = w_2 \oplus w_1$$

$$w_4 = w_2 \oplus \text{RCON}(2) \oplus \text{SubNib}(\text{RotNib}(w_3))$$

$$w_5 = w_4 \oplus w_3$$

$g$ 函数三步：
1. **RotNib**：将 8 位字中的两个 nibble 交换位置（循环移位）；
2. **SubNib**：对交换后的两个 nibble 分别查 S 盒；
3. **RCON**：与轮常数异或——$\text{RCON}(1) = \texttt{10000000}$，$\text{RCON}(2) = \texttt{00110000}$，用于消除对称性。

---

## 5. 能说明 S-AES 如何体现 AES 的四个核心设计原则。

| 设计原则 | S-AES 体现 | AES 对应 |
|----------|-----------|----------|
| **SPN 结构** | 代替-置换网络（非 Feistel），每轮 SubNib + ShiftRows + MixColumns | 相同结构，10 轮 |
| **S 盒（字节代替）** | SubNibbles 基于 $GF(2^4)$ 求逆 + 仿射变换，提供非线性 | SubBytes 基于 $GF(2^8)$ 求逆 + 仿射变换 |
| **行移位** | $2 \times 2$ 矩阵第 2 行交换 1 个 nibble | $4 \times 4$ 矩阵各行循环移位 0/1/2/3 字节 |
| **列混淆** | $GF(2^4)$ 上 $2 \times 2$ 矩阵乘 | $GF(2^8)$ 上 $4 \times 4$ 矩阵乘 |
| **密钥扩展** | $g$ 函数含 RotNib/SubNib/RCON | $g$ 函数含 RotWord/SubWord/Rcon |

S-AES 与 AES 结构同构，仅参数缩小（16 位分组、2 轮、$GF(2^4)$），便于手工演算以理解 AES。

---

## 6. 能解释 MixColumns 在 $GF(2^4)$ 上的矩阵乘法为何可化简为位异或运算。

MixColumns 的矩阵乘法中，系数 $4$（二进制 `0100`）对应多项式 $x^2$。在 $GF(2^4)$ 上做多项式乘法后，需对不可约多项式 $x^4 + x + 1$ 取模。利用：

$$x^4 \equiv x + 1 \pmod{x^4 + x + 1}$$

$$x^5 \equiv x^2 + x \pmod{x^4 + x + 1}$$

高次项被降为低次项，而 $GF(2^4)$ 上的加法就是按位异或。因此矩阵乘法最终展开为输入位之间的异或组合。以 $s'_{0,0}$ 为例：

$$s'_{0,0} = [(b_0 \oplus b_6),\ (b_1 \oplus b_4 \oplus b_7),\ (b_2 \oplus b_4 \oplus b_5),\ (b_3 \oplus b_5)]$$

这说明有限域上的多项式运算可高效实现为简单的位异或和移位——这是 AES 在硬件和软件中高性能实现的关键。

---
title: "附录E 生日攻击的数学基础 - 复习笔记"
aliases:
  - "密码编码学与网络安全 附录E 复习笔记"
tags: [birthday_attack]
category: textbook
source: "密码编码学与网络安全 原理与实践 第八版"
source_pdf: "Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf"
chapter: null
section: appendix
appendix: E
pdf_pages: "531-534"
book_pages: "509-512"
document_type: review-notes
status: complete
review_status: pending
created: 2026-07-24
updated: 2026-07-24
---


# 附录E 生日攻击的数学基础 - 复习笔记

## 本附录复习

| 模型 | 50%阈值 |
|---|---:|
| 匹配固定值 | $k\approx n/2$ |
| 单集合任意重复 | $k\approx1.18\sqrt n$ |
| 两个集合相交 | $k\approx0.83\sqrt n$ |

若 $n=2^m$，碰撞与集合相交均为 $2^{m/2}$ 量级。

## E.1 相关问题

固定 $H(x)$，随机输入 $y$：

$$
\Pr[H(y)=H(x)]=\frac1n
$$

$$
\Pr[k\text{次均不匹配}]
=\left(1-\frac1n\right)^k
$$

$$
\Pr[\text{至少一次匹配}]
=1-\left(1-\frac1n\right)^k
\approx\frac{k}{n}
$$

50%近似：

$$
k\approx\frac n2
$$

$m$ 位[[Course Notes/密码学 (Cipher)/Hash Functions and Applications (III)/Hash Functions and Applications (III)|哈希]]：

$$
k=2^{m-1}
\tag{E.1}
$$

## E.2 生日悖论

无重复取法：

$$
N
=365\times364\times\cdots\times(365-k+1)
=\frac{365!}{(365-k)!}
\tag{E.2}
$$

无重复概率：

$$
Q(365,k)
=\frac{365!}{(365-k)!365^k}
$$

至少一次重复：

$$
P(365,k)
=1-\frac{365!}{(365-k)!365^k}
\tag{E.3}
$$

![[attachments/fig_E_1.png]]

*图E.1 生日悖论概率曲线*

$$
P(365,23)=0.5073
$$

$$
\binom{23}{2}=253
$$

反直觉来源：题目比较任意两个人，而非其他人只与某个固定的人比较。

## E.3 有用的不等式

$$
1-x\le e^{-x},
\qquad x\ge0
\tag{E.4}
$$

![[attachments/fig_E_2.png]]

*图E.2 $1-x$ 是 $e^{-x}$ 在0处的切线*

$$
f(x)=e^{-x},
\qquad
f'(0)=-1
$$

$x$ 小时：

$$
1-x\approx e^{-x}
$$

用途：把概率乘积转成指数的求和。

## E.4 元素重复的一般情形

精确式：

$$
P(n,k)
=1-\frac{n!}{(n-k)!n^k}
\tag{E.5}
$$

乘积式：

$$
P(n,k)
=1-
\prod_{i=1}^{k-1}
\left(1-\frac{i}{n}\right)
$$

指数估计：

$$
P(n,k)
>
1-e^{-k(k-1)/(2n)}
$$

50%阈值：

$$
\ln2=\frac{k(k-1)}{2n}
\approx\frac{k^2}{2n}
$$

$$
k
=\sqrt{2(\ln2)n}
=1.18\sqrt n
\approx\sqrt n
\tag{E.6}
$$

哈希输出 $n=2^m$：

$$
k\approx\sqrt{2^m}=2^{m/2}
\tag{E.7}
$$

## E.5 两个集合间的元素重复

两个集合各有 $k$ 个元素：

$$
\Pr[Y\text{与固定 }x_1\text{无匹配}]
=\left(1-\frac1n\right)^k
$$

近似假设 $X$ 内元素不同：

$$
\Pr[X\cap Y=\varnothing]
=\left(1-\frac1n\right)^{k^2}
$$

$$
R(n,k)
=1-
\left(1-\frac1n\right)^{k^2}
>
1-e^{-k^2/n}
$$

50%阈值：

$$
\ln2=\frac{k^2}{n}
$$

$$
k
=\sqrt{(\ln2)n}
=0.83\sqrt n
\approx\sqrt n
\tag{E.8}
$$

对 $n=2^m$：

$$
k\approx2^{m/2}
$$

## 重点、难点与易错点

| 类型 | 内容 |
|---|---|
| 重点 | 补事件、精确生日公式、指数近似、$2^{m/2}$ |
| 难点 | 区分三类概率模型；理解常数因子来源 |
| 易错 | 把固定目标匹配写成生日碰撞；漏掉组合对数量；把近似当等式 |

## 三类模型对比

| 模型 | 失败概率 | 成功概率 | 比较机会 |
|---|---|---|---:|
| 固定目标 | $(1-1/n)^k$ | $1-(1-1/n)^k$ | $k$ |
| 单集合重复 | $\prod_{i=1}^{k-1}(1-i/n)$ | $P(n,k)$ | $k(k-1)/2$ |
| 双集合相交 | $(1-1/n)^{k^2}$ | $R(n,k)$ | $k^2$ |

## 教材内容与补充理解

| 类型 | 内容 |
|---|---|
| 教材内容 | 固定匹配、生日悖论、不等式、一般重复、双集合相交及式（E.1）至（E.8） |
| 补充理解 | 用比较机会数量解释线性量级与平方根量级的差异 |

## 关键词索引

| 关键词 | 定位 |
|---|---|
| 固定哈希匹配 | E.1 |
| 生日悖论、补事件 | E.2 |
| 指数不等式 | E.3 |
| 一般碰撞概率 | E.4 |
| 双集合相交 | E.5 |
| $2^{m/2}$ | E.4、E.5 |

## Related

- [[第十一章_密码学哈希函数_复习笔记|第11章 密码学哈希函数]]
- [[第十三章_数字签名_复习笔记|第13章 数字签名]]
- [[附录B_保密性和安全性度量_复习笔记|附录B 保密性度量]]

## 复盘问题

- 为什么固定目标搜索与任意碰撞的复杂度不同？
- 生日问题为什么从无重复概率入手？
- 式（E.4）如何把乘积变成平方根近似？
- 单集合与双集合的50%阈值常数为什么分别约为1.18和0.83？

---
title: 第 2 章 数论基础 - CRT 求解同余方程组
aliases:
  - 密码编码学与网络安全 第 2 章 Note
  - 第二章 数论基础 CRT 求解同余方程组
tags:
  - course
  - cryptography
  - number_theory
  - note
  - "#prime_number"
  - "#中国剩余定理"
  - "#同余方程组"
category: note
source: 密码编码学与网络安全 原理与实践 第八版
source_pdf: Reference Books/密码编码学与网络安全 原理与实践 第八版.pdf
chapter: 2
document_type: note-note
created: 2026-07-30
updated: 2026-07-30
---
# 一、概述

CRT 用于求解 **一元线性同余方程组**：

$$ \begin{cases} x \equiv a_1 \pmod{m_1}\\ x \equiv a_2 \pmod{m_2}\\ \dots\\ x \equiv a_n \pmod{m_n} \end{cases} $$

分为两种情况：

- **普通 CRT**：模数两两互质 → 一定有唯一解（模 M）
    
- **扩展 exCRT**：模数任意（可不互质） → 可能无解、可能唯一解、可能无穷解
    

**考试/做题通用原则：一律用 exCRT，兼容互质与不互质。**

# 二、普通 CRT

## 1. 条件

$m_1,m_2,\dots,m_n$ **两两互质**。

## 2. 公式

设总模数：$M = m_1m_2\cdots m_n$

设 $M_i = \dfrac{M}{m_i}$

设 $t_i$ 为 $M_i$ 在模 $m_i$ 下的逆元：$M_i t_i \equiv 1 \pmod{m_i}$

**最终解：**

$$x \equiv \sum_{i=1}^n a_i M_i t_i \pmod M$$

模 $M$ 下解唯一。

## 3. 缺点

只要任意两个模数不互质，**公式直接失效**，必须用 exCRT。

# 三、扩展 exCRT

## 1. 核心思想

**两两合并方程**，不断把两个同余式合成一个，最终只剩一个方程。

## 2. 两个方程合并推导

现有：

$$ \begin{cases} x \equiv a_1 \pmod{m_1}\\ x \equiv a_2 \pmod{m_2} \end{cases} $$

变形：$x = a_1 + k\cdot m_1$

代入第二个式子：

$$m_1 k \equiv a_2 - a_1 \pmod{m_2}$$

令：

$$A=m_1,\;B=a_2-a_1,\;Mod=m_2$$

得到标准线性同余方程：$Ak \equiv B \pmod{Mod}$

### 判定规则

设 $d=\gcd(A,Mod)$

- **若 $d \nmid B$：无解**
    
- **若 $d \mid B$：有解，合并出新同余式**
    

### 合并结果

解出 $k$ 的最小解后，可得到新方程：

$$x \equiv a_{\text{new}} \pmod{\text{lcm}(m_1,m_2)}$$

其中：$\text{lcm}(m_1,m_2)=\dfrac{m_1m_2}{d}$

## 3. 整体算法流程

1. 初始化当前解：$res=a_1,\;mod=m_1$
    
2. 遍历剩下每一组同余方程，依次合并
    
3. 一旦出现无解，直接整体无解
    
4. 最终得到：$x \equiv res \pmod{mod}$
    

**通解：$x = res + k\cdot mod,\;k\in \mathbb Z$ **

# 四、标准例题（exCRT）

求解：

$$ \begin{cases} x \equiv 4 \pmod{6}\\ x \equiv 7 \pmod{15}\\ x \equiv 10 \pmod{21} \end{cases} $$

模数：6、15、21，两两不互质，只能 exCRT。

## 第一步：合并前两个

$$ \begin{cases} x\equiv4\pmod6\\ x\equiv7\pmod{15} \end{cases} $$

$x=4+6k$

$$6k \equiv 3 \pmod{15}$$

$d=\gcd(6,15)=3,\;3\mid3$，有解。

同除 3：$2k\equiv1\pmod5 \Rightarrow k\equiv3\pmod5$

$k=3+5t$

$x=4+6(3+5t)=22+30t$

得到：$x\equiv 22 \pmod{30}$

## 第二步：合并第三个方程

$$ \begin{cases} x\equiv22\pmod{30}\\ x\equiv10\pmod{21} \end{cases} $$

$x=22+30t$

$$22+30t \equiv10\pmod{21}$$

化简：$1+9t\equiv10\pmod{21} \Rightarrow 9t\equiv9\pmod{21}$

$d=\gcd(9,21)=3,\;3\mid9$，有解。

同除 3：$3t\equiv3\pmod7 \Rightarrow t\equiv1\pmod7$

$t=1+7s$

$x=22+30(1+7s)=52+210s$

## 最终结果

$$\boldsymbol{x \equiv 52 \pmod{210}}$$

最小正整数解：$\boldsymbol{x=52}$

# 五、核心结论与易错点

## 1. 什么时候无解？

合并两方程时：$m_1k \equiv (a_2-a_1) \pmod{m_2}$

**gcd 不整除余数差 → 整体无解**

## 2. 互质/不互质本质区别

- 模数互质：一定有解，解的周期为所有模数乘积
    
- 模数不互质：解的周期为 **所有模数的 lcm**，可能无解
    

## 3. 做题万能口诀

**方程组不要批量代公式，永远两两合并；先判无解，再求通解；最终解模 lcm。**

# 六、配套代码（C++ exCRT 模板）

```cpp
typedef long long ll;
ll exgcd(ll a,ll b,ll &x,ll &y){
    if(!b){x=1;y=0;return a;}
    ll d=exgcd(b,a%b,y,x);
    y-=a/b*x;
    return d;
}

// 返回最小解，无解返回 -1
ll exCRT(int n,ll a[],ll m[]){
    ll res=a[0],mod=m[0];
    for(int i=1;i<n;i++){
        // 合并 x≡res(mod mod) 与 x≡a[i](mod m[i])
        ll x,y;
        ll d=exgcd(mod,m[i],x,y);
        ll c=a[i]-res;
        if(c%d!=0) return -1;

        ll lcm=mod/d*m[i];
        ll k=(c/d*x)%(m[i]/d);
        res+=mod*k;
        mod=lcm;
        res=(res%mod+mod)%mod;
    }
    return res;
}
```

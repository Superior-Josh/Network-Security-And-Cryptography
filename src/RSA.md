> # RSA

## 公钥与私钥

1. 随意选择两个大素数 $p$ 和 $q$, $ p\ne q$, 计算 $N=pq$.

2. 根据欧拉函数，求得 $r=\phi(N)=\phi(p) \times \phi(q)=(p-1)(q-1)$

3. 选择$e<r$ ($gcd(e,r)=1$). 求得$d, ed=1 \bmod r$

4. 销毁$p,q$

5. $pk=(N,e)$

   $sk=(N,d)$

## 加密

将消息 $m$ 转换为小于 $N$ 的非负整数 $n$：

$c=n^e\bmod N$

## 解密

$n=c^d\bmod N$

### 原理

$c^d=n^{ed}\bmod N$

已知 $ed=1 \bmod r$，即 $ed=1+h\phi(N)$，那么有

$n^{ed}=n^{1+h\phi(N)}=n\cdot (n^{\phi(N)})^h$

若 $n$ 与 $N$ 互素，则由欧拉定理得：

$n^{ed}=n\cdot (n^{\phi(N)})^h=n(1)^h=n\bmod N$

若 $n$ 与 $N$ 不互素，则不失一般性考虑 $n=pt$, 以及 $ed-1=k(q-1)$，得：

$n^{ed}=(pt)^{ed}=0=pt=n\bmod p$

$n^{ed}=n^{ed-1}n=n^{k(q-1)}n=(n^{q-1})^kn=1^kn=n\bmod q$

故 $n^{ed}=n \bmod N$ 得证

## IND-CPA安全结构：使用RSA加密

<img src="img\Encryption using RSA.png" alt="Encryption using RSA" style="zoom:67%;" />

$c=m \oplus H(r)\space||\space RSA.ENC_{sk}(r)$

## 可以直接使用 RSA 实现单向安全吗

* 不。带有小消息和小 $e$ 的 RSA 函数不安全：

  * 假设 $p, q > 10^6$. 因此$N = pq > 10^{12}$
  * 假设 $e = 7$ 和消息 $m = 5$
  * 计算 $c = m^e \bmod N = 5^7 \bmod N = 78125\bmod N = 78125$

* 密文很小，所以 $mod N$ 没有效果。$c = m^e$

  * $\log_{10}c= e\log_{10}\implies m=10^{\frac{\log C}{e}}$
  * 在我们的例子中 $log 78125 = 4.892$。因此 $\frac{log c}{e} = 0:698$

  * 恢复 $m=10^{0.698} = 5$

## 填充:PKCS #1v1:5

* 随机填充

​	$y = 0x00||0x02||r||0x00||m$

​	$c = y^e\bmod N$

* SSL的解密后处理（1998年前）

  $y = c^d mod N$
  检查 $y$ 的前两个字节。如果 $y \ne 0x00||0x02||... $ 输出 **Bad Format** ，否则输出 $m$

### 选择密文攻击：Bleichenbacher 攻击

* 得到一个密文 $c = y^e mod N$。
  * 计算 $c' = c\cdot s^e\bmod N = (y\cdot s)^e\bmod N$，取合适的 $s$。

* 检查服务器是否接受 $c'$。

* $N$ 是一个 $k$ 字节数

  * $2.2^{8(k−2)}\leq ys \bmod N < 3.2^{8(k−2)}$ 和

  * $2.2^{8(k−2)}\leq y \bmod N < 3.2^{8(k−2)}$
  * $y \bmod N < 3.2^{8(k−2)}/s$

* 以 $s' > s$ 重复上述过程。

* 通过二分查找选择 $s$。

## IND-CCA安全结构：最优非对称加密（OAEP）

<img src="img\OAEP.png" alt="OAEP" style="zoom: 50%;" />

$s=(m||0...0)\oplus G(r)$

$t=r\oplus H(s)$

$c=RSA.ENC_{pk}(s||t)$




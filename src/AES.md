> # 高级加密标准（AES）

## AES 可参数化

* 块大小128

* 密钥大小为128、192和256位

* 每个密钥大小为10、12或14轮加密

以 AES-128 为例。

AES是一个替换置换网络（不是Feistel网络）。


首先将消息安排在元素为8比特的4 × 4矩阵中，向下填充，然后向右填充。

## AES 操作

* 字节代换（S盒）：对每个字节进行独立操作。这就给出了 AES 的非线性。


* 字节排列：行移位
* 列操作：列混淆
  * 行移位和列混淆提供了 AES 中的扩散。

* 轮密钥加

这 10 轮加密过程之前会有一次密钥加法（因此，总共有 11 次密钥加法）。最后一轮稍有不同：没有列混淆操作。

<img src="img\AES.png" alt="AES" style="zoom:50%;" />

### AES 的字节操作

AES 是一种面向字节的加密算法。被各轮操作处理的 128 位“状态”（state）被视为 16 个字节，并排列成一个矩阵的形式：

<img src="img\AES_matrix.png" alt="AES_matrix" style="zoom:80%;" />

* $\oplus$ ：按位异或

* $\otimes$ ：$F_{2^8}$上的乘法 $F_2[x]$

​		如：$0x53 \otimes 0xCA / (x^8 + x^4 + x^3 + x + 1)$

​			$=(x^6+x^4+x+1)(x^7+x^6+x^3+x)/(x^8 + x^4 + x^3 + x + 1)$
​				$=0x01$

### 字节代换

<img src="img\AES-SubBytes.svg.png" alt="AES-SubBytes.svg" style="zoom: 25%;" />

矩阵中的各字节透过一个8位的[S-box](https://zh.wikipedia.org/wiki/S-box)进行转换。这个步骤提供了非线性的变换能力。

### 行移位

<img src="img\AES-ShiftRows.svg.png" alt="AES-ShiftRows.svg" style="zoom: 33%;" />

### 列混淆

<img src="img\AES-MixColumns.svg.png" alt="AES-MixColumns.svg" style="zoom: 25%;" />

将每一列分别混合，通过与矩阵相乘来实现：

<img src="img\AES_matrix_times.png" alt="AES_matrix_times" style="zoom: 67%;" />

每一列的四个字节透过[线性变换](https://zh.wikipedia.org/wiki/线性变换)互相结合。每一列的四个元素分别当作 $1,x,x_2,x_3$ 的系数，合并即为 $GF(2^8)$ 中的一个多项式，接着将此多项式和一个固定的多项式 $c(x)=3x^3+x^2+x+2$ 在模 $x^4+1$ 下相乘。

### 轮密钥加

<img src="img\AES-AddRoundKey.svg.png" alt="AES-AddRoundKey.svg" style="zoom: 33%;" />

## 密钥调度

派生轮密钥 $K_i$ 的方法如下：将 $K$ 分成四个字 $W_0,W_1,W_2$ 和 $W_3$ 各32位。

```c++
for i:=1 to 10 do
    T:=W[4i-1]<<<8
    T:=SubBytes(T)
    T:=T⊕RC[i]
    W[4i]:= W[4i−4]⊕T
    W[4i+1]:= W[4i−3]⊕W[4i]
    W[4i+2]:= W[4i−2]⊕W[4i+1]
    W[4i+3]:= W[4i−1]⊕W[4i+2]
end
```

* 轮密钥$K_i = W_{4i}, W_{4i+1}, W_{4i+2}, W_{4i+3}$

* 常数 $RC_i$ ：
# SM3: :boom::boom::boom:
### Original:<br>
http://www.sca.gov.cn/sca/xwdt/2010-12/17/1002389/files/302a3ada057c4a73830536d03e683110.pdf
### 一、符号：
    ABCDEFGH：8个字寄存器或它们的值的串联
    B(i)：第i个消息分组
    CF：压缩函数
    FF_j：布尔函数，随j的变化取不同的表达式
    GG_j：布尔函数，随j的变化取不同的表达式
    IV：初始值，用于确定压缩函数寄存器的初态
    P0：压缩函数中的置换函数
    P1：消息扩展中的置换函数
    T_j：常量，随j的变化取不同的值
    m：消息
    m′：填充后的消息
    mod：模运算
    ∧：32比特与运算
    ∨：32比特或运算
    ⊕：32比特异或运算
    ¬：32比特非运算
    +：mod 2^32算术加运算
    ≪ k：循环左移k比特运算
    ←：左向赋值运算符
### 二、常数与函数
* IV=`7380166f 4914b2b9 172442d7 da8a0600 a96f30bc 163138aa e38dee4d b0fb0e4e`
* T_ j：<br>
    `0≤j≤15：T_ j=79cc4519`<br>
    `16≤j≤63：T_ j=7a879d8a`
* FF_ j(X, Y, Z)：其中X、Y、Z为字<br>
    `0≤j≤15：X ⊕ Y ⊕ Z`<br>
    `16≤j≤63：(X ∧ Y) ∨ (X ∧ Z) ∨ (Y ∧ Z)`   
* GG_ j(X, Y, Z)：<br>
    `0≤j≤15：X ⊕ Y ⊕ Z`<br>
    `16≤j≤63：(X ∧ Y) ∨ (¬X ∧ Z)`
* P0(X)=`X ⊕ (X ≪ 9) ⊕ (X ≪ 17)`，其中X为字
* P1(X)=`X ⊕ (X ≪ 15) ⊕ (X ≪ 23)`
### 三、算法描述
#### 3.1 概述
    对长度为l(l < 2^64) 比特的消息m，SM3杂凑算法经过填充和迭代压缩，生成杂凑值，杂凑值长度为256比特。
#### 3.2 填充
    假设消息m的长度为l比特。首先将比特"1"添加到消息的末尾，再添加k个"0"，k是满足l + 1 + k ≡ 448 mod 512的最小的非负整数。然后再添加一个64位比特串，该比特串是长度l的二进制表示。填充后的消息m′ 的比特长度为512的倍数。
#### 3.3 迭代压缩
* 迭代过程：
![error](pictures/pic1.png "迭代过程")
* 消息扩展：
![error](pictures/pic2.png "消息扩展")
* 压缩函数：
![error](pictures/pic3.png "压缩函数")
#### 3.4 杂凑值
    ABCDEFGH ← V(n)
    输出256比特的杂凑值 y=ABCDEFGH
### 四、运行结果
    明文：bear
    结果：
![error](pictures/pic5.png "sm3结果")

# SM2: :boom::boom::boom:
### Original:<br>
https://blog.csdn.net/m0_57291352/article/details/123693837<br>
https://github.com/gongxian-ding/gmssl-python/blob/master/gmssl/sm2.py
### 一、算法简述
        SM2是一种非对称密码算法，基于椭圆曲线密码的公钥密码算法标准，其密钥长度256bit，
    包含数字签名、密钥交换和公钥加密，用于替换RSA/DH/ECDSA/ECDH等国际算法。可以满足电子
    认证服务系统等应用需求，由国家密码管理局于2010年12月17号发布。
        SM2采用的是ECC 256位的一种，其安全强度比RSA 2048位高，且运算速度快于RSA。随着密
    码技术和计算技术的发展，目前常用的1024位RSA算法面临严重的安全威胁，我们国家密码管理部
    门经过研究，决定采用SM2椭圆曲线算法替换RSA算法。SM2算法在安全性、性能上都具有优势。
### 二、获取公私钥
![error](pictures/pic4.png "获取公私钥")
    其中**密钥对**的生成如下：<br>
    1.产生随机整数d[1,n-2]
    2.G为基点，计算点P=(xP,yP)=[d]G
    3.密钥对为(d,P)，其中d为私钥，P为公钥
### 三、SM2加密
    M为明文字符串
    1.获取随机数k
    2.(x1,y1)=[k]G
    3.S=[h]P，h为余因子
    4.C1=(x2,y2)=[k]P
    5.t=KDF(x2||y2,klen)，klen为M的长度，KDF是sm2的密钥派生函数
    6.C2=M+t
    7.C3=Hash(x2||M||y2)
    8.C=C1||C2||C3
### 四、SM2解密
    C为密文字符串，klen为密文中C2的长度
    1.C1=C里面获取，验证C1是否满足椭圆曲线，C2长度确定，可以获取C1内容
    2.S=[h]C1，S为无穷点，退出
    3.(x2,y2)=[d]C1
    4.t=KDF(m2||y2,klen)
    5.M~=C2+t
    6.u=Hash(x2||M~||y2)
    7.M~为明文
### 五、运行结果
* sm2:
![error](pictures/pic6.png "sm2结果")
* sm2-gmssl:
![error](pictures/pic7.png "sm2-gmssl结果")

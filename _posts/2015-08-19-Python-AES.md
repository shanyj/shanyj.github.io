---
layout: post
title:  "Python AES加解密"
date:   2015-08-19 20:43:19
categories: Python
excerpt: Python AES加解密
---

* content
{:toc}


## 序

python 的PyCryto模块可以为我们提供包括ECB,CBC等方式的加解密,下面就来简单的介绍一下

---

# 安装与调用

 * 安装:pip install PyCrypto

 * 调用:
   <pre><code>
    from Crypto.Cipher import AES
    cryptor=AES.new(key, AES.MODE_CFB, iv)
    cryptor.encrypt(text)
   </code></pre>

---

# 参数介绍

 * key:16\24\32位的密钥

 * model:加解密的方式,有MODE_ECB MODE_CBC MODE_CFB

 * iv:ecb和ctr模式不需要,是一个初始化加解密向量,默认是一堆block_size大小的0
        且为binary型(block_size可以使用AES.block_size查看)

 * text必须为16的整数倍,不足补0

---

# 加密算法

 *ECB

    * 简单；

    * 有利于并行计算；

    * 误差不会被传送；

    * 不能隐藏明文的模式；

    * 可能对明文进行主动攻击；

 * CBC

    * 不容易主动攻击,安全性好于ECB,适合传输长度长的报文,是SSL、IPSec的标准。

    * 不利于并行计算；

    * 误差传递；

    * 需要初始化向量IV;

 * CFB

    * 隐藏了明文模式;

    * 分组密码转化为流模式;

    * 可以及时加密传送小于分组的数据;

    * 不利于并行计算;

    * 误差传送：一个明文单元损坏影响多个单元;

    * 唯一的IV;

---

# 例子

<pre><code>
from Crypto.Cipher import AES
from binascii import b2a_hex, a2b_hex
class AESCrypto():
    def __init__(self,key):
        self.key = key
        self.mode = AES.MODE_CBC
	print AES.block_size

    def encrypt(self,text):
        if len(text)%16!=0:
            text=text+str((16-len(text)%16)*'0')
        cryptor = AES.new(self.key,self.mode,b'0000000000000000')
        self.ciphertext = cryptor.encrypt(text)
        return b2a_hex(self.ciphertext)

    def decrypt(self,text):
        cryptor = AES.new(self.key,self.mode,b'0000000000000000')
        plain_text  = cryptor.decrypt(a2b_hex(text))
        return plain_text.rstrip('\0')
if __name__ == '__main__':
    pc = AESCrypto('1111111111111111') #初始化密钥
    e = pc.encrypt('shanyj')
    d = pc.decrypt(e) #解密
    print "加密:",e
    print "解密:",d
</code></pre>
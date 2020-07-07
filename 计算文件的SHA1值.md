**Sha简介及应用**

1、安全散列算法SHA（Secure Hash Algorithm）是美国国家安全局 （NSA） 设计，美国国家标准与技术研究院（NIST） 发布的一系列密码散列函数，包括 SHA-1、SHA-224、SHA-256、SHA-384 和 SHA-512 等变体。

2、sha能计算出一个数字消息所对应到的，长度固定的字符串（又称消息摘要）的算法。且若输入的消息不同，它们对应到不同字符串的机率很高。

3、MD5 与SHA-1均是从MD4 发展而来，其应用领域大体相似。它们的结构和强度等特征有很多相似之处，SHA-1与MD5 的最大区别在于其摘要比MD5 摘要长 32 比特。对于强行攻击，产生任何一个报文使之摘要等于给定报文摘要的难度：MD5 是2128 数量级的操作，SHA-1 是2160 数量级的操作。产生具有相同摘要的两个报文的难度：MD5是 264 是数量级的操作，SHA-1 是280 数量级的操作。因而,SHA-1 对强行攻击的强度更大。但由于SHA-1 的循环步骤比MD5 多（80:64）且要处理的缓存大（160 比特:128 比特），SHA-1 的运行速度比MD5 慢。



其实获取`sha1`和获取文件的`md5`类似只是调用方法不一样而已

```python
import hashlib


BLOCK_SIZE = 1024 * 1024 * 4

def file_iter(input_stream, size, offset=0):
    """
    读取输入流

    Args:
        input_stream: 待读取文件的二进制流
        size:         二进制流的大小

    Raises:
        IOError: 文件流读取失败
    """
    input_stream.seek(offset)
    d = input_stream.read(size)
    while d:
        yield d
        d = input_stream.read(size)

def file_sha1(file_path):
    
    sha1_obj = hashlib.sha1()

    with open(file_path, 'rb') as f:
        for block in file_iter(f, BLOCK_SIZE):
            sha1_obj.update(block)

    hash_code = sha1_obj.hexdigest()
    sha1 = str(hash_code).lower()

    return sha1
```



> 对于计算文件的 SHA-256、 SHA-512 等 直接将 方法更换一下即可



<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易
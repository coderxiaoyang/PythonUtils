如果需要将IP地址存入数据库 一般考虑到存储和查询 不是直接保存字符串类型的IP地址。
通常是将IP地址转为数字进行存储。

**将IP地址转为整型数字**

```python
import binascii
import socket

def ip2int(val):

    try:
        return int(binascii.hexlify(socket.inet_aton(val)), 16)
    except socket.error:
        return int(binascii.hexlify(socket.inet_pton(socket.AF_INET6, val)), 16)

```

其中的`socket.inet_aton`转换IPV4地址字符串（192.168.10.8）成为32位打包的二进制格式（长度为4个字节的二进制字符串），它不支持IPV6。inet_pton()支持IPV4/IPV6地址格式。

其中的`binascii.hexlify`返回二进制数据的十六进制表示

其中的`int()`函数 将十六进制转换为十进制数字

**将整型数字转为IP地址**
```python
import binascii
import socket


def int2ip(val):

    try:
        return socket.inet_ntoa(binascii.unhexlify(r'%08x' % val))
    except socket.error:
        return socket.inet_ntop(socket.AF_INET6, binascii.unhexlify(r'%032x' % val))

```

其中的`r'%08x' % val`含义为将十进制整型数字表示为十六进制

其中的`binascii.unhexlify` 返回十六进制数据的二进制

其中的`socket.inet_ntoa` 转换32位打包的IPV4地址为IP地址的标准点号分隔字符串表示。


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

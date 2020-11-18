我们首先了解一个知识点–伪随机数发生器。用于在系统需要随机数的时候，通过一系列种子值计算出来的伪随机数。因为生成一个真正意义上的“随机数”对于计算机来说是不可能的，伪随机数也只是尽可能地接近其应具有的随机性，但是因为有“种子值”，所以伪随机数在一定程度上是可控可预测的。

我们平时在使用`random`模块的时候 会有一个默认的种子值，我们可以使用`/dev/urandom`生成一个随机值，来当做`random`的种子值。

`/dev/random`和`/dev/urandom`是Linux系统中提供的随机伪设备，这两个设备的任务，是提供永不为空的随机字节数据流。很多解密程序与安全应用程序（如SSH Keys,SSL Keys等）需要它们提供的随机数据流。


```Python
import struct
import random


def urandom_seed():

    seed_num = None

    with open(r'/dev/urandom', r'rb') as f:
      	# 我们获取四个字节 字符串 将其转为 32位的大端无符号整型数据
        seed_num = struct.unpack(f'!I', f.read(4))[0]
        random.seed(seed_num)

    return seed_num
```

> seed() 方法改变随机数生成器的种子，可以在调用其他随机模块函数之前调用此函数。

测试下
```python
In [303]: urandom_seed()
Out[303]: 2049098257

In [304]: urandom_seed()
Out[304]: 1289773171

In [305]: urandom_seed()
Out[305]: 979095207
```


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

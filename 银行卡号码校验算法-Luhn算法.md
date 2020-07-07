我们在对接一些三方或者不同服务传输一些数据的时候可以使用`Luhn`算法来进行一些加密或者校验。

可以阅读 [银行卡号码校验算法(Luhn算法，又叫模10算法)](https://www.cnblogs.com/cc11001100/p/9357177.html) 这篇文章了解更多。

对某个字符串数字加上校验位
```python
from stdnum import luhn


def luhn_sign(val):

    result = None

    if val.isdigit():
        result = val + luhn.calc_check_digit(val)

    return result
```

验证某个字符串数字是否符合`luhn`算法
```python
from stdnum import luhn


def luhn_valid(val):

    return luhn.is_valid(val)
```

> 对于计算文件的 SHA-256、 SHA-512 等 直接将 方法更换一下即可


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

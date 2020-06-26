我们在实际写代码的时候 有时候不知道哪里会抛出异常 如果一味地使用`try except`将会看上去很不美观

我们实现一个上下文异常捕捉器，在可能发生异常的地方使用这个上下文 使代码更优雅

```python
from contextlib import contextmanager


@contextmanager
def catch_error():

    try:

        yield

    except Exception as err:
        # 记录到 log 或者直接打印
        print(err)
```

示例一下使用方式

```shell script
In [82]: with catch_error():
    ...:     1/0
    ...:
division by zero
```

通过这个 上下文异常捕捉器 我们在编写一些代码的时候 可以在函数内部消化掉异常 不再抛出


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

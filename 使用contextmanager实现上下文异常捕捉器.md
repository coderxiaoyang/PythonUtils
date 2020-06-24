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

![NwjAbj.jpg](https://s1.ax1x.com/2020/06/25/NwjAbj.jpg)
在代码中如果我们想获取到本机的IP地址 可以使用`socket`模块

```python
from contextlib import closing
import socket


def get_host_ip():

    result = None

    with closing(socket.socket(socket.AF_INET, socket.SOCK_DGRAM)) as _socket:
        _socket.connect((r'8.8.8.8', 53))
        result = _socket.getsockname()[0]

    return result
```

这个方法是目前见过最优雅获取本机服务器的IP方法了。没有任何的依赖，也没有去猜测机器上的网络设备信息。

而且是利用 UDP 协议来实现的，生成一个UDP包，把自己的 IP 放如到 UDP 协议头中，然后从UDP包中获取本机的IP。

这个方法并不会真实的向外部发包，所以用抓包工具是看不到的。但是会申请一个 UDP 的端口，所以如果经常调用也会比较耗时的，这里如果需要可以将查询到的IP给缓存起来，性能可以获得很大提升。

有同学好奇了 为什么上面要使用`contextlib`模块下面的`closing`方法？

我们从源码中探究下吧：
```python

import abc
import _collections_abc

class AbstractContextManager(abc.ABC):

    """An abstract base class for context managers."""

    def __enter__(self):
        """Return `self` upon entering the runtime context."""
        return self

    @abc.abstractmethod
    def __exit__(self, exc_type, exc_value, traceback):
        """Raise any exception triggered within the runtime context."""
        return None

    @classmethod
    def __subclasshook__(cls, C):
        if cls is AbstractContextManager:
            return _collections_abc._check_methods(C, "__enter__", "__exit__")
        return NotImplemented

class closing(AbstractContextManager):
    """Context to automatically close something at the end of a block.

    Code like this:

        with closing(<module>.open(<arguments>)) as f:
            <block>

    is equivalent to this:

        f = <module>.open(<arguments>)
        try:
            <block>
        finally:
            f.close()

    """
    def __init__(self, thing):
        self.thing = thing
    def __enter__(self):
        return self.thing
    def __exit__(self, *exc_info):
        self.thing.close()
```

我们从源码中可以看出`closing`函数可以将一个对象的引用变成上下文方式，之前需要手动调用`close`现在已经隐式在上下文退出的时候调用了。

如果我们不使用`closing`函数，则上面的代码需要改下

```python
import socket
 
def get_host_ip():
    ip = ""
    try:
        csocket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        csocket.connect(('8.8.8.8', 80))
        ip = csocket.getsockname()[0]
    finally:
        csocket.close()
 
    return ip
```

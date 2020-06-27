我们在工作中 如果使用的是异步编程方式 可能会遇见一些同步操作 既然是异步编程方式 再调用同步方法 效率肯定不是很高。我们可以使用`ThreadPoolExecutor`将同步函数变为异步函数。

看下我们的实现
```python
import asyncio
import functools

from concurrent.futures import ThreadPoolExecutor

class ThreadPool:

    def __init__(self, max_workers):

        self._thread_pool = ThreadPoolExecutor(max_workers)

    async def run(self, _callable, *args, **kwargs):

        future = self._thread_pool.submit(_callable, *args, **kwargs)

        return await asyncio.wrap_future(future)


class ThreadWorker:

    def __init__(self, max_workers):

        self._thread_pool = ThreadPool(max_workers)

    def __call__(self, func):

        @functools.wraps(func)
        def _wrapper(*args, **kwargs):
            return self._thread_pool.run(func, *args, **kwargs)

        return _wrapper


thread_worker = ThreadWorker(32)


@thread_worker
def some_io_block():
    return 1


asyncio.run(some_io_block()) 
# 输出 1
```


上面的`thread_executor.submit`返回的是一个`future`对象，但是并不是一个符合`asyncio`模块的`future`是不可等待的，即无法调用`await`去等待该对象。
s
通过`wrap_future`函数可以将`concurrent.futures.Future`变成`asyncio.Future`实现可等待。

这样我们可以不用显示的获取当前的`loop`也可以直接去将同步函数变成协程去执行了。

关于上面代码的更多解释可以阅读我之前总结的一篇文章[Asyncio中将同步函数改为异步调用](https://zhaobugs.com/2019/06/21/Asyncio%E4%B8%AD%E5%B0%86%E5%90%8C%E6%AD%A5%E5%87%BD%E6%95%B0%E6%94%B9%E4%B8%BA%E5%BC%82%E6%AD%A5%E8%B0%83%E7%94%A8/)


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易


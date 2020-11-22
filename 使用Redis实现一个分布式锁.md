**为何需要分布式锁**

在我们日常开发中，难免会遇到要加锁的情景。例如扣除产品库存，首先要从数据库中取出库存，进行库存判断，再减去库存。这一波操作明显不符合原子性，如果代码块不加锁，很容易因为并发导致超卖问题。咱们的系统如果是单体架构，那我们使用本地锁就可以解决问题。如果是分布式架构，就需要使用分布式锁。


**使用lua脚本的好处：**

原子操作，redis会将整个脚本作为一个整体执行，中间不会被其他命令插入，换句话说，编写脚本的过程中无需担心会出现插队、竞争等条件；
减少网络开销，在Lua脚本中可以把多个命令放在同一个脚本中运行；

**Redis + Lua 实现分布式锁**
1. 依赖于redis里提供了SETNX互斥特性的命令
    ```shell script
    SETNX：在Key不存在的情况下才会给 Key 设置值成功，否则返回0
    EXPIRE：设置过期时间，过期后自动删除key
    DEL ：主动删除Key（释放锁）
    ```
2. 主要使用以上3个命令实现分布式锁, 首先尝试使用 SETNX 设置值，如设置成功则等同于获取到锁，而其他线程不可能再设置成功，释放锁手动删除Key

3. lua 语言加锁
    ```shell script
    if (redis.call('setnx', KEYS[1], ARGV[1]) == 1) then
       --设置成功返回1，当key不存在或者不能为key设置生存时间时，返回0
       return redis.call('expire', KEYS[1], ARGV[2]);
    else
       --没有获取到锁
       return 0;
    end
    ```
   
4. lua 语言解锁
    ```shell script
    if (redis.call('get', KEYS[1]) == ARGV[1]) then
       --被删除key的数量
       return redis.call('del', KEYS[1]);
    else
       return 0;
    end
    ```
 
**Python + Redis + Lua 实现一个分布式锁**

```python
import uuid
from aioredis.commands.string import StringCommandsMixin

class MLock(AsyncContextManager):
    """基于Redis实现的分布式锁，使用with进行上下文管理
    """

    _renew_script = '''
if redis.call("get",KEYS[1]) == ARGV[1] and redis.call("ttl",KEYS[1]) > 0 then
    return redis.call("expire",KEYS[1],ARGV[2])
else
    return 0
end
'''

    _unlock_script = '''
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
'''

    def __init__(self, cache, key, expire):
        """
        cache 为Redis连接客户端
        key 为锁的redis key
        expire 为锁的过期时间
        """

        self._cache = cache
        self._expire = expire

        self._lock_tag = f'process_lock_{key}'
        self._lock_val = uuid.uuid1().hex.encode()

        self._locked = False

    @property
    def locked(self):
        """
        返回锁是否被占用
        """
        return self._locked

    async def _context_release(self):
        """
        退出上下文 释放操作
        """

        await self.release()

    async def exists(self):
        """
        判断锁是否存在
        """

        if await self._cache.exists(self._lock_tag):
            return True
        else:
            return False

    async def acquire(self, timeout=0):
        """
        获取锁
        """
        
        # 如果已经被获取 再次执行获取 则会续时
        if self._locked:

            await self.renew()

        else:
            
            # 通过 SET_IF_NOT_EXIST 来实现加锁
            params = {
                r'key': self._lock_tag,
                r'value': self._lock_val,
                r'expire': self._expire,
                r'exist': StringCommandsMixin.SET_IF_NOT_EXIST,
            }
            
            # 循环尝试加锁
            async for _ in AsyncCirculator(timeout):

                if await self._cache.set(**params):
                    self._locked = True

                if self._locked or timeout == 0:
                    break

        return self._locked

    async def wait(self, timeout=0):
        """
        指定等待时间获取锁
        """

        async for _ in AsyncCirculator(timeout):

            if not await self.exists():
                return True
        else:

            return False

    async def renew(self):
        """
        对锁进行续时操作
        """

        if self._locked:

            if await self._cache.eval(self._renew_script, [self._lock_tag], [self._lock_val, self._expire]):
                self._locked = True
            else:
                self._locked = False

        return self._locked

    async def release(self):
        """
        释放锁
        """

        if self._locked:
            await self._cache.eval(self._unlock_script, [self._lock_tag], [self._lock_val])
            self._locked = False

```


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

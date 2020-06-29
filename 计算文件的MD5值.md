有的时候 我们对接第三方的时候 在传输文件的同时需要我们将文件的md5值一并传过去 来判断文件在传输过程中是否发生改变。

数字签名(MD5的典型应用是对一段Message(字节串)产生fingerprint(指纹），以防止被“篡改”。
举个例子，你将一段话写在一个叫 `readme.txt`文件中，并对这个`readme.txt`产生一个MD5的值并记录在案，然后你可以传播这个文件给别人，别人如果修改了文件中的任何内容，你对这个文件重新计算MD5时就会发现（两个MD5值不相同）。)

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

def file_md5(file_path):
    
    md5_obj = hashlib.md5()

    with open(file_path, 'rb') as f:
        for block in file_iter(f, BLOCK_SIZE):
            md5_obj.update(block)

    hash_code = md5_obj.hexdigest()
    md5 = str(hash_code).lower()

    return md5
```

其中的`update`方法传入arg对象来更新hash的对象。必须注意的是，该方法只接受byte类型，否则会报错。
同时要注意，重复调用`update(arg)`方法，是会将传入的arg参数进行拼接，而不是覆盖。

<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易



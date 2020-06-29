我们在对接一些三方服务的时候可能需要我们传输一些文件并且将该文件的CRC32值一并传输。

CRC本身是“冗余校验码”的意思，CRC32则表示会产生一个32bit（8位十六进制数）的校验值。
由于CRC32产生校验值时源数据块的每一个bit（位）都参与了计算，所以数据块中即使只有一位发生了变化，也会得到不同的CRC32值

```python
import binascii


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


def file_crc32(file_path):
    """计算文件的crc32检验码:

    Args:
        filePath: 待计算校验码的文件路径

    Returns:
        文件内容的crc32校验码。
    """
    crc = 0
    with open(file_path, 'rb') as f:
        for block in file_iter(f, BLOCK_SIZE):
            crc = binascii.crc32(block, crc) & 0xFFFFFFFF
    return crc
```

上面返回的为十进制数字，如果想转为十六进制数字需要对输出的数字执行下`"%08X" % crc`操作。


其中的`file_iter`我们使用生成器方式读取文件 即使是大文件也会更加高效

> 除了`binascii`模块 `zlib`模块也有 `crc32`函数


<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易


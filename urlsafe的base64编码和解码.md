有时我们在对接第三方的时候，三方会要求我们传输的某些参数为urlsafe的base64编码，下面我们写两个编码和解码的方法

```python
import base64


def urlsafe_base64_encode(data):

    ret = base64.urlsafe_b64encode(utf8(data))

    return string(ret)
```
> urlsafe的base64编码 传入的参数一般为字符串  返回的结果我们处理成了字符串

```python
import base64


def urlsafe_base64_decode(data):

    ret = base64.urlsafe_b64decode(utf8(data))

    return string(ret)

```
> urlsafe的base64解码 传入的参数一般为字符串  返回的结果我们处理成了字符串


其中的`utf8`和`string`函数为我们在[字符串和字节类型之间相互转换](https://github.com/lufeisan/PythonUtils/blob/develop/%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%92%8C%E5%AD%97%E8%8A%82%E7%B1%BB%E5%9E%8B%E4%B9%8B%E9%97%B4%E7%9B%B8%E4%BA%92%E8%BD%AC%E6%8D%A2.md)  中编写的函数。
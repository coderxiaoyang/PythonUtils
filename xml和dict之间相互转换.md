有时候我们对接一些三方的时候 三方返回或者请求三方的是XML格式（例如腾讯微信支付），但是我们还是习惯于操作dict
我们写两个函数 方便xml和dict数据之间随时切换

```python
from xml.etree import ElementTree


def xml_to_dict(xml_data):

    xml_dict = {}
    root = ElementTree.fromstring(xml_data)
    for child in root:
        xml_dict[child.tag] = child.text
    return xml_dict
```

> 将xml格式数据转为dict类型数据


```python
def trans_dict_to_xml(data):

    xml = []
    for k, v in data.items():
        xml.append(f'<{k}>{v}</{k}>')

    inner_xml = ''.join(xml)

    return f'<xml>{inner_xml}</xml>'
```

<div  style="text-align: center;">    
<img src="https://s1.ax1x.com/2020/06/25/NwjAbj.jpg" alt="求微信赞赏" border="0"  width="230" height="230" />
<img src="https://s1.ax1x.com/2020/06/25/NwjvyF.jpg" alt="求支付宝赞赏" border="0"  width="230" height="230"/>
<img src="https://s1.ax1x.com/2020/06/25/Nwv8l8.jpg" alt="加微信好友" border="0" width="230" height="230"/>
</div>

> 如果您觉得此文对您有帮助 可以选择微信或支付宝支持我一下或者加我微信好友 一起搞py交易

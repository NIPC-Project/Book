# Json+Base64

当需要在json中加入二进制数据时，可以使用Base64编码，相当于对二进制数据做了一个到纯ascii字符串的映射。

[Base64维基百科](https://en.wikipedia.org/wiki/Base64)

[Python使用方法](https://docs.python.org/3/library/base64.html)

```py
import base64
encoded = base64.b64encode(b'data to be encoded') # b'ZGF0YSB0byBiZSBlbmNvZGVk'
data = base64.b64decode(encoded) # b'data to be encoded'
```

---
layout: post
title: "python笔记"
date: 2021-05-03
tags: ["python"]
toc: true
author: oneman233
excerpt: "一些特别的语法"
---

# f-string

python的一种格式化字符串，可以方便地替换字符串某一部分的内容，用法如下：

```py
a = "ABC"
print(f"output:{a}")
print(f"output:{a.lower()}")
```

运行结果如下：

```
output:ABC
output:abc
```

大括号中的内容被自动替换成了对应的变量值，甚至是函数的返回值，详细可以参考[官方文档](https://docs.python.org/zh-cn/3/library/string.html#format-string-syntax)

# 类的写法

基本写法如下：

```py
class A:
    def __init__(self,x):
        self.x=x
    def fun(self):
        return self.x
```

**注意：**

1. `__init__()`函数是构造函数
2. 每一个函数的第一个参数都是`self`，否则会报错：`takes 0 positional arguments but 1 was given`
3. 成员变量不用定义，直接使用`self.x`来赋值和调用即可

# 调用函数和类

假设在`A.py`中有以下函数：

```py
def fun(a):
    return a
```

调用函数：

```py
import A
A.fun(a)
```

或者写成：

```py
from A import *
fun(a)
```

假设在`A.py`中有以下类定义：

```py
class A:
    def __init__(self,x):
        self.x=x
    def fun(self):
        return self.x
```

调用类：

```py
import A
a=A.A(1)
```

```py
from A import *
a=A(1)
a.fun()
```

# 长度

1. `len(list)`：返回列表长度
2. `len(s)`：返回字符串长度

# range()用法

函数定义：

`range(start, stop, step)`

代表着遍历区间`[start, stop)`，步长为`step`

默认步长为$1$，默认起点为$0$

# 读写JSON

把python对象打包成json：

```py
import json

data = {
    'name' : 'ACME',
    'shares' : 100,
    'price' : 542.23
}

json_str = json.dumps(data)
```

把json拆开成dist对象：

```py
data = json.loads(json_str)
```

**注意：**python的dist对象跟c++的map差不多，必须用`dist["key"]`来访问`val`

[参考资料](https://python3-cookbook.readthedocs.io/zh_CN/latest/c06/p02_read-write_json_data.html)

# 随机数生成

```py
random.randint(a,b)
```

生成的随机数范围为$[a,b]$

# exec用法

`exec()`函数可以用于动态执行脚本，只需要将需要执行的Python脚本以字符串形式传入即可，就像下面这样：

```py
exec('a=5')
```

但是有个问题，执行了上面这条语句之后尝试获取变量时会报错：`Variable a  is not defined`，正确的调用方法应该像下面这样：

```py
exec('a=5')
print(locals()['a'])
```

# Python时间函数

1. `time.time()`：返回自纪元以来的秒数作为浮点数
2. `time.perf_counter()`：返回性能计数器的值
3. `time.process_time()`：返回当前进程的系统和用户CPU的时间总和值

测量程序的运行时间可以在程序运行前后调用时间函数，用两次结果做差来获得

# 一个字符串错误

报错：`not all arguments converted during string formatting`

两种可能：

1. 字符串格式化的时候参数不完整
2. 拿着一堆字符串做加减乘除等等数学运算

# 字符串和数字以及数字间的互化

数字化字符串很简单，用`str()`函数即可

`int()`函数则可以用于将字符串转化为整形，但是默认只能转化成十进制数，比如下面这样：

```py
int('1010')
>>> 1010
```

如果想指定基数，可以加入一个参数：

```py
int('1010', base = 2)
>>> 10
```

基数不一定局限为10、8、16等等：

```py
int('10', base = 3)
>>> 3
```

此外，想要把整型数字转换为十六进制字符串时可以使用`hex()`函数：

```py
hex(100)
>>> '0x64'
```

# 判断是否为空

采用以下写法：

```py
if x is None:
    # do something
```

# 字典中键是否存在

如果采用`dist[key]`这样的调用方式，当`key`不存在时就会报错：`KeyError`

有几种可用的方法：

1. 调用前判断一下`dist[key]`是否为空
2. 采用`dist.get(key,'not exist')`，这样当`key`为空时就会返回一个字符串，当然也不局限于字符串，可以是其他任何类型数据
3. 可以向类添加`__missing__(self, key)`方法，如下所示：

```py
t = {
    'a': '1',
    'b': '2',
    'c': '3',
}

class Counter(dict):
    def __missing__(self, key):
        return key
c = Counter(t)
print(c['d'])
print(c)
>>> d
>>> {'c': '3', 'a': '1', 'b': '2'}
```

[参考](https://www.polarxiong.com/archives/Python-%E6%93%8D%E4%BD%9Cdict%E6%97%B6%E9%81%BF%E5%85%8D%E5%87%BA%E7%8E%B0KeyError%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E6%B3%95.html)

# 输出不换行

可以使用`print(x, end='')`来自定义输出结尾的符号，例如：

```py
x = [1, 2, 3]
for xx in x:
    print(xx, end = ',')
>>> 1,2,3,
```

# 类型注解

```py
def add(x:int, y:int) -> int:
    return x + y
```

指明了函数的返回值类型，但是这些注解并不提供校验，函数仍然可以返回任意类型

```py
a: int = 123
b: str = 'hello'
l: List[int] = [1, 2, 3]
```

变量也是可以进行类型注解的
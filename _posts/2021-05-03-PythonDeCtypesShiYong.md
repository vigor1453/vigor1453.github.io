---
layout: post
title: "python的ctypes使用"
date: 2021-05-03
tags: ["python"]
author: oneman233
excerpt: "加速python"
---

最近在做轻量级加密算法的实现时遇到了这样一个问题：**如何用python调用C函数**，主要目的是加快加解密的速度。

先来看看C函数的定义：

```c
typedef unsigned char uint8_t;

void present_encrypt(const uint8_t *plain, const uint8_t *key, uint8_t *ans);
void present_decrypt(const uint8_t *cipher, const uint8_t *key, uint8_t *ans)
```

这两个函数没有返回值，直接通过修改`ans`数组来返回加解密结果，并且它们都在C下做好了测试，可以输出正确结果。

第一步，我们在Linux系统中用以下指令把C函数编译成动态库：

```
gcc -fPIC -shared present.c -o present.so
```

其中`present.c`就是我实现好的加解密C函数。

第二步，我们可以在python脚本中这样调用：

```py
from ctypes import *
lib = CDLL('./80-bit PRESENT.so')
a = lib.present_encrypt
```

注意：**要把动态库`present.so`和python脚本放在同一目录下**

第三步，我们需要在python脚本中指定函数的参数：

```py
a.argtypes = [POINTER(c_ubyte),POINTER(c_ubyte),POINTER(c_ubyte)]
```

ctypes的数据类型请参考：[官方文档](https://docs.python.org/2/library/ctypes.html#fundamental-data-types)，这里的`POINTER()`函数用于把对应常量类型转换成指针类型。

注意：**ctypes可以传入指针，直接对传入变量进行修改！**

接下来我们可以定义输入的数组：

```py
aa=[0,0,0,0,0,0,0,0]
bb=[0,0,0,0,0,0,0,0,0,0]
cc=[0,0,0,0,0,0,0,0]
```

但是我们还需要把数组中的数据类型转换成`c_ubyte`，所以使用以下函数：

```py
def gao(x,y,z):
    x=(c_ubyte * 8)(*x)
    y=(c_ubyte * 10)(*y)
    z=(c_ubyte * 8)(*z)
    a(x,y,z)
    return z
'''
    *(x)会将x中的变量逐个解析
    例如：*(1,2,3)意味着按顺序对1、2、3进行相应处理
    在上面是把x中对应的数字转换成c_ubyte类型
'''
```

最后输出结果：

```py
for i in gao(aa,bb,cc):
    print(hex(i))
```

与预期的十六进制输出完全一样，大功告成。
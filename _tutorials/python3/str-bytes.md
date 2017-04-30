---
layout: page
title:  "Python 中 bytes 和 int 的相互转化"
date:   2017-01-01 23:02:00
categories: "Python3 Tutorials"
---

# bytes 和 str 的区别

`bytes`是python中的一个类, 和`str`类似, 都是好用来存放顺序性的东西. `bytes`存放的是字节而`str`存放的是字符.

bytes的字面量写法是
    
    b"Ascii String as bytes"

或者

    b"\x01\xFF"
    
注意 \x01 的写法, 必须要是两个数字, 如果写 \x1 则会出现语法错误.

而字符串的字面量写法是
    
    "字符串" 或者 'This is a string'

# bytes 和 int 的关系

在网络编程时, 输入输出函数通常都使用`bytes`来作为参数或者返回值, 直接带来的不便之处就是, 如果有一个`int`对象, 如何用于这些函数呢? 例如`socket.send(bytes)`方法, 如果我们直接用`int`作为参数, python 会直接报错.

# 从 `int` 到 `bytes` 的转换方法

如何将 int 转换为 bytes 呢? 有很多方法, 如下:

1.  使用 struct.pack

        struct.pack('>I', 2017)
        得到: b'\x00\x00\x07\xe1'

    其中 '>' 表示大端字节序, 'I' 表示转换为 unsigned int 的字节表示, 被称为 [Formatter][formatter api].
    
    如果你需要处理的是一个复杂的网络协议, 你最好还是用 pack/unpack 来做这事, 这样的代码最紧凑.
    
2.  使用 int.to_bytes(length, byteorder). 从 python 3.2 开始, 我们可以使用下面的方法从`int`得到`bytes`

        (1024).to_bytes(2, byteorder='big')
        得到: b'\x04\x00'

    其中的 length, byteorder 都是必要参数. 上面例子中字节序为'big' endian 即 [网络字节序][endian], 它的最高字节在最低地址, 所以上面结果中的 0x04 在第一个地址上.

3.  *Python3.5+* 以上版本可以使用`%替换`方法

        b'\x05%d' % 2

    但是要注意，%d 得到是数字的 ascii 内码，而不是数值本身，即是 50 而不是 2。

# 从`bytes`到`int`的转换方法

1.  使用 struct.unpack(fmt, buff)

    其中 fmt 为 formatter 串. 这种方法对 python2 和 python3 都适用.

2.  使用 int.from_bytes(buff, 'big')


# 参考

* [StackOverflow](http://stackoverflow.com/questions/21017698/converting-int-to-bytes-in-python-3)


[endian]: http://blog.csdn.net/sunshine1314/article/details/2309655
[formatter api]: https://docs.python.org/3/library/struct.html#format-characters
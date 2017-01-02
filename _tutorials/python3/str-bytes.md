---
layout: page
title:  "bytes和int的相互转化"
date:   2017-01-01 23:02:00
categories: "Python3 Tutorials"
---

bytes是python中的一个类,用来存放字节数据.bytes的字面量写法是
    
    b"Ascii String as bytes"

或者
    
    b"\x01\xFF"
    
注意 \x01 的写法, 必须要是两个数字, 如果写 \x1 则会出现语法错误.

在网络编程时经常需要传输这些bytes对象,但是int对象是不能直接用于bytes对象的,需要进行转换.

从 `int` 到 `bytes` 的转换方法有:

1. 使用 struct.pack
2. 使用 int.to_bytes(length, byteorder). From python 3.2 you can do

    (1024).to_bytes(2, byteorder='big')
    
    b'\x04\x00'

其中的 length, byteorder 都是必要参数.网络字节序是'big' endian,即高位最后传输.

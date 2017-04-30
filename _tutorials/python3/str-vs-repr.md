---
layout: page
title:  "Python 中 __str__ 和 __repr__ 的区别"
date:   2017-01-01 23:02:00
categories: "Python3 Tutorials"
---

python中，一个类如果实现了`__str__`方法，那么就能在`print`的时候显示友好的字符串信息。
如果实现了`__repr__`方法，就可以用`eval(repr(object))`方法得到一个相同的对象, 即

    object == eval(repr(obj))
    
一些示例代码

    >>> class D(object):
    ...     def __str__(self):
    ...         return "a __str__"
    ...     def __repr__(self):
    ...         return "a __repr__"
    ...
    >>> dr = D()
    >>> print dr
    a __str__
    >>> dr
    a __repr__
    >>> "%s" % dr
    'a __str__'
    >>> "%r" % dr
    'a __repr__'


# 参考

[python - __str__ 和 __repr__](http://blog.csdn.net/yyt8yyt8/article/details/7030416)
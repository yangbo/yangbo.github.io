---
layout: page
title:  "Caffe Python 接口使用笔记"
date:   2017-03-19 12:40:00
categories: deep-learning caffe
---

# 准备

大家在开始学习 Caffe Python 的时候，最好安装 python2.7 版本，这样能减少很多安装问题，用 python3 尤其是 python3.6 将会遇到很多安装问题，耗费大量精力。

# 开始

Caffe 提供了一个使用方便的 Python 接口，但是没有相关文档，除了 [Caffe C++ classes doc][caffe-classes-doc]，所以我需要在这里记录一些常用的 caffe python 接口文档。

Caffe Python 接口的实现在 _caffe.cpp 代码中，所以需要搞清楚参数的含义，只需要看这个代码即可。

# Caffe module

- caffe.__version__ : 当前caffe版本号

## Caffe utility functions

- `caffe.init_log([level])`

> 设置 [glog][glog] 的log级别。

> glog 默认对log分为4级： INFO,  WARNING,  ERROR,  FATAL. 

> 如果不给 level 参数则默认使用 INFO 级别。

> GLOG_INFO = 0, GLOG_WARNING = 1, GLOG_ERROR = 2, GLOG_FATAL = 3

- `caffe.log(msg)`

> 记录日志, msg 为 string 类型的日志内容

- `caffe.set_mode_cpu()`

> 设置 caffe 运行模式为 cpu

- `caffe.set_mode_gpu()`

> 设置 caffe 运行模式为 gpu

- `caffe.set_random_seed(seed)`

> Sets the random seed of both boost and curand.
> seed is integer.

- `caffe.set_device()`

- `caffe.solver_count()`

- `caffe.set_solver_count()`

- `caffe.solver_rank()`

- `caffe.set_solver_rank()`

- `caffe.set_multiprocess()`

- `caffe.layer_type_list()`

- `caffe.device_query()`

> 查询当前可用的GPU设备，这个是我修改 Caffe 代码添加上的一个小功能，官方代码还没有。

# Net 类

## 废弃的 Net(param_file, pretrained_param_file, phase) 构造函数

需要使用推荐的构造函数，如下：

	Net(param_file, phase, weights='pre-trained-param-file')

# data[...] = image 用法解释

在 python 中，... 操作符叫 sequenceIncludes，numpy 扩展来作为列下标的通配符。

The dots (...) represent as many colons as needed to produce a complete indexing tuple. For example, if x is a rank 5 array (i.e., it has 5 axes), then

	x[1,2,...] is equivalent to x[1,2,:,:,:],
	x[...,3] to x[:,:,:,:,3] and
	x[4,...,5,:] to x[4,:,:,5,:].


# 参考

- [Caffe C++ classes doc][caffe-classes-doc]
- [Python 教程][python2-tutorial]
- [Python2 官方教程][python2-official-tutorial]
- [Python2.7 官方文档](https://docs.python.org/2/)
- [Python2.7 标准库](https://docs.python.org/2/library/index.html)
- [Glog blog](http://www.cnblogs.com/foreveryl/archive/2011/10/14/2212265.html)

# 版权

本文采用[创作共用署名2.5中国大陆版许可证](https://creativecommons.org/licenses/by/2.5/cn/)（Creative Commons Attribution 2.5 China Mainland License）授权。![Creative Commons License](http://i.creativecommons.org/l/by/2.5/cn/88x31.png)


[caffe-classes-doc]: http://caffe.berkeleyvision.org/doxygen/annotated.html
[python2-tutorial]: http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000
[python2-official-tutorial]: https://docs.python.org/2/tutorial/index.html
[glog]: https://github.com/google/glog
[cc-license]: https://creativecommons.org/licenses/
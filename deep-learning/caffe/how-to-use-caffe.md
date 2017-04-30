---
layout: page
title:  "如何使用训练好的 Caffe 模型"
date:   2017-03-19 15:08:00
categories: deep-learning caffe
---

# 如何使用训练好的 Caffe 模型

* 编写 deploy.prototxt 是使用网络时需要的一个文件，输入和训练时的网络不一样。
 	- input layer
 	
为什么分类结果是颠倒的呢？训练中label是0的，在softmax层输出后反而是 index 1 的概率大。

# 如何快速下载 caffe model?

可以使用 axel 多线程下载 model，model的地址在readme.md中。

	axel -n 5 <model url>

# 参考

# 版权

本文采用[创作共用署名2.5中国大陆版许可证](https://creativecommons.org/licenses/by/2.5/cn/)（Creative Commons Attribution 2.5 China Mainland License）授权。![Creative Commons License](http://i.creativecommons.org/l/by/2.5/cn/88x31.png)

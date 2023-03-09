---
layout: post
title: python中需要特别注意的复制和拷贝
---

1） http://songlee24.github.io/2014/08/15/python-FAQ-02/
注意区分原子元素的赋值和非原子元素的赋值

2） https://blog.csdn.net/gobsd/article/details/56485177
关于np.asarray() 和np.array()
np.array() 本质来说是深copy， np.asarray() 是浅copy

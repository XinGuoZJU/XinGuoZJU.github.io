---
layout: post
title: python2和python3编码问题
---

参考　http://www.cnblogs.com/cyhv5/p/9083805.html

在进行.gz文件读取的时候遇到了原文件用python2写的，现在要用python3进行读取．
代码：open(path,'r').read()
但是报错　UnicodeDecodeError: 'utf-8' codec can't decode byte 0x9c in position 1: invalid start byte
所以百度了下，发现是编码问题．尝试进行添加encoding='utf-8'无效，修改为open(path,'rb).read()，正确．因为python3需要解析文件为二进制编码．
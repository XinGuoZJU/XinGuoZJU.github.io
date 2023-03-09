---
layout: post
title: docker:从ubuntu.iso文件创建docker-image
---

原文：
https://www.cnblogs.com/-beyond/p/9259923.html


1) 将ubuntu.iso通过虚拟机安装
2) 进入虚拟机，进行打包操作：
i) cd /
ii) tar -cf system.tar bin dev ...(出了home 和tmp我都打包了)
3) 将system.tar复制到安装了docker的系统内，并执行下面的操作:
cat system.tar | docker import - ubuntu:18.04

至此，我们创建了ubuntu的docker image

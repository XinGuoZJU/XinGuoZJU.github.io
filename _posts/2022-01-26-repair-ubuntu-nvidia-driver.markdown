---
layout: post
title: 修复由于linux内核更新导致的显卡驱动失效
---

由于系统自动升级内核导致显卡驱动实效，直接表现是，ubuntu显示低分辨率，且无法修改分辨率（即用了主板集成显卡显示），nvidia-smi命令无法检测到显卡。

https://www.jianshu.com/p/3c269d3b2bd0

修复较为简单：

查看推荐显卡驱动：

ubuntu-drivers devices

安装驱动：

# sudo apt update
# sudo apt upgrade
# sudo apt autoremove
# sudo apt-get remove --purge nvidia*
# sudo apt-get remove --purge "nvidia*"
# sudo apt install nvidia-driver-470

为了比避免linux内核自动更新，我们关掉自动更新机制

https://github.com/chiwent/blog/issues/1

sudo apt-mark hold linux-image-generic linux-headers-generic

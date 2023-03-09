---
layout: post
title: 使用sshfs挂载远程的Linux文件系统及目录
---

安装：
sudo apt-get install sshfs

挂载：
sshfs guoxin@192.168.3.1:/home/guoxin/  /home/guoxin/gx01

卸载
umount  /home/guoxin/gx01
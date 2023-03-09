---
layout: post
title: 物理接触破解linux服务器
---

https://blog.51cto.com/13401435/2426571?source=dra

### Ubuntu
简单来说因为修改了开机启动项,导致开机停留在ubuntu开机界面开不了机. 
(https://blog.csdn.net/ydyang1126/article/details/77968768 )
于是开机尝试了ubuntu 高级选项,之后切到root的选项,此时仅有只读权限,没有写权限,所以仍然不能修复bug.
按照帖子进行尝试 
mount -o remount,rw /
获得了权限后删除了原来的开机设置修复了系统.

### debain

debain进入root要稍微麻烦点,
破解debian系统：
在开机的时候进入系统选择菜单，按e键，
在ro quiet结尾的那一行，讲ro quiet改为rw single init=/bin/bash

然后可以获得root权限

如果不把ro改为rw的话，需要执行
mount -o remount,rw /
获得读写权限


fdisk -l 查看所有磁盘，新插入的u盘会自动显示盘符，一般是/dev/sda1之类，
需要挂载才能使用。用mount /dev/sda1 /mnt挂载到mnt文件夹

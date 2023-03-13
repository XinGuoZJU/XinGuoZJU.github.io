---
layout: post
title: Connect linux from mac using x11
---

想让linux服务器上执行的可视化命令显示在本地mac端，我们需要借助x11。具体配置如下：


# mac os
安装并打开XQuartz
https://www.xquartz.org/

执行配置命令，允许所有服务器访问本地mac
xhost +


# linux （以centos为例）
连接服务器时用命令 ssh -X / ssh -Y / ssh -L，检查连接时是否有x11相关的错误提示

检查服务器是否开启x11
sudo vim /etc/ssh/sshd_config
# X11Forwarding yes
sudo service sshd restart

安装x11插件和库
sudo yum install -y xorg-x11-server-Xorg xorg-x11-xauth xorg-x11-apps

运行xclock，检查是否成功显示
xclock


# 如果在linux服务器上运行了docker, 那么docker启动时需要用如下命令
docker run -it -e DISPLAY -v $HOME/.Xauthority:/root/.Xauthority --name ${containerName} ${imageName} bash
注意：$HOME/.Xauthority是个文件，如果没有的话说明x11没有正确配置


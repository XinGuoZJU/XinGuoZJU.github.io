---
layout: post
title: mac远程控制ubuntu服务器显示图像
---

有一个更好的工具juypter notebook，请参考我的博客 http://blog.sina.com.cn/s/blog_1496fa80e0102xfws.html


这里使用的是XQuartz for mac（也就是x11），有点不好用，仅供参考

1.

首先需要在linux 服务器端打开 X11

以ubuntu为例

编辑 /etc/ssh/sshd_config 配置文件

配置转发参数为yes 

X11Forwarding yes  
X11DisplayOffset 10  
 

重启ssh 服务

service ssh restart 

2.

2.1 编辑mac端下文件

 

 /private/etc/ssh/ssh_config

 

设置为

 ForwardX11 yes //去掉注释

2.2 安装XQuartz ，

 按照官网下载安装 https://www.xquartz.org/releases/index.html



3. 测试

打开 XQuartz

打开 mac 终端   //普通mac终端而不是xquartz自带的

ssh -X   {用户名}@{远程端ip}； （注意大写的X）



输入gedit查看是否能显示图形界面。
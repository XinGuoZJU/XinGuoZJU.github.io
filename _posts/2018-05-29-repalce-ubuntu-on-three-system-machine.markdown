---
layout: post
title: win10+win7+ubuntu17.04基础上将ubuntu换为ubuntu18.04LTS
---

之前的文章：win10+win7+ubuntu16.04LTS 装机以及gtx970显卡驱动安装经验
http://blog.sina.com.cn/s/blog_1496fa80e0102xfop.html

首先由于ubuntu17.04已经不被支持，很多镜像无法使用，直接升级觉得麻烦于是直接重装ubuntu了，当然前提是不影响windows使用。

1、按照以前的步骤，用UtralISO制作启动u盘，安装ubuntu18.04，注意要重新划定一块区域（300M左右）作为引导区，否则安装会失败。
2、安装后发现Ubuntu已经安装了，但会进入grub rescue模式，尝试手动恢复（一个很好的参考https://blog.csdn.net/jscese/article/details/36865449），但无法找到/boot/grub/normal.mod的正确路径。最后尝试通过上面的启动u盘使用try ubuntu选项，成功进行引导区修复（https://blog.csdn.net/laocaibcc229/article/details/79274412）
sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt-get update  
sudo apt-get install -y boot-repair && boot-repair 
3、修复windows引导 sudo update-grub

CUDA和显卡驱动安装 参考：http://blog.sina.com.cn/s/blog_1496fa80e0102xfop.html

另外总结下ubuntu18.04装机完成后必要软件的安装（要注意很多步骤只适应于18.04,之前版本可能在ubuntu software找不到对应的安装）：（https://blog.csdn.net/qingkong1994/article/details/80372436）
1、chrome
     直接ubuntu software寻找安装(chromium 和 chrome不一样)
     如果上述方法失败，再参考：https://www.cnblogs.com/Michelle-Yang/p/6660331.html
2、安装switchyomega：https://www.switchyomega.com/
3、搜狗输入法
    ubuntu software先寻找fcitx，把三个小企鹅都安装好，然后去搜狗官网下载搜狗ubuntu输入法
sudo dpkg -i sogoupinyin*
后续配置参考：https://blog.csdn.net/lupengCSDN/article/details/80279177
4、sublime text3
      直接ubuntu software寻找安装
5、防止rm误删  http://blog.sina.com.cn/s/blog_1496fa80e0102xjhm.html
6、在.bashrc 添加alias 111="sshpass -p 123456 guoxin@10.76.0.111"，增加快捷登录和密码控制选项 http://blog.sina.com.cn/s/blog_1496fa80e0102xu0t.html

7、另外，需要安装 open ssh：
          sudo apt-get install openssh-server
      才能进行远程ssh访问
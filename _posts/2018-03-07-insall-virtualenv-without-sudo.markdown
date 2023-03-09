---
layout: post
title: ubuntu无sudo权限virtualenv安装tensorflow及virtualenvwrapper介绍
---

# virtualenv
1. pip install virtualenv --user
     显示安装成功后会在~/.local/bin/ 下有 virtualenv
2. 在~/.bashrc 中添加 export PATH="/home/guoxin/.local/bin:$PATH" 
     其中的guoxin是用户名
3. source ~/.bashrc
     至此完成virtualenv的安装

接下来创建新的virtualenv环境（参考 http://blog.csdn.net/u012734441/article/details/55044025）
1. 在工作目录下执行 virtualenv VENV, VENV是虚拟环境的名字，执行完后会自动创建系统默认python版本的virtualenv环境，
    也可以用下面的命令 virtualenv -p /usr/bin/python2.7 VENV
    如果想用python3.5 则是 virtualenv -p /usr/bin/python3.5 VENV
    （如果本机没有安装python3版本是不是就没办法了？）
2. 运行 source VENV/bin/activate 进入虚拟环境，此时执行pip后续操作均是在虚拟环境下，不会污染全局
3. 运行 deactivate退出当前环境

安装tensorflow
pip install tensorflow-gpu (可指定版本pip install tensorflow-gpu==1.4.1)，下载较慢请使用proxychains
    参考tensorflow官网
    https://www.tensorflow.org/install/install_linux#InstallingVirtualenv

# virtualenvwrapper
　　鉴于virtualenv不便于对虚拟环境集中管理，所以推荐直接使用virtualenvwrapper。 virtualenvwrapper提供了一系列命令使得和虚拟环境工作变得便利。它把你所有的虚拟环境都放在一个地方。

1、安装virtualenvwrapper(确保virtualenv已安装)
pip install virtualenvwrapper
pip install virtualenvwrapper-win　　#Windows使用该命令

2、安装完成后，在~/.bashrc写入以下内容
export WORKON_HOME=~/Envs
source /usr/local/bin/virtualenvwrapper.sh　　
　　 第一行：virtualenvwrapper存放虚拟环境目录
　　 第二行：virtrualenvwrapper会安装到python的bin目录下，所以该路径是python安装目录下bin/virtualenvwrapper.sh
source ~/.bashrc　　　　#读入配置文件，立即生效
　
3、virtualenvwrapper基本使用
1）创建虚拟环境　mkvirtualenv
mkvirtualenv venv　　　
　　这样会在WORKON_HOME变量指定的目录下新建名为venv的虚拟环境。

　　若想指定python版本，可通过"--python"指定python解释器
mkvirtualenv --python=/usr/local/python3.5.3/bin/python venv
2）基本命令 　
　　 i）查看当前的虚拟环境目录
[root@localhost ~]# workon
py2
py3
　　 ii）切换到虚拟环境
[root@localhost ~]# workon py3
(py3) [root@localhost ~]# 
　　 iii）退出虚拟环境
(py3) [root@localhost ~]# deactivate
[root@localhost ~]# 
　　 iv）删除虚拟环境
rmvirtualenv venv

---
layout: post
title: Install opencv open3d in docker
---


基础镜像：
    docker pull pytorch/pytorch:1.6.0-cuda10.1-cudnn7-devel
系统版本：ubuntu 18.04.3 LTS


1. ## 换源：
1.1 # apt
    # https://zhuanlan.zhihu.com/p/142014944
    # https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11iEnHXx
    记得用下面三个命令更新下源：
        更新源
        sudo apt-get update
        如出现依赖问题，解决方式如下：
        sudo apt-get -f install
        更新软件：
        sudo apt-get upgrade

1.2 # pip
    # https://www.cnblogs.com/vipstone/p/9038023.html


2. ## 安装opencv
    # https://blog.csdn.net/hjl240/article/details/51520003 
    # https://blog.csdn.net/wandugu/article/details/117335867
    # 安装教程：https://www.jianshu.com/p/59608e83becb

2.1 安装依赖
    apt install build-essential pkg-config libgtk2.0-dev libavcodec-dev libavformat-dev libjpeg-dev libswscale-dev libtiff5-dev 
    apt install cmake
    apt install libgl1-mesa-glx

2.2 下载
    git clone https://github.com/opencv/opencv.git  # 失败的话就直接自己下载zip文件
    cd opencv
    git checkout remotes/origin/3.4
    cd ..
    git clone https://github.com/opencv/opencv_contrib/
    cd opencv_contrib
    git checkout remotes/origin/3.4
    cd ../opencv

2.3 编译
    mkdir release
    cd release
    cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local OPENCV_ENABLE_NONFREE=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules -D WITH_GTK=ON -D OPENCV_GENERATE_PKGCONFIG=YES WITH_QT=ON ..
    make -j256
    make install

2.4 安装cv2
    pip install opencv-python


2. ## 安装open3d
    pip install open3d

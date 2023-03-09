---
layout: post
title: Install opencv open3d in docker
---

## 换源：
# apt
# https://zhuanlan.zhihu.com/p/142014944
# https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11iEnHXx

# pip
# https://www.cnblogs.com/vipstone/p/9038023.html

## 安装
# 下载： https://blog.csdn.net/hjl240/article/details/51520003
# https://blog.csdn.net/wandugu/article/details/117335867
# 安装教程：https://www.jianshu.com/p/59608e83becb

apt install build-essential pkg-config libgtk2.0-dev libavcodec-dev libavformat-dev libjpeg-dev libswscale-dev libtiff5-dev


git clone https://github.com/opencv/opencv.git
cd opencv
git checkout remotes/origin/3.4
cd ..
git clone https://github.com/opencv/opencv_contrib/
cd opencv_contrib
git checkout remotes/origin/3.4
cd ../opencv

mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local OPENCV_ENABLE_NONFREE=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules -D WITH_GTK=ON -D OPENCV_GENERATE_PKGCONFIG=YES ..
make -j256
make install

pip install opencv-python


### open3d
apt install libgl1-mesa-glx
pip install -i open3d


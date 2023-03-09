---
layout: post
title: 安装docker和nvidia-docker
---

参加考了 https://www.tensorflow.org/install/docker

### 一、按照官网教程安装docker：
https://docs.docker.com/get-docker/ 

### 二、安装nvidia-docker
注意需要先安装驱动：参考我们之前的教程：https://xinguozju.github.io/2020/08/28/how-to-selecet-cuda-cudnn-pytorch-version/

https://github.com/NVIDIA/nvidia-docker

### 三、Docker 创建docker用户组，应用用户加入docker组
1. 创建docker用户组
 sudo groupadd docker1
2. 应用用户加入docker用户组
 sudo usermod -aG docker ${USER}1
3. 重启docker服务
 sudo systemctl restart docker

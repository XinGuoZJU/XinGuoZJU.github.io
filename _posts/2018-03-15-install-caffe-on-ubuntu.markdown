---
layout: post
title: Ubuntu16.04安装caffe
---

首先当然是参考官网：
http://caffe.berkeleyvision.org/install_apt.html
装好依赖（按照经验一开始肯定还有很多库是缺失的，可以一边编译一边查找）

接着安装caffe
http://caffe.berkeleyvision.org/installation.html
在修改Makefile.config的时候：
USE_CUDNN := 1
CUDA_DIR := /usr/local/cuda-8.0 (我安装了cuda-8.0版本)

CUDA_ARCH := -gencode arch=compute_30,code=sm_30 \ 
-gencode arch=compute_35,code=sm_35 \
-gencode arch=compute_50,code=sm_50 \
-gencode arch=compute_52,code=sm_52 \
-gencode arch=compute_60,code=sm_60 \
-gencode arch=compute_61,code=sm_61 \
-gencode arch=compute_61,code=compute_61

BLAS := atlas 

PYTHON_INCLUDE := /usr/include/python2.7 \ 
/usr/lib/python2.7/dist-packages/numpy/core/include  (这里用系统的python)

PYTHON_LIB := /usr/lib 

INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include \ 
/usr/local/cuda-8.0/include \
/home/guoxin/.local/cuda/include \   (这是我安装cudnn的位置)
/usr/include/hdf5/serial　　　　　　(这是后面编译时发现.h文件的位置)

LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib \
/usr/lib /usr/local/cuda-8.0/lib64 \
/home/guoxin/.local/cuda/lib64 \　　(这是我安装cudnn的位置)
/usr/lib/x86_64-linux-gnu/hdf5/serial/　　(这是后面编译时发现.so文件的位置)

BUILD_DIR := build
DISTRIBUTE_DIR := distribute 
TEST_GPUID := 0

Q ?= @ 

如果要用pycaffe可以考虑将WITH_PYTHON_LAYER := 1注释去掉（允许使用python写的层）
如果用python3，需要将

PYTHON_LIBRARIES:= boost_python3 python3.5m

这一行取消注释，并修改成对应的版本（需要安装libboost_python3:
参考https://github.com/openai/roboschool/issues/122， conda install -c conda-forge boost，boost是boost-python的超集）

接着（-j16多线程）

make all
make test
make runtest
成功后可以继续编译pycaffe:(官网不知道为何没有了这一部分了)
1. 由于本机安装了virtualenv，因此我直接在自己创建的py2环境下安装了依赖：(这一步也可以在其他步骤完成后再安装)
　　进入caffe/python  执行 pip install -r requirements.txt
2. 编译pycaffe 执行 make pycaffe -j16
3. 在.bashrc里添加相应的路径 export PYTHONPATH="/path/to/caffe/python:$PYTHONPATH"
4. 在python里import caffe 测试是否安装成功

有一个类似的安装贴，可以参考其修复bug
https://blog.csdn.net/isuccess88/article/details/70165726



Ubuntu16.04安装caffe中遇到的问题总结:

发现了一个很好的汇总 https://github.com/BVLC/caffe/wiki/Commonly-encountered-build-issues

解决办法是依据出现错误的顺序而给出的，为了方便，可以直接先执行所有解决办法后再安装caffe。

1. ./include/caffe/common.hpp:5:27: fatal error: gflags/gflags.h: No such file or directory

解决办法：sudo apt-get install libgflags-dev

2. ./include/caffe/util/mkl_alternate.hpp:14:19: fatal error: cblas.h: No such file or directory

解决办法：sudo apt-get install libblas-dev

3. ./include/caffe/util/hdf5.hpp:6:18: fatal error: hdf5.h: No such file or directory

解决办法：在Makefile.config找到以下行并添加蓝色部分

INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial 

LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial
4. ./include/caffe/util/db_lmdb.hpp:8:18: fatal error: lmdb.h: No such file or directory

解决办法：sudo apt install liblmdb-dev

5. /usr/bin/ld: cannot find -lcblas
    /usr/bin/ld: cannot find -latlas

解决办法：sudo apt install libatlas-base-dev

版权声明：本文作者Pekary：http://blog.csdn.net/u012576214，转载请注明出处。 http://blog.csdn.net/u012576214/article/details/68947893
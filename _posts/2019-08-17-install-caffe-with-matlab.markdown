---
layout: post
title: Caffe-with-matlab安装
---

下面这篇链接是最快速的安装教程

https://blog.csdn.net/qq_33003797/article/details/79334923

我尝试了很多版本按照上面的教程下载相应版本的ubuntu16.04和matlab2014b能够最快的安装好matcaffe



1． 首先一定要检查 matlab版本 所对应的gcc和g++，不然虽然前面的make all 和 make test 都能过，但会卡在make mattes这里。https://blog.csdn.net/jiandanjinxin/article/details/69943853

Matlab所需的gcc和g++版本远低于ubuntu库里自带的，因此需要手动选择版本安装，

我用的matlab 2014b，所以用gcc/g++4.7.x版本

这个一定要首先解决，因为其他的库可能会依赖于gcc 和g++版本，如果不安装matlab版本的caffe那就不需要考虑这个问题了。



2. 安装 gcc/g++4.7 （事实证明不需要4.7而应该用ubuntu16.04自带的gcc/g++5.0，这是因为ubuntu16.04上的库依赖于gcc/g++5.0而，一开始先安装g++-4.7，装其他依赖的时候ubuntu自动会把g++-5.0安装进去并设置软链接，make all 和make test的时候要用gcc-5.0否则会出现protobuf的错误，matlab2014b支持gcc-4.7，但gcc-5.0也可以兼容）

sudo gedit /etc/apt/sources.list （如果没有的话），添加

deb http://dk.archive.ubuntu.com/ubuntu/ xenial main

deb http://dk.archive.ubuntu.com/ubuntu/ xenial universe

执行 sudo apt update && sudo apt install g++-4.7



在/usr/bin 下添加 gcc和g++的软连接 ln -s g++-4.7 g++  ln -s gcc-4.7 gcc



3.   https://caffe.berkeleyvision.org/install_apt.html

按照官网安装依赖：

sudo apt install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler

sudo apt install --no-install-recommends libboost-all-dev



4. 查看opencv安装版本pkg-config --modversion opencv

安装python3  sudo apt install python3 （事实上我用了自带的python2，opencv用了2版本）

ln -s python3 python

因为caffe里面还有 /usr/local/lib/python3.6/dist-packages/numpy/core/include

所以安装numpy 

sudo apt-get install python3-numpy



5.其他依赖的安装：https://github.com/BVLC/caffe/wiki/Commonly-encountered-build-issues



6. cblas.h no such file or directoty

 sudo apt-get install libopenblas-dev



7.上面都搞定测试下

make all

make test

make runtest （make runtest之前需要将lib include等写入环境变量）

都没有问题可以开始安装matlab了

参看之前的帖子 http://blog.sina.com.cn/s/blog_1496fa80e0102z00w.html

1) make all 遇到问题可能需要在.bashrc中 export LC_ALL=C

2) make matcaffe遇到https://github.com/BVLC/caffe/issues/6153

在Makefile 中添加  CXXFLAGS ++ -std=c++11

之后

make matcaffe

make mattest

按照上述应该就能安装完成



我在使用ubuntu18.04安装时，遇到的其他问题（最后并没有成功）

8. 第7步中 make all可能会出现protobuf的问题，建议安装protobuf-2.5.0:

https://blog.csdn.net/qq_16775293/article/details/81119375



9. 因为gcc和g++版本降低了，所以apt install的一些库会和当前所需不匹配

1）第7步中 make all可能会出现protobuf的问题，建议安装protobuf-2.5.0:

https://blog.csdn.net/qq_16775293/article/details/81119375

遇到./autogen.sh: 10: ./autogen.sh: autoreconf: not found

./autogen.sh: 10: ./autogen.sh: autoreconf: not found

遇到bzip2 is not a bzip2 file，

curl -L https://github.com/google/googletest/archive/release-1.5.0.tar.gz

解压后放到protobuf-2.5.0文件夹下并命名为gtest

2）安装glog gflags

https://blog.csdn.net/csm201314/article/details/75094527

安装gflags请不要参考上面的链接

https://blog.csdn.net/cumt08113684/article/details/51861678

注意要使用2.2.1版本并且

wget https://github.com/schuhschuh/gflags/archive/master.zip 

unzip master.zip 

cd gflags-master 

mkdir build && cd build 

export CXXFLAGS=”-fPIC” && cmake .. && make VERBOSE=1 

make && make install

这样可以避免关于后续caffe中‘-fPIC’的bug

3）安装imdb (参考caffe官网)


# lmdb
git clone https://github.com/LMDB/lmdb
cd lmdb/libraries/liblmdb
make && make install
4）安装leveldb

https://github.com/google/leveldb/releases/tag/v1.20

下载1.20版本，按照readme编译，然后把include中的leveldb文件夹cp到/usr/local/include

把.so 文件 cp -p （保持软链接）到/usr/local/lib中




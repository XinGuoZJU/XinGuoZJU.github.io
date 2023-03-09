---
layout: post
title: ubuntu安装opencv并安装常用python库
---

安装opencv 建议选择2.x版本而不是3.x版本
解压后进入文件夹
cd opencv-2.4.13.3/      //这里选择opencv2.4.13.3
mkdir release                //这些文件是临时的，安装完可以删除
cd release/
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..     
(如果出现关于cuda的问题的话，可以把CMakeLists.txt中的WITH_CUDA部分选为NO)
make -j4
sudo make install

python 机器学习的开发环境搭建（numpy, scipy, matplotlib）

sudo apt-get install python-opencv  //python cv2
sudo apt-get install python-scipy
sudo apt-get install python-matplotlib
sudo apt-get install python-sklearn

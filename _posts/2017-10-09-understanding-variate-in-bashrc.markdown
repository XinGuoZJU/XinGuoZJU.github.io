---
layout: post
title: ubuntu.bashrc文件中LD_LIBRARY_PATHPYTHONPATHPATH等变量的含义
---

LD_LIBRARY_PATH  动态连接库路径，一般.so文件什么的所在文件夹
PYTHONPATH  python需要import的库或自定义文件路径
PATH  系统命令所在路径，比如nvcc命令，sh命令等

用法示例：
export PATH=/usr/local/cuda/bin:$PATH
注意用":"隔开。

LD_LIBRARY_PATH等路径不能递归搜索，所以要指定到所需文件所在目录。
PYTHONPATH 可以递归寻找

source .bashrc 命令是让.bashrc再执行一遍，因此，$PATH变量会在原来基础上再增加。将与$PATH相关语句删除后，关闭终端后，$PATH会恢复默认值。

附一个很好的关于.bashrc等文件等说明：http://blog.csdn.net/wei_qifan/article/details/50528159
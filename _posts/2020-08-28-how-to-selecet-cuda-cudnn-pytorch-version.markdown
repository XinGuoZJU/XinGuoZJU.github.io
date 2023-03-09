---
layout: post
title: 如何根据GPU选择cuda cudann和pytorch、tensorflow版本
---

原帖子 https://zhuanlan.zhihu.com/p/106133822
https://www.jianshu.com/p/7546d3abbaef


首先显卡驱动和CUDA是不同的东西，CUDA是toolkit

1) 根据自己的显卡，查找可以安装的驱动：
	
	https://www.nvidia.com/Download/index.aspx?lang=en-us

2) 安装驱动教程：https://zhuanlan.zhihu.com/p/59618999
	建议用 sudo ubuntu-drivers autoinstall 命令

3) 执行nvidia-smi，查看显卡驱动版本号

4) 根据版本号找对应版本的CUDA: 
	
	https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html

5）下载CUDA .run文件本地安装 
	
	https://developer.nvidia.com/cuda-toolkit-archive

6）下载cudnn，解压放在CUDA对应目录 
	
	https://developer.nvidia.com/rdp/cudnn-download
	使用 cp -a 或者cp -d命令可以同时保留软连接

7） 安装

	pytorch根据cuda版本安装，https://pytorch.org/

	tensorflow各个版本需要的CUDA版本以及Cudnn的对应关系

		https://www.tensorflow.org/install/source#common_installation_problems

		https://www.tensorflow.org/install/source_windows

	tensorflow安装：1) 官网: https://tensorflow.google.cn/install

					2) conda: https://anaconda.org/

8) 其他

	显卡计算力 Compute Capability： https://developer.nvidia.com/cuda-gpus
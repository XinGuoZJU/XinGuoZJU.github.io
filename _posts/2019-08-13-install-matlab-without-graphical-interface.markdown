---
layout: post
title: 无图形界面安装matlab
---

2017b
https://blog.csdn.net/Xiao_Song_PKU/article/details/82700228
2014b
https://blog.csdn.net/wangpengfei163/article/details/47311041
对于2014b的安装补充，
安装完毕后需要在安装目录下mkdir licenses，然后把license.lic复制到该文件夹。


unzip遇到
file #1:  bad zipfile offset (local header sig):  4

的问题，这是因为 有 R2017b_glnxa64.z01  R2017b_glnxa64.z02  R2017b_glnxa64.zip 
多个文件，且文件太大。
尝试了如下方法均失败：1） zip -F R2017b_glnxa64.zip --out file-large.zip
先压成一个文件，再解压：
2) 或者 cat archive.z01 archive.z02 archive.zip > complete.zip
Note: The order of concatenating should be as follows: all numbered parts of archive first, and then the .zip file in the end.(注意顺序)
最后在windows下用https://support.firmex.com/hc/en-us/articles/204579673-Downloads-with-multiple-parts-z01-and-z02-files-
解压，猜测原本作者就是在windows下压缩的。。。

matlab -nodesktop -nosplash 

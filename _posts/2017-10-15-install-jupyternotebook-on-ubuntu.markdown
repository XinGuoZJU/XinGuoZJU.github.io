---
layout: post
title: ubuntu17.04安装jupyter notebook
---

先说一下jupyter notebook是什么。当我们远程使用服务器的时候，不能直接使用显示器查看服务器里面的文件，尤其是图片的查看十分不方便，因此jupyter notebook的出现让我们将远程服务器中的文件夹和github网站端一样可视化。
jupyter notebook是ipython下的一个小工具，虽然ipython很反人类，但这个小工具却是十分有用。jupyter是新版本的名字，以前版本叫做 notebook。

1、安装ipython
pip install ipython 
     如果这里安装成功但运行不成功的话就全局搜索一下ipython，把路径添加到.bashrc里面。
2、安装jupyter notebook，通常只需要安装jupyter，一些奇怪的机器需要两个都安装。
pip install jupyter 
pip install notebook
     同样，如果安装成功但运行不成功的话就全局搜索下，添加到.bashrc里面。
给个示例：
export PYTHONPATH='.local/lib/python2.7/site-packages':$PYTHONPATH
export PATH=$PATH:~/.local/bin

3、生成配置文件
jupyter notebook --generate-config
4、运行
ipython notebook --port 10086 --ip 0.0.0.0 或者运行 jupyter notebook --port 10086 --ip 0.0.0.0 
(--ip 0.0.0.0是可以使局域网都能访问到)

到这里，基本就可以按照命令行里给出的网址在浏览器里打开使用了。后续密码端口设置什么的，请参考http://blog.csdn.net/a819825294/article/details/55657496

由于我之前mac上安装了XQuartz，因此使用jupyter notebook会自动唤醒XQuartz导致浏览器窗口在服务器打开，而mac本地无法打开，参考我之前的安装贴http://blog.sina.com.cn/s/blog_1496fa80e0102xft6.html 
按照逆过程把XQuartz关掉即可。

另外给出一个小经验，pip安装和apt-get安装具有独立的体系，在一台电脑上安装的话最好只用一个命令，避免后期的版本混乱。

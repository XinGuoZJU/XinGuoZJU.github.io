---
layout: post
title: 源码安装tmux
---

### 零：准备工作：
在~/.local 下创建 bin local share三个文件夹
在.bashrc 里面添加
```
export PATH="~/.local/bin:$PATH"
export LD_LIBRARY_PATH="~/.local/lib:$LD_LIBRARY_PATH"
export C_INCLUDE_PATH="~/.local/include:$C_INCLUDE_PATH"
```

### 一、安装openssl
https://blog.csdn.net/turnaroundfor/article/details/86076214

1）下载并解压openssl-1.1.0.h.tar.gz    https://www.openssl.org/source/
2）进入文件夹，执行
```
./config --prefix= ~/.local/.local/openssl --openssldir=~/.local/.local/ssl shared
```
3) make & make install
4) ln -s ~/.local/local/openssl/bin/openssl ~/.local/bin/openssl

在.bashrc里面添加
```
export PKG_CONFIG_PATH="~/.local/local/openssl/lib/pkgconfig:$PKG_CONFIG_PATH"
```

### 二、安装tmux依赖：
https://github.com/tmux/tmux/wiki/Installing#building-dependencies

1）For libevent:
```
tar -zxf libevent-*.tar.gz
cd libevent-*/
./configure --prefix=~/.local --enable-shared
make && make install
```

2）For ncurses:
```
tar -zxf ncurses-*.tar.gz
cd ncurses-*/
./configure --prefix=～/.local --with-shared --with-termlib --enable-pc-files --with-pkg-config-libdir=～/.local/lib/pkgconfig
make && make install
```

### 三、安装tmux
1）git clone https://github.com/tmux/tmux.git
2）sh autogen.sh
3）PKG_CONFIG_PATH=～/.local/lib/pkgconfig ./configure --prefix=~/.local (即使前面因为已经在.bashrc中配置过了，也要加)
4）将～/.local/include/ ncurses中的文件移到～/.local/include中
5）make && make install

Note: If ncurses and libevent were installed in different directories rather than all together in ~/.local, both their lib/pkgconfig directories will need to be in PKG_CONFIG_PATH, for example:
```
PKG_CONFIG_PATH=/opt/libevent/lib/pkgconfig:/opt/ncurses/lib/pkgconfig ./configure --prefix=~/.local
```


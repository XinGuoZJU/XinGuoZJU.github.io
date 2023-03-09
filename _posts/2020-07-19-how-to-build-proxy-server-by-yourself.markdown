---
layout: post
title: 如何搭建代理服务器
---

涉及几个概念：１）代理服务器(10.76.5.59:7070)　２）桥接服务器(10.76.0.131:8080)　３）客户端(10.76.0.1)

### 1. 代理服务器: 提供数据服务的电脑

　　１）安装shadowsocks
		
        apt-get install python-pip
        pip install shadowsocks

	 2) 编辑　/etc/shadowsocks.json 按照如下设置（端口和密码自行修改）
        {
  			"server":"0.0.0.0",
  			"server_port":7070,　　　　#　代理服务器输出端口
  			"local_address": "127.0.0.1",
  			"local_port":1080,
  			"password":"my_password",
  			"timeout":300,
  			"method":"aes-256-cfb"
		}
	3）使用ssserver开启代理服务
        sudo ssserver ‐c /etc/shadowsocks.json
        或者后台运行
        sudo ssserver ‐c /etc/shadowsocks.json ‐d start
        后台关闭
        sudo ssserver -d stop
### 2. 桥接服务器：提供中间转接的电脑
	
    1) 安装shadowsocks
	
    2) 编辑　/etc/shadowsocks.json 按照如下设置（端口和密码自行修改）
        {
  			"server":".10.76.5.59",
  			"server_port":7070,　　　　  # 代理服务器输出端口
  			"local_address": "0.0.0.0",  # 本地ip 注意：不要使用127.0.0.1或192.168.1.100等服务器ip，否则影响局域网内电脑服务连接代理服务器
  			"local_port":8080,
  			"password":"my_password",
  			"timeout":300,
  			"method":"aes-256-cfb"
		}
	3) 使用sslocal开启服务
        sslocal  -c /etc/shadowsocks.json -d start

参考: [Linux搭建Shadowsocks服务器](https://www.chenshaowen.com/blog/shadowsocks-server-linux.html)
	[ubuntu 服务器搭建 Shadowsocks 服务](https://my.oschina.net/noofi/blog/3123980)

### 3. 使用privoxy将socks5转成http/https代理 (https://www.cnblogs.com/hongdada/p/10787924.html)
　　shadowsocks 是 socks5 代理，在 shell 里执行的命令，发起的网络请求不支持 socks5 代理，只支持 http/https 代理。
	
    为了在 shell 中使用 shadowsocks 代理，需要安装 privoxy 将全局 http 代理转发到 socks5 代理。

	1) 安装privoxy
	
    2）配置
        vim /etc/privoxy/config
		在socks5t有关的项目里添加
        forward-socks5t / 10.76.0.131:8080 . 
        (注意有个点"."，这里假设不同于桥接服务器的本地机器上。如果是在桥接服务器上，这里改为127.0.0.1:8080)

		在listen-address中添加
        listen-address  127.0.0.1:8118 (如果想让非本机设备手机能用的话，需要改为0.0.0.0:8118)
		# listen-address  [::1]:8118
    3) 在~/.bashrc中添加
	export http_proxy=http://127.0.0.1:8118
	export https_proxy=http://127.0.0.1:8118
	export ftp_proxy=http://127.0.0.1:8118
	配置之后，就可以在终端使用代理了，以及在ios手机端可以用http/https代理了(具体在wifi里面添加HTTP Proxy，但不推荐)
     systemctl start privoxy　　# 启动
     systemctl restart privoxy　# 重启
     systemctl status privoxy   # 查看状态
### ４. 客户端：
	１）浏览器端可以下载SwitchyOmega/SwitchySharp等，用桥接服务服务区的10.76.0.131:8080
	
    ２）电脑端可以直接下载shadowsocks或者v2vay，用代理服务器10.76.5.59:7070
	
    ３）终端除了可以和３中说的一样，可以下载proxychains使用桥接服务器的10.76.0.131:8080
        sudo apt install proxychains
		sudo vim /etc/proxychains.conf
        末尾添加
		[ProxyList]
			socks5  10.76.0.131:8080
        使用的时候用proxychains + 相应命令（注意使用sudo命令时，sudo要放在proxychains前面否则代理不会生效）

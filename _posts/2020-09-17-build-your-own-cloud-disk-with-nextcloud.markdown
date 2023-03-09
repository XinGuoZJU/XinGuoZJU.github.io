---
layout: post
title: 用nextcloud搭建自己的云盘
---

### 1. Nextcloud 安装:
```
sudo snap install nextcloud
```
设置端口：
```
sudo snap set nextcloud ports.http=8888
```
访问http://iplocal:8888 创建用户名和密码即可

代码目录位置：/snap/nextcloud/current

这样子我们可以在内网使用nextcloud，包括ios，mac os客户端，但无法在外网使用，于是我们希望做内网穿透。首先我们租用vps

### 2.	租用google cloud vps以获得外网ip地址：ipserver。

https://zhuanlan.zhihu.com/p/60993816

### 3.	内网穿透：用frp做反向代理：

https://zhuanlan.zhihu.com/p/57477087

注意服务器和客户端要下载完全相同的版本：

https://github.com/fatedier/frp/releases

配置和端口举例：
```
# frps.ini
[common]
bind_port = 7000
token = 1234567

# frpc.ini
[common]
server_addr = ipserver
server_port = 7000
token = 1234567

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 221

[https]
type = tcp
local_ip = iplocal
local_port = 8888
remote_port = 6006
```

p.s. 后台运行：利用nohup
```
nohup ./frpc -c frpc.ini &
```
搞好之后就可以通过ipserver:6006访问nextcloud了

### 4.	解决nextcloud 外网代理服务器ip不受信任的问题

https://dusays.com/243/

https://help.nextcloud.com/t/getting-error-as-read-only-for-config-php/70139/2

虽然官方文档上是让修改

/snap/nextcloud/current/htdocs/config/config.php
 ```
'trusted_domains' =>
  array (
   0 => 'localhost',
   1 => 'server1.example.com',
   2 => 'ipserver',
   3 => '[fe80::1:50]',
),
```
但这个文件无法被修改，需要用命令：
查看：
```
sudo nextcloud.occ config:system:get trusted_domains
```
修改
```
sudo nextcloud.occ config:system:set trusted_domains 0 --value=iplocal   # 端口可添加，也可不添加
sudo nextcloud.occ config:system:set trusted_domains 2 --value=ipserver  # 端口可添加，也可不添加
```

### 5. 文件存储路径

/var/snap/nextcloud/common/nextcloud/data/USERNAME/files


### 6. 自定义nextcloud数据保存目录：https://www.bilibili.com/read/cv6156905/

/var/snap/nextcloud/24051/nextcloud/config/config.php
用sudo权限才能看，修改'datadirectory' => '/var/snap/nextcloud/common/nextcloud/data' 
/mnt/nfs/nextcloud/data

sudo cp -r /var/snap/nextcloud/common/nextcloud/data /mnt/nfs/nextcloud/data

chomd -R 773 /mnt/nfs/nextcloud/data

sudo snap restart nextcloud

sudo snap connect nextcloud:removable-media
---
layout: post
title: sshpass进行密码控制
---

安装：
sudo apt-get install sshpass

ssh：
sshpass -p 123456 ssh guoxin@192.168.3.1

sshfs：
sshfs -o ssh_command='sshpass -p 123456 ssh'  guoxin@192.168.3.111:/home/guoxin 111
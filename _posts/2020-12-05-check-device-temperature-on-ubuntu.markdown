---
layout: post
title: ubuntu查看设备温度
---

1. 通用设备：
	安装lm-sensors和hddtemp
		sudo apt install lm-sensors hddtemp
	配置lm-sensors
		sudo sensors-detect -y
	配置好后运行
		sensors 

2. 查看机械硬盘温度：
 hddtemp /dev/sda

3. 查看固态硬盘温度：
	安装nvme-cli
		sudo apt-get install nvme-cli
	查看nvme硬盘列表
		sudo nvme list 	
	查看nvme硬盘详细信息
		sudo nvme smart-log /dev/nvme0n1
		sudo watch -n 1 nvme smart-log /dev/nvme0n1
	查看温度：
		sudo nvme smart-log /dev/nvme0n1 | grep "^temperature"
---
layout: post
title: win10＋win7＋ubuntu16.04LTS三系统安装以及gtx970显卡驱动和cuda安装经验
---

先讲一下这个任务需要注意的地方。首先，通常来说要先装window系统再装ubuntu系统。其次，win10采取了UEFI启动，双系统的话需要在bios里面调整。
讲一下我的经历，就当是日记啦。
暑假买了个xbox one s游戏手柄，发现只有win10对其兼容较好（win7下载驱动按键会错乱），于是在台式机原有ubuntu16.04+win7的基础上安装了win10。安装win10后，win10自动识别win7，但是由于之前是ubuntu引导win7，所以安装win10后会直接把ubuntu的引导区破坏掉，我尝试了一些修复方法都没有修复ubuntu引导区成功（但在2015年刚来实验室的时候貌似成功过），因此干脆把ubuntu重装。


重装ubuntu需要修改bios里面的两个选项为关闭：快速启动（关闭）和安全启动（关闭，我的主板上采用了开启+其他操作系统选项）。安装ubuntu时候要注意磁盘已有的分区，不能把windows分区覆盖掉，我手动选取了swap分区和原先的ubuntu分区格式化（也可以对swap分区不格式化），然后重新划分了swap分区和ntf4主分区（设置根目录’／‘），进行安装（ubuntu安装至少需要swap分区和根目录‘／’分区）。安装完成后，需要在ubuntu终端下进行 sudo update-grub 命令来恢复对win10的引导。至此，就完成了第一阶段的安装，然而问题在于，ubuntu自带的显卡驱动是nouveau，在没有安装gtx 970显卡驱动的时候，主板上插着显卡无法启动ubuntu。所以每次使用ubuntu需要把显卡拆下来，使用windows又要把显卡装上（当然不装独显也可以使用windows，就是画质差）
    公司实习的时候需要远程使用ubuntu，所以显卡驱动还是得装，于是倒霉的事情来了，我安照教程，"sudo gedit /etc/default/grub, 找到这一行，GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"改成GRUB_CMDLINE_LINUX_DEFAULT="quiet splash text" ，在输入命令：sudo update-grub开机后就自动进入tty1了。” 上面操作无效，开机后还是会进入图形界面，所以我又设置“GRUB_TERMINAL=console ”，结果，电脑直接无法进入ubuntu了。其实想想看，上面的操作是没必要的，我们只需开机状态下关闭图形界面安装显卡驱动就好。
    所以不得不ubuntu再重装一遍，然后倒霉的事情又来了，安装ubuntu的时候，总是在最后的阶段报错：“无法将grub-efi-amd64-signed软件包安装到/target/”，尝试了ubuntu17.04和16.04都有问题，最后快放弃的时候突然bug没有了，后来确认了下的确是因为在安装ubuntu的时候系统会提示“监测到其他UEFI的xxx”，这时候需要选择“返回”而不是”确认“，否则ubuntu将会以UEFI的模式安装，会出bug。按装了ubuntu17.04后，接着按照http://m.blog.csdn.net/Good_Day_Day/article/details/74352534 进行显卡驱动下载安装，结果总是出bug说ubuntu的kernel和gtx970的显卡驱动不匹配。一开始以为是17.04版本问题，后来，卸载掉重装16.04后，还是有问题，于是按照http://blog.csdn.net/u010094199/article/details/54380086 进行安装终于成功了！

流程总结：
      系统安装：
             1、先安装win7和win10，再安装ubuntu
             2、安装ubuntu的时候进入bios，设置 快速启动（关闭）和安全启动（关闭，我的主板上采用了开启+其他操作系统选项）
             3、安装阶段当系统提示“监测到其他UEFI的xxx”，这时候需要选择“返回”而不是”确认“，否则ubuntu将会以UEFI的模式安装，会出bug。
             4、安装ubuntu时候要注意磁盘已有的分区，不能把windows分区覆盖掉，我手动选取了swap分区和原先的ubuntu分区格式化（也可以对swap分区不格式化），然后重新划分了swap分区和ntf4主分区（设置根目录’／‘），进行安装（ubuntu安装至少需要swap分区和根目录‘／’分区）。
             5、安装完成后，需要在ubuntu终端下进行 sudo update-grub 命令来恢复对win10的引导。


显卡驱动安装：
https://zhuanlan.zhihu.com/p/59618999
查看显卡型号：
ubuntu-drivers devices
自动安装推荐驱动
sudo ubuntu-drivers autoinstall

''' 暂时不使用下面的教程
参考https://blog.csdn.net/u010094199/article/details/54380086，可以看出显卡驱动我们用的是nvidia-367，toolkit（也就是deeplearning需要的）用官网cuda8.0 or 9.0，至于驱动直接用cuda官网的.run文件尚未尝试能否成功
             1、（感觉这一步并不必须）sudo gedit/etc/modprobe.d/blacklist.conf 在该文件后添加一下几行：
blacklist vga16fb 
blacklist nouveau 
blacklist rivafb 
blacklist rivatv 
blacklist nvidiafb 
2、保存后运行 sudo service lightdm stop关闭图形界面，（没有lightdm则直接跳过）
Ctrl+Alt+F1切换到tty1（参考下面的注意1）

 sudo add-apt-repository ppa:graphics-drivers/ppa
//引入PPA库里的显卡驱动

sudo apt-get install nvidia-367
//安装当前的长期稳定版nvidia-367驱动

3. sudo service lightdm start

4.用nvidia-smi验证是否安装成功

       注意：
              1、图形桌面--->命令行模式：Ctrl+Alt+F1/F2/F3/F4/F5/F6 （分别进入tty1～6，貌似只有第一次进入的tty才能用用户名和密码登陆，其他的无法登陆）
              2、命令行模式--->图形桌面：Ctrl+Alt+F7
              3、解除命令行模式锁定光标快捷键：Ctrl+Alt
            上面的过程由于ubuntu装了gtx 970显卡就开不了机，所以是在不插显卡，用主板上的集成显卡的过程进行的，上面过程结束后，就可以把gtx 970显卡插上去，重启电脑使用啦～
'''

安装cuda：
CUDA下载和安装：
cudnn: https://developer.nvidia.com/rdp/cudnn-download

cuda:   https://developer.nvidia.com/cuda-toolkit

显卡和CUDA版本的对应:https://developer.nvidia.com/cuda-gpus

1、具体在cuda官网选取和操作系统和显卡支出的cuda版本的.run文件（debian系统可以使用ubuntu16.04的cuda，遇到不匹配的提示可以选择yes忽略并继续安装），在命令行下bash *.run 运行（无需sudo权限，除非需要安装在系统目录下），（在ubuntu18.04装16.04的cuda会提示内核不匹配，可忽略），选择driver不安装，因为我们已经安装过nvidia-367的驱动了，选择tookit安装并可以自定义安装目录，选择example不安装。

2、cudnn在官网下载文件解压后复制到cuda安装目录/usr/local/cuda 中即可

3、最后需要在.bashrc中添加

export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
4、用nvcc --version验证是否安装成功



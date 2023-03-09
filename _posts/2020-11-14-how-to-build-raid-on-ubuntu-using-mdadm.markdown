---
layout: post
title: 在Ubuntu 20.04上使用mdadm创建RAID阵列
---

https://cloud.tencent.com/developer/article/1346533

https://www.digitalocean.com/community/tutorials/how-to-create-raid-arrays-with-mdadm-on-ubuntu-18-04


### 介绍

该mdadm实用程序可用于使用Linux的软件RAID功能创建和管理存储阵列。管理员可以非常灵活地协调各自的存储设备，并创建具有更高性能或冗余特性的逻辑存储设备。

在本指南中，我们将介绍可以使用Ubuntu 18.04服务器设置的多种不同RAID配置。

### 准备
要完成本指南中的步骤，您应该：

sudo具有Ubuntu 16.04服务器权限的非root用户：你需要一台已经设置好可以使用sudo命令的非root账号的Ubuntu服务器，并且已开启防火墙。没有服务器的同学可以在这里购买，不过我个人更推荐您使用免费的腾讯云开发者实验室进行试验，学会安装后再购买服务器。
对RAID术语和概念的基本了解：虽然本指南将逐步介绍一些RAID术语，但更完整的理解非常有用。要了解有关RAID的更多信息并更好地了解适合您的RAID级别。
您的服务器上有多个原始存储设备：我们将演示如何在服务器上配置各种类型的阵列。根据阵列类型，您至少需要两到四个存储设备。在遵循本指南之前，不需要格式化这些驱动器。您也可以使用腾讯云容器服务，他提供了比较完整的日志分析系统。腾讯云容器服务基于原生 kubernetes 提供以容器为核心的、高度可扩展的高性能容器管理服务。腾讯云容器服务完全兼容原生 kubernetes API ，扩展了腾讯云的 CBS、CLB 等 kubernetes 插件，为容器化的应用提供高效部署、资源调度、服务发现和动态伸缩等一系列完整功能，解决用户开发、测试及运维过程的环境一致性问题，提高了大规模容器集群管理的便捷性，帮助用户降低成本，提高效率。容器服务提供免费使用，涉及的其他云产品另外单独计费。
重置现有RAID设备
在本指南中，我们将介绍创建许多不同RAID级别的步骤。如果您希望继续操作，则可能需要在每个部分后重复使用存储设备。可以参考本节以了解如何在测试新RAID级别之前快速重置组件存储设备。如果尚未设置任何数组，请暂时跳过此部分。

警告：此过程将完全销毁数组以及写入其中的任何数据。确保您正在使用正确的阵列，并且在销毁阵列之前复制了需要保留的所有数据。

键入以下内容在/proc/mdstat文件中查找活动数组：

cat /proc/mdstat
Personalities : [raid0] [linear] [multipath] [raid1] [raid6] [raid5] [raid4] [raid10] 
md0 : active raid0 sdc[1] sdd[0]
      209584128 blocks super 1.2 512k chunks
​
            unused devices: <none>
从文件系统中卸载数组：

sudo umount /dev/md0
然后，键入以下命令停止并删除数组：

sudo mdadm --stop /dev/md0
使用以下命令查找用于构建阵列的设备：

警告：请记住，重新启动时/dev/sd*的名称可能会发生变化！每次检查它们以确保您使用正确的设备。

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
NAME     SIZE FSTYPE            TYPE MOUNTPOINT
sda      100G                   disk 
sdb      100G                   disk 
sdc      100G linux_raid_member disk 
sdd      100G linux_raid_member disk 
vda       25G                   disk 
├─vda1  24.9G ext4              part /
├─vda14    4M                   part 
└─vda15  106M vfat              part /boot/efi
在发现用于创建阵列的设备后，将其超级块清零以删除RAID元数据并将其重置为正常：

sudo mdadm --zero-superblock /dev/sdc
sudo mdadm --zero-superblock /dev/sdd
您应该删除对该数组的任何持久引用。编辑/etc/fstab文件并注释掉或删除对数组的引用：

sudo nano /etc/fstab
. . .
# /dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0
另外，注释掉或从/etc/mdadm/mdadm.conf文件中删除数组定义：

sudo nano /etc/mdadm/mdadm.conf
. . .
# ARRAY /dev/md0 metadata=1.2 name=mdadmwrite:0 UUID=7261fb9c:976d0d97:30bc63ce:85e76e91
最后，initramfs再次更新，以便早期启动过程不会尝试将不可用的阵列联机。

sudo update-initramfs -u
此时，您应该准备单独重用存储设备，或者作为不同阵列的组件。

### 创建RAID 0阵列
RAID 0阵列的工作原理是将数据分解为块并在可用磁盘上对其进行条带化。这意味着每个磁盘包含一部分数据，并且在检索信息时将引用多个磁盘。

要求：至少2个存储设备
主要好处：表现
要记住的事项：确保您有功能备份。单个设备故障将破坏阵列中的所有数据。
识别组件设备
要开始使用，请找到您将使用的原始磁盘的标识符：

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
NAME     SIZE FSTYPE TYPE MOUNTPOINT
sda      100G        disk
sdb      100G        disk
vda       25G        disk 
├─vda1  24.9G ext4   part /
├─vda14    4M        part 
└─vda15  106M vfat   part /boot/efi
如上所示，我们有两个没有文件系统的磁盘，每个磁盘大小为100G。在此示例中，已为这些设备提供了此会话的标识符/dev/sda和/dev/sdb标识符。这些将是我们用于构建阵列的原始组件。

创建数组
要使用这些组件创建RAID 0阵列，请将它们传递给mdadm --create命令。您必须指定要创建的设备名称（在我们的示例中是/dev/md0），RAID级别和设备数量：

sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sda /dev/sdb
您可以通过检查/proc/mdstat文件来确保成功创建RAID ：

cat /proc/mdstat
Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
md0 : active raid0 sdb[1] sda[0]
      209584128 blocks super 1.2 512k chunks
​
            unused devices: <none>
正如您在突出显示的行中所看到的，已使用/dev/sda和/dev/sdb设备在在RAID 0配置中创建了/dev/md0设备。

创建和挂载文件系统
接下来，在数组上创建一个文件系统：

sudo mkfs.ext4 -F /dev/md0
创建挂载点以附加新文件系统：

sudo mkdir -p /mnt/md0
您可以键入以下命令来挂载文件系统：

sudo mount /dev/md0 /mnt/md0
键入以下命令检查新空间是否可用：

df -h -x devtmpfs -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  1.4G   23G   6% /
/dev/vda15      105M  3.4M  102M   4% /boot/efi
/dev/md0        196G   61M  186G   1% /mnt/md0
新文件系统已安装并可访问。

保存数组布局
为了确保在引导时自动重新组装阵列，我们将不得不调整/etc/mdadm/mdadm.conf文件。您可以通过键入以下内容自动扫描活动数组并附加文件：

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
之后，您可以更新initramfs或初始RAM文件系统，以便在早期启动过程中阵列可用：

sudo update-initramfs -u
将新的文件系统挂载选项添加到/etc/fstab文件中以便在引导时自动挂载：

echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
现在，您的RAID 0阵列应自动组装并在每次启动时安装。

### 创建RAID 1阵列
RAID 1阵列类型通过在所有可用磁盘上镜像数据来实现。RAID 1阵列中的每个磁盘都可获得数据的完整副本，从而在设备发生故障时提供冗余。

要求：至少2个存储设备
主要好处：冗余
需要注意的事项：由于维护了两个数据副本，因此只有一半的磁盘空间可用
识别组件设备
要开始使用，请找到您将使用的原始磁盘的标识符：

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
NAME     SIZE FSTYPE TYPE MOUNTPOINT
sda      100G        disk
sdb      100G        disk
vda       25G        disk 
├─vda1  24.9G ext4   part /
├─vda14    4M        part 
└─vda15  106M vfat   part /boot/efi
如上所示，我们有两个没有文件系统的磁盘，每个磁盘大小为100G。在此示例中，已为这些设备提供了此会话的标识符/dev/sda和/dev/sdb标识符。这些将是我们用于构建阵列的原始组件。

创建数组
要使用这些组件创建RAID 1阵列，请将它们传递给mdadm --create命令。您必须指定要创建的设备名称（在我们的示例中是/dev/md0），RAID级别和设备数量：

sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sda /dev/sdb
如果您使用的组件设备不是boot启用了标志的分区，您可能会看到以下警告。键入y继续是安全的：

mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 104792064K
Continue creating array? y
该mdadm工具将开始镜像驱动器。这可能需要一些时间才能完成，但在此期间可以使用该阵列。您可以通过检查/proc/mdstat文件来监视镜像的进度：

cat /proc/mdstat
Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
md0 : active raid1 sdb[1] sda[0]
      104792064 blocks super 1.2 [2/2] [UU]
      [====>................]  resync = 20.2% (21233216/104792064) finish=6.9min speed=199507K/sec

unused devices: <none>
正如您在第一个突出显示的行中所看到的，/dev/md0已使用/dev/sda和/dev/sdb设备在RAID 1配置中创建了设备。第二个突出显示的行显示镜像的进度。您可以在此过程完成时继续指南。

创建和挂载文件系统
接下来，在数组上创建一个文件系统：

sudo mkfs.ext4 -F /dev/md0
创建挂载点以附加新文件系统：

sudo mkdir -p /mnt/md0
您可以键入以下命令来挂载文件系统：

sudo mount /dev/md0 /mnt/md0
键入以下命令检查新空间是否可用：

df -h -x devtmpfs -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  1.4G   23G   6% /
/dev/vda15      105M  3.4M  102M   4% /boot/efi
/dev/md0         99G   60M   94G   1% /mnt/md0
新文件系统已安装并可访问。

保存数组布局
为了确保在引导时自动重新组装阵列，我们将不得不调整/etc/mdadm/mdadm.conf文件。您可以通过键入以下内容自动扫描活动数组并附加文件：

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
之后，您可以更新initramfs或初始RAM文件系统，以便在早期启动过程中阵列可用：

sudo update-initramfs -u
将新的文件系统挂载选项添加到/etc/fstab文件中以便在引导时自动挂载：

echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
现在，您的RAID 1阵列应自动组装并在每次启动时安装。

### 创建RAID 5阵列
RAID 5阵列类型通过在可用设备上条带化数据来实现。每个条带的一个分量是计算的奇偶校验块。如果设备发生故障，则可以使用奇偶校验块和其余块来计算丢失的数据。接收奇偶校验块的设备被旋转，使得每个设备具有平衡量的奇偶校验信息。

要求：至少3个存储设备
主要好处：具有更多可用容量的冗余。
需要注意的事项：在分配奇偶校验信息时，一个磁盘的容量将用于奇偶校验。在处于降级状态时，RAID 5可能会遭受非常差的性能。
识别组件设备
要开始使用，请找到您将使用的原始磁盘的标识符：

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
sda      100G        disk
sdb      100G        disk
sdc      100G        disk
vda       25G        disk 
├─vda1  24.9G ext4   part /
├─vda14    4M        part 
└─vda15  106M vfat   part /boot/efi
如上所示，我们有三个没有文件系统的磁盘，每个磁盘大小为100G。在这个例子中，这些设备被赋予了/dev/sda，/dev/sdb和/dev/sdc会话标识符。这些将是我们用于构建阵列的原始组件。

创建数组
要使用这些组件创建RAID 5阵列，请将它们传递给mdadm --create命令。您必须指定要创建的设备名称（在我们的示例中是/dev/md0），RAID级别和设备数量：

sudo mdadm --create --verbose /dev/md0 --level=5 --raid-devices=3 /dev/sda /dev/sdb /dev/sdc
该mdadm工具将开始配置阵列（出于性能原因，它实际上使用恢复过程来构建阵列）。这可能需要一些时间才能完成，但在此期间可以使用该阵列。您可以通过检查/proc/mdstat文件来监视镜像的进度：

cat /proc/mdstat
Personalities : [raid1] [linear] [multipath] [raid0] [raid6] [raid5] [raid4] [raid10] 
md0 : active raid5 sdc[3] sdb[1] sda[0]
      209584128 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/2] [UU_]
      [===>.................]  recovery = 15.6% (16362536/104792064) finish=7.3min speed=200808K/sec

unused devices: <none>
正如在第2行，/dev/md0设备已使用的/dev/sda，/dev/sdb以及/dev/sdc设备在RAID 5配置中创建。第4行显示了构建的进度。

警告：由于mdadm构建RAID 5阵列的方式，在阵列仍在构建时，阵列中的备件数量将报告不准确。这意味着在更新/etc/mdadm/mdadm.conf文件之前必须等待阵列完成组装。如果在阵列仍在构建时更新配置文件，则系统将具有有关阵列状态的错误信息，并且无法在引导时使用正确的名称自动组装它。

您可以在此过程完成时继续指南。

创建和挂载文件系统
接下来，在数组上创建一个文件系统：

sudo mkfs.ext4 -F /dev/md0
创建挂载点以附加新文件系统：

sudo mkdir -p /mnt/md0
您可以键入以下命令来挂载文件系统：

sudo mount /dev/md0 /mnt/md0
键入以下命令检查新空间是否可用：

df -h -x devtmpfs -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  1.4G   23G   6% /
/dev/vda15      105M  3.4M  102M   4% /boot/efi
/dev/md0        197G   60M  187G   1% /mnt/md0
新文件系统已安装并可访问。

保存数组布局
为了确保在引导时自动重新组装阵列，我们将不得不调整/etc/mdadm/mdadm.conf文件。

如上所述，在调整配置之前，请再次检查以确保阵列已完成组装。 在构建阵列之前完成此步骤将阻止系统在重新引导时正确组装阵列：

cat /proc/mdstat
Personalities : [raid1] [linear] [multipath] [raid0] [raid6] [raid5] [raid4] [raid10] 
md0 : active raid5 sdc[3] sdb[1] sda[0]
      209584128 blocks super 1.2 level 5, 512k chunk, algorithm 2 [3/3] [UUU]

unused devices: <none>
上面的输出显示重建已完成。现在，我们可以自动扫描活动数组并通过键入以下内容来附加文件：

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
之后，您可以更新initramfs或初始RAM文件系统，以便在早期启动过程中阵列可用：

sudo update-initramfs -u
将新的文件系统挂载选项添加到/etc/fstab文件中以便在引导时自动挂载：

echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
现在，您的RAID 5阵列应自动组装并在每次启动时安装。

### 创建RAID 6阵列
RAID 6阵列类型通过在可用设备上条带化数据来实现。每个条带的两个分量是计算的奇偶校验块。如果一个或两个设备发生故障，则可以使用奇偶校验块和其余块来计算丢失的数据。接收奇偶校验块的设备被旋转，使得每个设备具有平衡量的奇偶校验信息。这类似于RAID 5阵列，但允许两个驱动器发生故障。

要求：至少4个存储设备
主要优点：双冗余，可用容量更大。
需要注意的事项：在分配奇偶校验信息时，两个磁盘的容量将用于奇偶校验。RAID 6在处于降级状态时可能会遭受非常差的性能。
识别组件设备
要开始使用，请找到您将使用的原始磁盘的标识符：

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
NAME     SIZE FSTYPE TYPE MOUNTPOINT
sda      100G        disk
sdb      100G        disk
sdc      100G        disk
sdd      100G        disk
vda       25G        disk 
├─vda1  24.9G ext4   part /
├─vda14    4M        part 
└─vda15  106M vfat   part /boot/efi
如您所见，我们有四个没有文件系统的磁盘，每个磁盘大小为100G。在这个例子中，这些设备被赋予了/dev/sda，/dev/sdb，/dev/sdc，和/dev/sdd会话标识符。这些将是我们用于构建阵列的原始组件。

创建数组
要使用这些组件创建RAID 6阵列，请将它们传递给mdadm --create命令。您必须指定要创建的设备名称（在我们的示例中是/dev/md0），RAID级别和设备数量：

sudo mdadm --create --verbose /dev/md0 --level=6 --raid-devices=4 /dev/sda /dev/sdb /dev/sdc /dev/sdd
该mdadm工具将开始配置阵列（出于性能原因，它实际上使用恢复过程来构建阵列）。这可能需要一些时间才能完成，但在此期间可以使用该阵列。您可以通过检查/proc/mdstat文件来监视镜像的进度：

cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4] [linear] [multipath] [raid0] [raid1] [raid10] 
md0 : active raid6 sdd[3] sdc[2] sdb[1] sda[0]
      209584128 blocks super 1.2 level 6, 512k chunk, algorithm 2 [4/4] [UUUU]
      [>....................]  resync =  0.6% (668572/104792064) finish=10.3min speed=167143K/sec

unused devices: <none>
可以在第2行看到，/dev/md0设备已在RAID 6配置中通过/dev/sda，/dev/sdb，/dev/sdc和/dev/sdd设备创建。第4行显示了构建的进度。您可以在此过程完成时继续指南。

创建和挂载文件系统
接下来，在数组上创建一个文件系统：

sudo mkfs.ext4 -F /dev/md0
创建挂载点以附加新文件系统：

sudo mkdir -p /mnt/md0
您可以键入以下命令来挂载文件系统：

sudo mount /dev/md0 /mnt/md0
键入以下命令检查新空间是否可用：

df -h -x devtmpfs -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  1.4G   23G   6% /
/dev/vda15      105M  3.4M  102M   4% /boot/efi
/dev/md0        197G   60M  187G   1% /mnt/md0
新文件系统已安装并可访问。

保存数组布局
为了确保在引导时自动重新组装阵列，我们将不得不调整/etc/mdadm/mdadm.conf文件。我们可以通过输入以下内容自动扫描活动数组并附加文件：

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
之后，您可以更新initramfs或初始RAM文件系统，以便在早期启动过程中阵列可用：

sudo update-initramfs -u
将新的文件系统挂载选项添加到/etc/fstab文件中以便在引导时自动挂载：

echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
现在，您的RAID 6阵列应自动组装并在每次启动时安装。

创建复杂的RAID 10阵列
传统上，RAID 10阵列类型通过创建由多组RAID 1阵列组成的条带化RAID 0阵列来实现。这种嵌套数组类型提供冗余和高性能，但代价是大量磁盘空间。该mdadm实用程序具有自己的RAID 10类型，可提供相同类型的优势并提高灵活性。它不是由嵌套数组创建的，而是具有许多相同的特性和保证。我们将在mdadm这里使用RAID 10。

要求：至少3个存储设备
主要好处：性能和冗余
需要注意的事项：阵列的容量减少量由您选择保留的数据副本数量来定义。使用mdadm样式RAID 10 存储的副本数是可配置的。
默认情况下，每个数据块的两个副本将存储在所谓的“近”布局中。决定每个数据块如何存储的可能布局是：

附近：默认安排。当条带化时，每个块的副本被连续写入，这意味着数据块的副本将被写在多个磁盘的相同部分周围。
far：第一个和后续副本被写入阵列中存储设备的不同部分。例如，第一个块可能写在磁盘的开头附近，而第二个块则写在另一个磁盘的中间。这可以为传统旋转磁盘提供一些读取性能增益，但代价是写入性能。
offset：复制每个条带，由一个驱动器偏移。这意味着副本彼此偏移，但仍然在磁盘上靠近。这有助于在某些工作负载期间最大限度地减
您可以通过查看man本页的“RAID10”部分找到有关这些布局的更多信息：

man 4 md
您也可以在这里man在线找到此页面。

识别组件设备
要开始使用，请找到您将使用的原始磁盘的标识符：

lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
NAME     SIZE FSTYPE TYPE MOUNTPOINT
sda      100G        disk
sdb      100G        disk
sdc      100G        disk
sdd      100G        disk
vda       25G        disk 
├─vda1  24.9G ext4   part /
├─vda14    4M        part 
└─vda15  106M vfat   part /boot/efi
如您所见，我们有四个没有文件系统的磁盘，每个磁盘大小为100G。在这个例子中，这些设备被赋予了/dev/sda，/dev/sdb，/dev/sdc，和/dev/sdd会话标识符。这些将是我们用于构建阵列的原始组件。

创建数组
要使用这些组件创建RAID 10阵列，请将它们传递给mdadm --create命令。您必须指定要创建的设备名称（在我们的示例中是/dev/md0），RAID级别和设备数量。

您可以使用near布局设置两个副本，方法是不指定布局和副本编号：

sudo mdadm --create --verbose /dev/md0 --level=10 --raid-devices=4 /dev/sda /dev/sdb /dev/sdc /dev/sdd
如果要使用其他布局或更改副本数，则必须使用--layout=选项，该选项采用布局和副本标识符。布局为n表示近，f表示远，o表示偏移。之后会附加要存储的副本数。

例如，要创建一个在偏移布局中具有3个副本的数组，该命令将如下所示：

sudo mdadm --create --verbose /dev/md0 --level=10 --layout=o3 --raid-devices=4 /dev/sda /dev/sdb /dev/sdc /dev/sdd
该mdadm工具将开始配置阵列（出于性能原因，它实际上使用恢复过程来构建阵列）。这可能需要一些时间才能完成，但在此期间可以使用该阵列。您可以通过检查/proc/mdstat文件来监视镜像的进度：

cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4] [linear] [multipath] [raid0] [raid1] [raid10] 
md0 : active raid10 sdd[3] sdc[2] sdb[1] sda[0]
      209584128 blocks super 1.2 512K chunks 2 near-copies [4/4] [UUUU]
      [===>.................]  resync = 18.1% (37959424/209584128) finish=13.8min speed=206120K/sec

unused devices: <none>
可以在第2行看到，/dev/md0设备已在RAID 10配置通过/dev/sda，/dev/sdb，/dev/sdc和/dev/sdd设备创建。第3行显示了此示例使用的布局（近配置中的2个副本）。第4行显示了构建的进度。您可以在此过程完成时继续指南。

创建和挂载文件系统
接下来，在数组上创建一个文件系统：

sudo mkfs.ext4 -F /dev/md0
创建挂载点以附加新文件系统：

sudo mkdir -p /mnt/md0
您可以键入以下命令来挂载文件系统：

sudo mount /dev/md0 /mnt/md0
键入以下命令检查新空间是否可用：

df -h -x devtmpfs -x tmpfs
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  1.4G   23G   6% /
/dev/vda15      105M  3.4M  102M   4% /boot/efi
/dev/md0        197G   60M  187G   1% /mnt/md0
新文件系统已安装并可访问。

保存数组布局
为了确保在引导时自动重新组装阵列，我们将不得不调整/etc/mdadm/mdadm.conf文件。我们可以通过输入以下内容自动扫描活动数组并附加文件：

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
之后，您可以更新initramfs或初始RAM文件系统，以便在早期启动过程中阵列可用：

sudo update-initramfs -u
将新的文件系统挂载选项添加到/etc/fstab文件中以便在引导时自动挂载：

echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab
现在，您的RAID 10阵列应自动组装并在每次启动时安装。

结论
在本指南中，我们演示了如何使用Linux的mdadm软件RAID实用程序创建各种类型的阵列。与单独使用多个磁盘相比，RAID阵列提供了一些引人注目的冗余和性能增强。



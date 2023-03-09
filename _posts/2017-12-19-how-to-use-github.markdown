---
layout: post
title: github使用总结（持续更新中）
---

http://www.runoob.com/w3cnote/git-guide.html


１、（远端操作）在github官网上注册一个账号，创建一个工程
　　２、（本地操作并在远端进行配置）参考　　http://blog.csdn.net/yy243/article/details/53243948
　　　　创建ssh key（如果已经有了ssh key就不需要再创建）
　　　　　ssh-keygen -t rsa -C "your_email@youremail.com"
          后面的your_email@youremail.com改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的，一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pu，复制里面的key。回到github上，进入Account Settings（账户设置），左边选择SSH Keys, Add SSH Key, 粘贴在电脑上生成的key。
３、（本地操作）若在该电脑上首次使用，则需要配置：
　　　　　git config --list 查看当前用户信息
　　　　　git config --global user.name " "    # 用户名和github上注册的一致
                  git config --global user.email "  "　# 邮箱和github上注册的一致
　　　　　可以使用global参数进行全局的配置，也可以用local等其他参数
　　　　　下面这种方式讲默认在仓库局部配置，全局和局部冲突时，局部优先
　　　　　git config user.name " "    # 用户名和github上注册的一致
                  git config user.email "  "　# 邮箱和github上注册的一致

４、（本地操作并上传远端）
　　　　任意新建一个文件夹test_only，并进入
              echo "# test_only" >> README.md 
　　　    git init 
              git add README.md 
              git commit -m "first commit" 
              git remote add origin https://github.com/XinGuo1993/test_only.git
　　　    git push -u origin master
５、（本地和远端进行匹配）
         在本地创建分支
　　　git checkout -b <分支名>
　　　切回主分支
　　　git checkout master
        删除新建分支
　　　git checkout -d <分支名>


６、git push 和git pull命令
将本地分支推动到远端
git push origin <分支名>

将远程分支拉到本地，如果有文件冲突会提示，并记录，本地多出的文件会保留
git pull origin <分支名>

关键词解释：
　　　　origin（github上的仓库）
      master（主分支）
          　 remote（远端，通常远端默认是origin）


https://segmentfault.com/q/1010000002664985　有一个很好的解释
git其实是是一个不用网络的仓库（本地仓库），你也可以把数据push到github上（远程仓库）。
你现在的pull和push都是本地版本库和远程仓库之间的数据交互。
在你的本地仓库，其实是由两部分组成：

工作区 (Working Directory) //看得见的
版本库 (Repository) //看不见的

暂存区(Stage)
分支 (branch)
版本库包含暂存区和分支

流程：
初次提交：
- 通过git add 将文件 工作区 ---》暂存区 (本地)
- 通过git commit 将文件 暂存区 ---》分支 (本地)
- 通过git push 将文件 分支 ---》远程库 (github)

提交改动：
- 通过git commit将文件 暂存区 ---》分支 (本地)
- 通过git push 将文件 分支 ---》远程库 (github)

pull&push
- 通过git pull 将文件 远程库 ---》分支 (本地)
- 通过git push 将文件 分支 ---》远程库 (github)

而上面的两个操作是需要有改动，有差异才能执行。
所以会提示暂存区和远程库的内容一致。

进阶：分支操作

git branch ：查看当前有哪些branch

git checkout gx：git clone操作后会将远程的分支都和本地关联，虽然执行git branch不会显示其他分支，但可通过git checkout命令进行切换分支。也就是说git checkout gx是切换到gx分支（gx可以是本地已存在分支或者远端已存在分支）

git checkout -b gx：基于当前的分支，拷贝创建本地新的分支gx，并切换到gx

删除分支：

1）删除本地分支：git branch -D BranchName

              其中-D也可以是--delete，如：git branch --delete BranchName

      2）删除本地的远程分支：git branch -r -D origin/BranchName

      3）远程删除git服务器上的分支：git push origin -d BranchName

            其中-d也可以是--delete，如：git push origin --delete BranchName

     在本地创建新分支gx后，执行git push，如果远端没有gx这个分支会提示使用：

git push --set-upstream origin gx 进行创建和链接

       如果远端有gx这个分支，会提示使用：

git branch --set-upstream-to=origin/ gx 选定远端分支进行设定



　　　　
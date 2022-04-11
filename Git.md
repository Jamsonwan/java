# Git

**版本控制工具**

## 机制

远程库（代码托管中心： 互联网：GitHub，局域网：GitLab）

本地库（历史版本）

暂存区（临时存储）

工作区（写代码）

## 常用命令

* 设置用户签名(与登录github的账号无关)
  * git config --global user.name xxx
  * git config --global user.email xxx.dd@dd.com
* 初始化本地库git init
* 查看本地工作区的状态
  * git status
* 追踪文件（添加到暂存区）
  * git add file.txt
  * 移除暂存区 git rm --cached file.txt
* 提交本地库（形成历史版本）
  * git commit -m  “版本说明” (暂存区文件)
  * 查看版本
    * 精简版 git reflog
    * 完整版 git log
* 版本穿梭
  * 先查看版本信息  git reflog
  * git reset --hard  bce4d89（版本号）

## 分支

* 创建分支 git branch branchName
* 查看分支 git branch -v
* 切换分支 git checkout branchName
* 合并分支 git merge branchName (把branchName合并到当前分支)
  * 冲突需要手动删除和保留信息

## GitHub

* 创建别名
  * git remote git-demo https://github.com/Jamsonwan/git-demo.git
* 查看别名
  * git remote -v 
* 推送
  * git push 别名 分支
  * git push git-demo master
* 拉取 pull
  * git pull 别名 分支
  * git pull git-demo master

* 免密钥登录
  * ssh -keygen -t rsa -C JamsonWan92@gmail.com

## .Ignore
* 直接写名字
  * venv
  * .gitignore
  * .idea/

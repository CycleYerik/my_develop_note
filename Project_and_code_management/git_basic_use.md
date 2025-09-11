# 基本概述  
https://www.cnblogs.com/jamiechoo/articles/18408791

### clone


克隆远端仓库到本地
git clone <git url>

克隆远端仓库到本地，并同时切换到指定分支 branch1
git clone <git url> -b branch1

克隆远端仓库到本地并指定本地仓库的文件夹名称为 my-project
git clone <git url> my-project

### 初始化仓库
git init
git add .
git --global user.name "Your Name"
git --global user.email "Your Email"
git commit -m "first commit"  
git remote add origin <git url>
git push -u origin master

### 版本切换  
git branch -a 所有版本  
git branch -vv 远程与本地关联  
git checkout test 本地切换到test分支

切换到已有的本地分支 branch1
git checkout branch1

切换到远程分支 branch1
git checkout origin/branch1

基于当前本地分支创建一个新分支 branch2，并切换至 branch2
git checkout -b branch2

基于远程分支 branch1 创建一个新分支 branch2，并切换至 branch2
git checkout origin/branch1 -b branch2

当前创建的 branch2 关联的上游分支是 origin/branch1，所以 push 时需要如下命令关联到远程 branch2
git push --set-upstream origin branch2

撤销工作区 file 内容的修改。危险操作，谨慎使用
git checkout -- <file>

撤销工作区所有内容的修改。危险操作，谨慎使用
git checkout .


### 提交  

将所有修改的文件都提交到暂存区
git add .

将修改的文件中的指定的文件 a.js 和 b.js 提交到暂存区
git add ./a.js ./b.js

将 js 文件夹下修改的内容提交到暂存区
git add ./js

把当前本地的分支推送到远程dev-CY分支（需要关联）
git push origin HEAD:dev-CY 

将当前本地分支 branch1 内容推送到远程分支 origin/branch1
git push

若当前本地分支 branch1，没有对应的远程分支 origin/branch1，需要为推送当前分支并建立与远程上游的跟踪
git push --set-upstream origin branch1

强制提交
例如用在代码回滚后内容
git push -f


### 拉取
若拉取并合并的远程分支和当前本地分支名称一致

例如当前本地分支为 branch1，要拉取并合并 origin/branch1，则直接执行：
git pull

若拉取并合并的远程分支和当前本地分支名称不一致
git pull <远程主机名> <分支名>

例如当前本地分支为 branch2，要拉取并合并 origin/branch1，则执行：
git pull git@github.com:zh-lx/git-practice.git branch1

使用 rebase 模式进行合并
git pull --rebase

### 回撤提交

将 a.js 文件取消缓存（取消 add 操作，不改变文件内容）
git reset --staged a.js

将所有文件取消缓存
git reset --staged .

取消某次 commit 内容，但是保留 commit 记录
git revert <commit-sha>

### 缓存代码
把本地的改动缓存起来
git stash

缓存代码时添加备注，便于查找。强烈推荐
git stash save "message"

查看缓存记录
eg: stash@{0}: On feat-1.1: 活动功能
git stash list

取出上一次缓存的代码，并删除这次缓存
git stash pop
取出 index 为2缓存代码，并删除这次缓存，index 为对应 git stash list 所列出来的
git stash pop stash@{2}

取出上一次缓存的代码，但不删除这次缓存
stash apply
取出 index 为2缓存代码，但不删除缓存
git stash apply stash@{2}

清除某次的缓存
git stash drop stash@{n}

清除所有缓存
git stash clear


### 修改远程等
git remote set-url origin xxx(为改名后的新地址)



### 查看工作区状态、日志

查看当前工作区暂存区变动
git status 

以概要形式查看工作区暂存区变动
git status -s 

查询工作区中是否有 stash 缓存
git status --show-stash


显示 commit 日志
git log

以简要模式显示 commit 日志
git log --oneline

显示最近 n 次的 commit 日志
git log -n

显示 commit 及分支的图形化变更
git log --graph --decorate

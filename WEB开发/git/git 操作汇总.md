---
title: "git操作汇总"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["git"]
categories: ["git"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 1. 配置相关
### 简单配置操作
- **git config --global user.name**  XXXX

- 常用的配置属性还有**user.email, color.ui, core.editor, alias**
 
- 显示当前的配置信息
  - **git config --list**

## 2. 本地操作
### 仓库相关
- 从其他仓库获取
  - **git clone git@code.csdn.net:liuquan0071/XXX.git**
  
- 初始化仓库
  - **git int** 在当前文件夹中建立
  
- 显示状态
  - **git status**

### 简单文件操作

- 增加和提交文件
  - **git add** files
  
  - **git add -p** 交互式，可选择
  
  - **git commit -m "XXX"** 直接提交已暂存文件
  
  - **git commit -am "XXX"** 直接提交未暂存文件
  
  - 若输入```git commit```或者```gitcommit -m```，则会跳转到编辑器中提示编写提交信息。编辑器类型可以通过```git config --global core.editor XXX```设定
    - 如果是新增文件，不可一次性commit，需要先```git add```添加到暂存区 
      
  
- 删除文件
  - **git rm file** 彻底删除某个文件
  
  - **git rm --cached file** 从版本库中删除，保留本地文件。即取消跟踪状态
  
- 重命名文件
  - **git mv file newfile**
  
- 显示提交日志
  - **git log**
  
  - **git log -p** 显示patch
  
  - **git log　--stat** 显示改动的一个总结
  
  - **git log --graph** 只显示当前branch的
  
  - **git log --graph --all** 显示所有branch的
  
  - **git log --graph --all --decorate** 显示branch的名字
  
  - **git log --pretty=oneline,short,full,fuller** 输出不同的形式
  
  - **git log --pretty=oneline, short, full, fuller** 输出的log 形式不同
  
  - **git log --pretty=format:"%h - %an, %ar : %s"** 按照指定的格式输出
    
    - 关于```--pretty```的其他选项和具体的format格式，参考 ```git log --help```中PRETTY FORMAT这部分。
  
  - **git log --follow file**
    - 通常我们会移动某个文件到某个目录下。如果这么做，```git log```是不能显示目录移动前的记录的。那就加上 ```--follow```吧
    
- 显示日志筛选
  - **git log -2 -p** 显示最近两次commit的log 和 diff
  
  - **git log --author="liuquan"** 筛选特定作者的log
 
  - **git log --since="2016-9-21" --before="2016-9-22"** 筛选时间段
  
  - **git log --grep="key word"** 在commit 的message中查找关键字
  
  - **git log dev --not master**  dev，但不在master上的记录。 
  
  - **git log -S"func_name"**   查找某个字符出现，或者移出的commit。 比如可以查找一个函数是什么时候添加，或者删除的。
  
  - **git show sha1** 这个sha1是每个commit的sha1，这样显示某个commit的完全信息，包括diff
  
  - **git log master ^dev** 可以在merge前看看有哪些需要commit
    - 在master上，但不在dev上的
  
### 改动相关
- 撤销未提交改动
  - **git checkout -- file** 撤销了未暂存区文件的改动
    - `--`表明了这是一个文件，而不是分支的名字
  
  - **git checkout head file** 暂存区和未暂存区的改动都会清除
  
  - **git reset --hard head** 撤销所有未提交的改动，包括暂存区和未暂存区
  
- 撤销已提交改动
  - **git revert head** 自动重新做一个commit，返回最后一次commit
  
  - **git reset --hard~1** 删除最近的一次提交（类似指针前移）
 
  - **git reset --hard (SHA1)** 回退已提交状态，包括暂存区和未暂存区（相当于回到文件初始状态，即上一次提交状态）
  
  - **git reset --mixed (SHA1)** 回退已提交状态，回到未暂存区
  
  - **git reset --soft (SHA1)** 回退已提交状态，回到暂存区
  
  - **git commit --amend** 提交最新，代替之前的提交
  
- 显示所做的改动
  - **git diff** 未暂存区VS本地初始文件（或者暂存区）
    - 同一文件先add到暂存区，然后又做了修改，此时被比较的文件是暂存区的
  
  - **git diff --staged**或者**git diff --cached** 暂存区VS本地版本库
  
  - **git diff commit1 commit2** 从commit1到commit2的变动
    - 注意：顺序不定，可以逆向
  
  - **git blame -C file** 显示文件的具体改动
  
  - **git blame -L n,m file** 查看n、m两行间的改动
  
  - **git blame commit1~1 -L n,m file** 查看commit1版本之前的改动
  
  - **git blame commit1~1 -L n,m -- oldfile**
    - 如果这个文件被命名过，或者移动过，就要输入旧文件的名字。
    
    - 而且一定要加上 `--`
    
## 分支相关
### 简单分支操作
- 显示分支
  - **git branch**
  
  - **git branch -v** 显示详细点的信息
  
  - **git branch --merged**

- 创建分支
  - **git branch XXX**
  
  - **git checkout -b XXX**
  
  - **git branch XXX commit1**
 
- 切换分支
  - **git checkout XXX**
  
- 图形化显示分支信息
  - **gitk**
  
  - **gitk --all**
  
- 合并分支
  - **git merge XXX**
  
- 删除分支
  - **git branch -d XXX**
  
- 处理冲突
  - **git mergetool**

- 查看哪些分支合并了
  - **git branch --merged**
  
  - **git branch --no-merged**
  
- 备份工作区
  - **git stash**

### 标签相关

- 显示tag
  - **git tag**
  
  - **git tag -l v3.0** 匹配显示
  
  - **git show XXX** 显示某个tag的详细信息
  
- 创建tag
  - **git tag -a XXX -m "XXXXX"**
  
  - **git tag -s XXX -m "XXXXX"**
    - 这个将会加上pgp的签名
  
  - **git tag -a XXX SHA** 对某个指定的SHA大tag
  
- 推送tag，默认情况是不会推送tag到远端的
  - **git push origin XXX**
  
  - **git push origin --tags** 推送所有标签

- 验证tag
  - **git tag -v XXX** 对于`-s`签名的tag
  
## 远端相关
### 简单远端操作
- 显示远端信息
  - **git remote**
  
  - **git remote -v**
  
  - **git remote show origin**

- 添加其他远端库
  - **git remote add XXX**
  
- 从远端下载
  - **git fetch XXX**
  
  - **git pull** 下载更新后直接`merge`了
  
  - **git clone**
  
- 上传
  - **git push origin master**
  
- 删除远端分支
  - **git push origin :master**

- 同步
  - **git fetch** 同步状态
  
  - **git fetch -p** 可以清空远端已经不用的分支
  
  - **git branch -v**  可以看到本地仓库中分支的状态。
  
  - **git branch -av** 可以看到远端和本地分支的状态
  
  - **git checkout XXX** 换到你想要更新的local branch上
  
  - **git rebase origin/XXX** 这样可以将本地做的commit 取下，并重新apply到最新的地方。

## 补丁相关
### 简单操作
- patch相关
  - **git apply patchfile** 从patchfile中应用这个patch。 效果和patch命令类似。 
    - `apply`要么成功，要么不成功。而`patch`可能有部分的patch打上了，有的没有打上。
    
    - `git apply`后，并没有自动生成一个commit
  
  - **git apply --check**  可以用来检测这个patch是不是会产生冲突或者失败
  
  - **git am patchfile** 专为git设计patchfile，patchfile是通过`git format-patch`生成的，会自动生成一个commit
  
  - **git am --resolved** 
    - `git am`过程中可能会有conflict. 若遇到conflict，就需要手动修改code，`git add`后用`git am --resolved`  
  
  - 可以学习一下git制作patch
  
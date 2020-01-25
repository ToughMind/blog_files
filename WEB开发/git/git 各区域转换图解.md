---
title: "git 各区域转换图解"
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
![这里写图片描述](http://img.blog.csdn.net/20160920214513380)

## 注意：此图解主要针对已跟踪状态文件而言，对于新增文件，有些操作会有问题。
### 1. 修改本地已被跟踪文件，文件进入未暂存区域。

### 2. 未暂存区域转到暂存区域
- **git add** *files*

### 3. 暂存区提交到本地仓库
- **git commit -m** 

### 4. 直接从未暂存区提交到本地仓库
- **git commit -am**

- 经测试，对已跟踪的文件可以正确执行，而对于未跟踪文件（即新增文件）则会出错

### 5. 本地库回退到暂存区
- **git reset --soft** *hash值*

- **git reset --soft** *origin/master*

- 一般回退到暂存区的文件作排查用，不要直接修改，不然会同时出现在暂存区和未暂存区（其实即使修改了也木有太大关系）

### 6. 本地库回退到未暂存区
- **git reset --mixed** *hash值*

- **git reset --mixed** *origin/master*

- 一般回退到未暂存状态就是为了进一步的修改

### 7. 本地库回退到文件初始状态（即此版本的）
- **git reset --hard** *hash值*

- 注意这里，通常先执行一次fetch，保证本地版本是origin的最新版本，然后再回退。（最厉害的是，这么操作不会有冲突，直接让文件变成和origin保持一致）  
  - **git fetch origin**
  - **git reset --hard** *origin/master*
  - 特别注意：这么操作会使你对文件的修改全部消失，还原成最初状态。
  
- (针对上一条情况衍生讲解)通常在推送到origin时，先要pull，然后再推送，一般是修改提交了的文件和pull下来的同一个文件产生冲突（所以建议修改代码前，一定先要pull）
  - **git pull**
  - **git push** *origin master*
  
### 8. 暂存区回退到未暂存区
- **git reset --** *files*

- **git rest**
  - 撤销所有暂存区的文件

### 9. 未暂存区回退到文件初始状态
- **git checkout --** *files*
  
### 10. 暂存区回退到文件初始状态
- **git checkout head --** *files*
 
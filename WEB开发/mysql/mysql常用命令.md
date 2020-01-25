---
title: "mysql常用命令"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["mysql"]
categories: ["mysql"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 2016.8.21
### 查看和更改字符集语句
- 查找MySQL的cnf文件的位置
    - find / -iname '*.cnf' -print

- 查看字符集设置
  - show variables like 'collation_%'; 
  - show variables like 'character_set_%';
  
### 彻底卸载mysql
```
sudo apt-get autoremove --purge mysql-server-5.7 
Y
sudo apt-get remove mysql-common
sudo rm -rf /etc/mysql/  /var/lib/mysql    #很重要
#清理残留数据
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P  
sudo apt autoremove
sudo apt autoreclean
```
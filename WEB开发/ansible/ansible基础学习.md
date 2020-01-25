---
title: "ansible基础学习"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["ansible"]
categories: ["ansible"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

## 安装ansible
```
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

## 基本连接命令
```
ansible -i ./hosts --connection=local local -m ping
ansible -i ./hosts remote -v -m ping -u root --private-key=~/.ssh/id_rsa
```
## 角色相关
```
ansible-galaxy init roles/mysql
# 安装别人写好的roles：(galaxy.ansible.com)
ansible-galaxy install -p /etc/ansible/roles bennojoy.mysql
# 安装到指定目录
ansible-galaxy -p ./roles install bennojoy.ntp
```
> 变量名不可以用中划线

## 常用命令
```
-i PATH --inventory=PATH 指定host文件的路径，默认是在/etc/ansible/hosts
--private-key=PRIVATE_KEY_FILE_PATH 使用指定路径的秘钥建立认证连接
-m DIRECTORY --module-path=DIRECTORY 指定module的目录来加载module，默认是/usr/share/ansible
-c CONNECTION --connection=CONNECTION 指定建立连接的类型，一般有ssh ，local
```


## 模块
[https://docs.ansible.com/ansible/latest/modules/](https://docs.ansible.com/ansible/latest/modules/)

## 可参考链接
- [ansible入门教程](https://blog.csdn.net/pushiqiang/article/details/78126063)
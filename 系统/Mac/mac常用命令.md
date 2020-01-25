---
title: "mac常用命令"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["mac"]
categories: ["mac"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---

## gem
> ruby下的gem，nodejs下的npm

命令 | 说明
---|---
gem sources -l | 查看gem源地址
gem sources --remove https://rubygems.org/ | 移除默认源
gem sources -a https://gems.ruby-china.org/  |添加ruby-china的源


## nginx

命令 | 说明
---|---
nginx -s reload | 修改配置后重新加载生效
nginx -s reopen | 重新打开日志文件
nginx -s stop | 快速停止nginx
nginx -s quit | 完整有序的停止nginx
nginx -t -c /path/to/nginx.conf | 测试nginx配置文件是否正确
kill -QUIT 主进程号 | 从容停止Nginx
kill -TERM 主进程号 | 快速停止Nginx
pkill -9 nginx | 强制停止Nginx
nginx -c /path/to/nginx.conf | 启动nginx
kill -HUP 主进程号 | 平滑重启nginx

### 配置
项目 | 说明
---|---
目录 | /usr/local/etc/nginx/




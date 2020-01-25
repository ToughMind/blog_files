---
title: "shadowsocks"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["网络"]
categories: ["网络"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
# 配置文件
```
{
  "server":"0.0.0.0",
  "server_ipv6":"::",
  "local_address":"127.0.0.1",
  "local_port":1080,
  "port_password":{
   #纯 SS 不带混淆 端口25 密码为123456.
   "25":"123456",
   #端口443，密码123456 ，protocol选择auth_chain_a。obfs选择tls1.2_ticket_auth，具体插件的介绍如下参考资料中
   "443":{"protocol":"auth_chain_a", "password":"123456", "obfs":"tls1.2_ticket_auth", "obfs_param":""},
  #注意无论怎么变化，最后一个端口设置，不带逗号！
   "3389":{"protocol":"auth_aes128_md5", "password":"123456", "obfs":"tls1.2_ticket_auth", "obfs_param":""}#此处没有逗号！
  },
  "timeout":400,
  #默认全局的加密方式，即上边各个端口的默认加密方式。一般为aes-256-cfb，		此处，选择为chacha20，移动设备性能较好。
  "method":"chacha20",
  #protocol.协议定义插件的默认值，origin即使用原版SS协议，不混淆。即上面端口配置中，你没有设置 protocol 和 obfs 情况下，使用的默认值。
  "protocol": "origin",
  "protocol_param": "",
   #protocol.协议定义插件的默认值，plain即使用原协议，不混淆。
  "obfs": "plain",
  "obfs_param": "",
  "redirect": "",
  "dns_ipv6": true,
 #TCP FAST OPEN ，打开
  "fast_open": true,
 "workers": 1
}

```

# 启动
- /etc/init.d/shadowsocks restart
- ssserver -c /etc/shadowsocks.json -d start


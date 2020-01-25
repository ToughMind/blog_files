---
title: "浏览器输入url发生了什么"
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
# [基本知识点]
### 1、总体而言
- DNS解析
- TCP连接
- 发送HTTP请求
- 服务器处理请求并返回HTTP报文
- 浏览器解析渲染页面
- 连接结束

### 2.1、DNS解析
- 实际就是寻找哪台机器有你需要资源的过程。
- 网址到ip地址的转换
- 解析过程：是个递归查询的过程
  - 1、在本地域名服务器查询ip
  - 2、根域名服务器查询(.)
    - www.google.com实际上省略了最后的.，.是根域名服务器
  - 3、向com顶级服务器查询
  - 4、向下递推
- 总结：. -> com. -> google.com. -> www.google.com.

### 2.2、DNS优化
- DNS缓存
  - 浏览器缓存，系统缓存，路由器缓存，IPS服务器缓存，根域名服务器缓存，顶级域名服务器缓存，主域名服务器缓存
    - 在chrome浏览器中输入:chrome://dns/，可以看到chrome浏览器的DNS缓存。
    - 系统缓存主要存在/etc/hosts(Linux系统)中
- DNS负载均衡（DNS重定向）
  - 哪台机器处理用户请求并不重要
  - DNS可以返回一个合适的机器的IP给用户，比如根据机器负载量、地理位置距离等
  - DNS服务器会返回IP地址，CDN节点的服务器负责响应用户的请求
  
### 3.1、TCP连接
- HTTP协议是使用TCP作为其传输层协议的

### 3.2、HTTPS协议443
- HTTPS协议的本质就是HTTP + SSL(or TLS)
- 在HTTP报文进入TCP报文之前，先使用SSL对HTTP报文进行加密
- HTTPS过程
  - HTTPS在传输数据之前需要客户端与服务器进行一个握手(TLS/SSL握手)
  - TLS/SSL使用了非对称加密，对称加密以及hash

### 3.3、HTTP请求
- HTTP请求报文是由三部分组成: 请求行, 请求报头和请求正文
- 请求行
  - `GET index.html HTTP/1.1`、
- 请求报头
  - 附加信息和客户端自身的信息
  - Accept, Accept-Charset, Accept-Encoding, Accept-Language, Content-Type, Authorization, Cookie, User-Agent等。
- 请求正文
  - 当使用POST, PUT等方法时，通常需要客户端向服务器传递数据
  - 在请求报头中有一些与请求正文相关的信息
  - 现在的Web应用通常采用Rest架构，请求的数据格式一般为json。这时就需要设置Content-Type: application/json
  
### 3.4、服务器处理请求并返回HTTP报文
- HTTP响应报文也是由三部分组成: 状态码, 响应报头和响应报文
- 状态码
  - 1xx：指示信息–表示请求已接收，继续处理。
  - 2xx：成功–表示请求已被成功接收、理解、接受。
  - 3xx：重定向–要完成请求必须进行更进一步的操作。
  - 4xx：客户端错误–请求有语法错误或请求无法实现。
  - 5xx：服务器端错误–服务器未能实现合法的请求。
- 响应报头
  - 常见的响应报头字段有: Server, Connection
- 响应报文
  - 服务器返回给浏览器的文本信息，通常HTML, CSS, JS, 图片等文件就放在这一部分
  
### 3.5、浏览器解析渲染页面
- 浏览器是一个边解析边渲染的过程
- 涉及到两个概念: reflow(回流)和repain(重绘)
- 页面在首次加载时必然会经历reflow和repain
- JS的解析是由浏览器中的JS解析引擎完成的
  - JS是单线程运行，也就是说，在同一个时间内只能做一件事，所有的任务都需要排队
  
### 4、web优化
- 合理使用缓存
- 考虑缩短连接时间，即DNS优化部分
- 减少响应内容大小，即对内容进行压缩

# [高级讲解](http://fex.baidu.com/blog/2014/05/what-happen/)
---
title: "Servlet及Tomcat学习"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["java"]
categories: ["java"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 2016.8.11
- ContextConfig 的 init 方法将会主要完成以下工作：
  - 创建用于解析 xml 配置文件的 contextDigester 对象
  - 读取默认 context.xml 配置文件，如果存在解析
  - 读取默认 Host 配置文件，如果存在解析它
  - 读取默认 Context 自身的配置文件，如果存在解析它
  - 设置 Context 的 DocBase
  
- ContextConfig 的 init 方法完成后，Context 容器的会执行 startInternal 方法
  - 创建读取资源文件的对象
  - 创建 ClassLoader 对象
  - 设置应用的工作目录
  - 启动相关的辅助类如：logger、realm、resources 等
  - 修改启动状态，通知感兴趣的观察者（Web 应用的配置）
  - 子容器的初始化
  - 获取 ServletContext 并设置必要的参数
  - 初始化“load on startup”的 Servlet
  
- 为什么要将 Servlet 包装成 StandardWrapper 而不直接是 Servlet 对象?
  - 这里 StandardWrapper 是 Tomcat 容器中的一部分，它具有容器的特征，而 Servlet 为了一个独立的 web 开发标准，不应该强耦合在 Tomcat 中 
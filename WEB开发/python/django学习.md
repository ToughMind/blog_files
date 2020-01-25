---
title: "django学习"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["python","django"]
categories: ["python"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
### 入门

- 创建一个项目
  - `django-admin startproject XXX`

- 数据库的建立
  - `python manage.py migrate`

- 启动服务器
  - `python manage.py runserver`
  - `python manage.py runserver 0.0.0.0:8000`    

- 创建模型
  - `python manage.py startapp XXX`
  
- 激活模型
  - `python manage.py makemigrations polls`
  - `python manage.py sqlmigrate polls 0001`

- 数据库中创建表
  - `python manage.py migrate`
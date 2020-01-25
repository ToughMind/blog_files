---
title: "mybatis学习笔记"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["mybatis"]
categories: ["mybatis"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 2016.7.19
### SqlSession的作用
- 向SQL语句传入参数
- 执行SQL语句
- 获取执行SQL语句的结果
- 事务的控制
  
## 2016.7.21
- jdbcType
  - 数据表支持的类型列表。这个属性只在insert,update或delete的时候针对允许空的列有用。JDBC需要这项，但MyBatis不需要。如果你是直接针对JDBC编码，且有允许空的列，而你要指定这项。

- if + where 的条件判断
  - “where”标签会知道如果它包含的标签中有返回值的话，它就插入一个‘where’。此外，如果标签返回的内容是以AND或OR 开头的，则它会剔除掉
  - 同理的有if+set
  
## 2016.07.24
- mybatis语句中，$amp;代表$，$quot代表"。

## 2016.08.10
- mybatis的#{}有预编译，但${}没有，是直接替换掉的。order by XX的时候应该就用${}了吧，因为总不能让它预编译成order by ？的形式吧。

## 2016.08.12
- mybatis接口编程方式
  - IUser.query() 接口并没有实现类？
  - MapperProxy.invoke()==sqlSession.select()？
  - IUser i = Proxy.newProxyInstance()？ 
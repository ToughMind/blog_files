---
title: "Intellij配置"
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

# intellij配置的文件保存路径
项目 | 说明
---|---
/Users/你的用户名/Library/Application Support/IntelliJIdeaXXXXX | 用于保存安装的插件
/Users/你的用户名/Library/Caches/IntelliJIdeaXXXXXX | 用于保存缓存、日志、以及本地的版本控制信息（local history 这个功能）
/Users/你的用户名/Library/Preferences/IntelliJIdeaXXXXXX | 用于保存你的个人配置，等价于 Windows 下的 config 目录

# 模板相关

## Code Templates

### File Header
```
/** @(#)${NAME}.JAVA, ${YEAR}年${MONTH}月${DAY}日.
 * 
 * Copyright ${YEAR} Netease, Inc. All rights reserved.
 * NETEASE PROPRIETARY/CONFIDENTIAL. Use is subject to license terms.
 */
```

### Files
```
/**
 * ${DESC}
 * 
 * @author 刘泉 ${YEAR}年${MONTH}月${DAY}日 ${TIME}
 */
``` 

### Live Templates

- at，作者信息，主要作用备注里
  - date("yyyy年MM月dd日 HH:mm")
```
@author 刘泉 $date$
```

- mc，简化的方法注释模板
```
/**   
 * 
 * 
 * @author 刘泉 $date$
 */
```
 
- main
```
public static void main(String[] args){
$END$
}
```
 
- de，debug日志模板
  - methodName()
  - groovyScript("_1.collect { it + '={}' }.join(', ')", methodParameters())
  - groovyScript("_1.collect { it }.join(', ')", methodParameters())
```
if (logger.isDebugEnabled()) {
    logger.debug(" ===> [op: $method$] $END$: $params$",$result$);
}
```
 
# 实用插件

- Eclipse Code Formatter

- Vim Emulation

- Ace Jump
  - emacsIDEAs
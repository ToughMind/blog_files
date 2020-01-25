---
title: "《跟我学spirng3》笔记"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["spring"]
categories: ["spring"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
## 2016.10.28
### Bean的基本配置
- Bean中使用id和name是不能重复的。
- （可能，并未验证成功）id可以利用xml解析器验证引用id是否存在，而name是在使用的时候才能发现

- 若xml配置中有多个相同类型的bean，则获取不能用getBean(Type)的方法，应该用getBeansOfType(Type)。
  - 注意，key是bean的id或者name值，id优先级更大。若没有写name则key值是类名#数字，如lq.test.Controller#0。
### IOC概念
- 谁控制谁？
  - IOC容器控制了对象
- 控制什么？
  - 控制了外部资源获取
- 为何是反转？
  - 容器帮忙查找和注入对象，对象是被动的接受依赖对象
 
- IOC容器就是具有DI功能的容器，负责实例化、定位、配置对象建立依赖关系。
- 在Spring中BeanFactory是IOC容器的实际代表。
  
### DI概念
- 谁依赖谁？
  - 应用程序依赖IOC
- 为什么需要依赖？
  - 应用程序需要外部资源
- 谁注入谁
  - IOC注入应用程序某个对象
- 注入了什么
  - 某个对象需要的外部资源（包括对象、资源、常量数据）
  
### 读取xml配置文件的方式
- BeanFactory实现
```
Resource resource = new FileSystemResource(new file("classpath.xml"));
BeanFactory beanFactory = new XmlBeanFactory(resource)
```
```
Resource resource = new ClassPathResource("classpath.xml");
BeanFactory beanFactory = new XmlBeanFactory(resource);
```

- ApplicationContext实现
  - ClassPathXmlApplicationContext
  ```
  BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath.xml");
  ```
  
  - FileSystemXmlApplicationContext
  ```
  BeanFactory beanFactory = new FileSystemXmlApplicationContext("fileSystemConfig.xml")
  ```
  
## 2016.10.29
### 类与类之间的关系
- 泛化：类与类、接口与接口的继承关系
- 实现：类对接口的实现
- 依赖：类与类之间有使用关系，是一种“相识关系”，只在某个特定地方（如方法）才有关系
- 关联：类与类或类与接口的依赖关系，表现“拥有关系”
- 聚合：关联的特殊情况，部分-整体关系；整体和部分可以有不同生命周期；弱拥有关系，弱关联
- 组合：关联的特殊情况，部分-整体关系；整体和部分是相同的生命周期；弱拥有关系，弱关联

### bean的注入
- 构造器
  - 常量值：`<constructor-arg index="0" value="常量"/>`
  - 引用：`<constructor-arg index="0" ref="引用"/>`
  
- setter注入
  - 常量值：`<property name="message" value="常量"/>`
  - 引用：`<property name="message" ref="引用"/>`
  - 字典：`<map><entry key="键常量" value="值常量"/><entry key-ref="键引用" value-ref="值引用"/></map>`
  
- p命名空间简化setter注入
  - 首先指定p命名空间:`xmlns:p="http://www.springframework.org/schema/p"`
  - `<bean id="……" class="……" p:id="value"/>`等价于`<property name="id" value="value"/>`
  
### 循环依赖问题
- 构造器的循环依赖是无法解决的。
- setter注入的循环依赖：可通过spring容器提前暴露刚完成构造器注入，但未完成其他步骤（setter）的bean完成
  - 只能解决单例域
  - 对于prototype域，无法解决，因为spring容器不缓存prototype域的bean
  - 对于“singleton”作用域Bean，可以通过“setAllowCircularReferences(false);”来禁用循环引用
  
### 自动装配相关
- default-autowire-candidates属性指定的匹配模式，如“dao、service”。被设为false，则该Bean将不会作为依赖注入的候选者。

- 集合类型不同
  - 根据泛型获取匹配的所有候选者并注入到数组或集合，如List<A>，将选择所有类型A的bean注入到list中。而对于一个具体类型的集合，如ArrayList<A>，则选择一个类型为ArrayList的bean注入。
  
- 类型：default、no、byName、byType、constructor、autodetect

- 依赖检查四类型：
  - none：默认不检查
  - objects：检查除基本类型外的依赖对象
  - simple：对基本类型进行依赖检查，包括数组类型
  - all：对所以类型进行依赖检查
  
## 2016.11.1
### 读取资源
- ByteArrayResource，代表byte[]数组资源
- InputStreamResource，代表java.io.InputStream字节流
- FileSystemResource，代表java.io.File资源
- ClassPathResource，代表classpath路径的资源
- UrlResource，代表URL资源
- ServletContextResource，代表web应用资源
- VfsResource，代表Jboss虚拟文件系统资源

## 2016.11.3
### 动态代理区别
- jdk：提取目标对象的接口。
- cglib：对类进行代理。通过子类创建代理。
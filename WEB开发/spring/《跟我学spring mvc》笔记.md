---
title: "《跟我学spring mvc》笔记"
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
### mvc各模块
- Front Controller：为表现层提供统一访问点，将选择具体视图和功能处理分离。
  - DispatcherServlet

- Application Controller(策略模式)：选择具体视图技术和功能处理。
  - Handler Mapping和View Resolver

- Page Controller(命令模式)：封装参数，转调业务对象处理模型，返回视图名给前端控制器（和具体视图技术解耦）。

- Context（ThreadLocal模式）：相关数据可放置在上下文

### 关键词
- 基于java，实现mvc模式，请求驱动，轻量级，web框架，约定大于配置（惯例优先原则），契约式编程

### 强大的典型特点
- web层干净、简单
- 与spring框架天生集成
- 容易web层单元测试
- url到page controller映射灵活
- 灵活数据验证、格式化、绑定，本地化、主题解析
- 强大jsp标签库
- 简单异常处理
- 静态资源支持
- restful风格

### 各种注解
- @Controller：用于标识是处理器类；
- @RequestMapping：请求到处理器功能方法的映射规则；
- @RequestParam：请求参数到处理器功能处理方法的方法参数上的绑定；
- @ModelAttribute：请求参数到命令对象的绑定；
- @SessionAttributes：用于声明session级别存储的属性，放置在处理器类上，通常列出模型属性（如@ModelAttribute）对应的名称，则这些属性会透明的保存到session中；
- @InitBinder：自定义数据绑定注册支持，用于将请求参数转换到命令对象属性的对应类型；
- @CookieValue：cookie数据到处理器功能处理方法的方法参数上的绑定；
- @RequestHeader：请求头（header）数据到处理器功能处理方法的方法参数上的绑定；
- @RequestBody：请求的body体的绑定（通过HttpMessageConverter进行类型转换）；
- @ResponseBody：处理器功能处理方法的返回值作为响应体（通过HttpMessageConverter进行类型转换）；
- @ResponseStatus：定义处理器功能处理方法/异常处理器返回的状态码和原因；
- @ExceptionHandler：注解式声明异常处理器；
- @PathVariable：请求URI中的模板变量部分到处理器功能处理方法的方法参数上的绑定，从而支持RESTful架构风格
的URI；

### spring mvc配置
- contextConfigLocation：表示用于加载Bean的配置文件；

- contextClass：表示用于加载Bean的ApplicationContext实现类，默认WebApplicationContext。

- 创建完毕后会将该上下文放在ServletContext。

### DispatcherServlet初始化顺序
- 总结了而言，1、初始化spring web mvc使用的web上下文 2、初始化DispatcherServlet使用的策略，如mapper、adapter

- 继承体系结构
  - HttpServlet <- HttpServletBean <- FrameworkServlet <- DispatcherServlet
  
- HtppServletBean的init方法：将（init-param）设置到该组件上（如contextAttribute,contextClass,contextConfigLocation）

- FrameworkServlet的initServletBean()方法，初始化web上下文。

- DispatcherServlet的onRefresh()方法,会进行我们需要的Web层Bean的配置，如HandlerMapping、
HandlerAdapter等，而且如果我们没有配置，还会给我们提供默认的配置

### DispatcherServlet默认配置
- 默认配置在DispatcherServlet.properties（和DispatcherServlet类在一个包下）

### Spring2风格的控制器（3.0开始已经不推荐使用了）
![image](http://sishuok.com/forum/upload/2012/7/26/c6052a24f0e0a212529755716a56a2b6__1.JPG)
- Controller接口：只有一个方法handleRequest，返回
ModelAndView

- WebContentGenerator：浏览器缓存、session开启、请求方法类型等

- AbstractController：是否在执行时同步session（会话的串行化访问功能）

- ServletForwardingController：将接收到的请求转发到一个命名的servlet

- BaseCommandController：请求参数绑定到一个命令对象，提供Validator注册功能

- AbstractCommandController：自动封装请求参数到一个命令对象，而且提供了验证功能

- AbstractFormController：带步骤的表单提交基类，表单的渲染和提交表单处理

- SimpleFormController：两步表单支持

- CancellableFormController：可取消的表单控制器

- AbstractWizardFormController：向导控制器类提供了多步骤（向导）表单的支持

- ParameterizableViewController：静态视图

- AbstractUrlViewController：直接根据URL计算出逻辑视图
名

- UrlFilenameViewController：将请求的URL路径转换为逻辑视图名并返回

- MultiActionController：将不同的请求映射不同的请求的功能处理方法

### 数据类型转换和验证
- createBinder(request, command)
- initBinder(request, binder)
- binder.bind(request)
- ongBind(request, command, errors)
- validators
- onBindAndValidate(request, command, errors)
- errors(BindException类型)

## 2016.10.13
### http请求主要包含6部分信息
- 请求方法
- URL
- 协议及版本
- 请求头，包括cookie
- 回车换行，CRLF
- 请求内容区

## 2016.10.21
### 常见的媒体类型格式
- text/html、text/plain、text/xml、image/gif、image/jpeg、image/png、application/x-www-form-urlencoded、multipart/form-data、application/xhtml+xml、application/pdf等

### spring各阶段
- 2.5版本之前，通过实现Controller接口定义处理器类，已经@Deprecated
- 2.5版本之后，利用注解，需要处理映射DefaultAnnotationHandlerMapping和处理适配器AnnotationMethodHandlerAdapter
- 3.1版本后，全新处理器，RequestMappingHandlerMapping和RequestMappingHandlerAdapter

### 类和方法上使用requestMapper标签时
- 只有生产者/消费者 模式 是覆盖，其他的使用方法是继承，如headers、params等都是继承

### 对contentType和Accept自动化
- Spring提供了一组注解（@RequestBody、@ResponseBody）和一组转换类（HttpMessageConverter）

### 方法支持的自动传递的参数
- ServletRequest/HttpServletRequest 和 ServletResponse/HttpServletResponse

- InputStream/OutputStream 和 Reader/Writer
  - 等价于request.getInputStream()等
  
- WebRequest/NativeWebRequest

- HttpSession

- 命令/表单对象

- Model、Map、ModelMap：此三者是同一个对象

- Errors/BindingResult

- 其他杂项：Locale，Principal等

### 形参赋值问题
- 赋值主要是看@RequestParam的value，而不是形参名。
  - 原子类型，必须有值，否则异常。但用boolean的时候默认false。
  - 所有引用类型中，Boolean默认为FALSE，其余都是null。
- 若对象里的属性和形参名相同，则两处都会赋值

- 形参若是List，一定要加上@requestParam，因为List是一个接口。

- 注意接受数组形式的时候很奇怪，比如String[]，传入s=a,b,c,d则是传入了4个；传入s=a,b&s=c,d则是传入了两个。

### 包含@SessionAttributes的执行流程
- 根据@SessionAttributes将对象放入到模型数据中
- 执行@ModelAttribute注解的方法：若模型中包含同名数据，则不执行，而是使用上步骤的回话数据；反之，则执行。
- 执行@RequestMapping方法：是否有@ModelAttribute注解
的同名对象，如果有直接使用，否则通过反射创建一个
  - 如果有@SessionAttributes注解，则此处一定是从模型中拿，否则报错
  
- `mvc:annotation-driven`会自动注册
```
<bean id="validator"
class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
<property name="providerClass" value="org.hibernate.validator.HibernateValidator"/>
<!-- 如果不加默认到 使用classpath下的 ValidationMessages.properties -->
<property name="validationMessageSource" ref="messageSource"/>
</bean>
```
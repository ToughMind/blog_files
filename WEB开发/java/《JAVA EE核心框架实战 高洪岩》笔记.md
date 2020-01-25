---
title: "《JAVA EE核心框架实战 高洪岩》笔记"
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
# 2016.7.18
- 为什么要使用mybatis类似的框架呢？
  - 在传统的jdbc代码时，需要编写DAO层代码将数据库表中的数据封装到自定义的实体bean中。而mybatis和hibernate类似的框架则已经封装好了。
  
- mybatis是个持久层框架，是一种半自动化的ORM映射框架。核心是SqlSessionFactory对象，由SqlSessionFactoryBuilder类创建。

# 2016.8.3
- SqlSessionFactoryBuilder创建了SqlSessionFactory后就不需要存在了，也就是不需要保持此对象的状态。SqlSessionFactory对象应在应用程序执行期间都存在，最佳方式就是写单例。而SqlSession每个线程都应该有自己的实例，决不能将SqlSession实例对象放在静态变量、实例字段即任何类型的管理范围中。

- 处理null值时可以用jdbcType指定方式，也可以用<if>标签进行判断。

- 编写动态sql时，对连接%和字段的语句可以用CONCAT('%',#{companyName},'%') 
  - 书中的写法是 '%'||#{companyName}||'%'
  - 视频讲解中直接用 '%' #{companyName} '%'
  
- MVC模型开发所必须的知识
  - 解析并创建XML文件：在struts2中利用dom4j可解析和创建xml文件
  - java反射技术：对对象的内容进行动态访问
  - 实现MVC模型，自定义配置文件，然后根据配置文件设计封装类
  
- Servlet中，<init-param>标签仅当前servlet对象可以获取，而<context-param>中的参数所有的servlet都可以获取 
   
## 2016.8.5
- 使用struts2标签可以显示html代码
```
<%@ taglib uri="/struts-tags" prefix="s" %>
```

- 要想去掉struts2标签自动生成的html代码，只需在配置文件中加入
```
<constant name="struts.ui.theme" value="simple"></constant>
```
- 用<s:debug>标签可以打印出调试信息，看到ValueStack发现addFiledError是使用Map进行存储，而addActionError是使用ArrayList进行存储。

- 在action中有多个业务方法时的处理
  - 通过url叹号“！”参数
  - 在action标记中加入method属性
  
- 在action中使用servlet的api
  - ServletActionContext.getRequest()
  - ActionContext.getContext().get("request")
  - ActionContext.getContext().put("","")
  
- WebService是一种基于HTTP协议的交互数据技术，使用的是SOAP协议，实现协议的语言叫做wsdl。其实wsdl就是一种xml格式的语言。

## 2016.8.6
- js语言主要目的是为web浏览器提供一种页面脚本语言，用来控制web浏览器中的DOM对象。

- 特殊字符，"""、"\"、"/"、"b"、"f"、"n"、"r"、"t"

- 前端可以使用json.org网站提供的json2.js框架文件将js对象转换成json字符串。

- spring MVC 知识点
  - 从控制层重定向到控制层
  - 匹配URL路径，用/user/{userId}的形式
  - 将url中的参数转成实体的示例
  - 服务器端获取json字符串并解析，利用@RequestBody标签
  - 服务器端响应json对象，利用@ResponseBody标签。可以在RequestMapping标签设置consumes="application/json"限制前台传递过来的数据格式必须是JSON，设置produces="application/json"限制返回的数据要转成JSON格式
  - 利用@SessionAttributes标签操作HttpSession对象，很类似memcache缓存
  
- spring mvc常用
  - 路径中添加通配符的功能
  - service层在controller层注入，有多个实现类，利用Resource标签指定某一个
  - 控制层返回void数据的情况：若用到了PrintWriter，则不会到默认jsp文件；否则就跳转到了默认jsp页面
    - 使用默认的jsp
    - 通过HttpServletResponse输出
    
## 2016.8.8
- hibernate知识点
  - 不要手动修改OID，会造成缓存中数据不对
  - 如果使用延迟加载，session.close()后，将list中的对象使用方法：Hibernate.initialize（代理对象参数）将list中的代理类进行实例化。
  - （联系延迟加载而言）load和get，load方法根据id 如果找不到实体类就返回一个代理实体类，而get返回null。
  
- spring4
  - BeanFactory使用bean是延迟取得，第一次使用时才创建指定类的对象，；而ApplicationContext接口取得Bean的实例是在自身容器被初始化时就创建全部类对象。
  - 作用域：Singleton、Prototype、request、session、globalSession。
  - AOP类似于增强，但区别于方法中增强，因为AOP通过代理原理，在代理类中进行了增强处理。代理分为静态代理和动态代理。静态代理是面向接口，动态代理是面向实现类。
  - 静态代理和动态代理都是对方法进行增强，而spirng 页支持方法的advice增强。
  

## 2016.8.9
- Session的运行机制主要通过Cookie对象，当第一次访问服务器时在服务器生成一个MAP对象，此map的key值就是一个唯一的sessionId。返回的数据带有符合HTTP协议的Cookie数据，IE能根据数据中的格式创建一个Cookie。第二次访问的时候就会携带刚刚创建的Cookie，其值sessionId。

## 2016.8.10
- EJB3主要的场合就是在数据分布情况下，对事务支持并考虑多数据源，也就是跨容器的自动全局性事务。                                                         
- ejb分为远程调用和本地调用。个人觉得本地调用很类似于maven依赖包，而所需要的方法在jar包中。ejb项目和web项目在一个企业级项目中。而远程调用相当于一个项目在weblogic中，另一个项目在tomcat中。
  - 不过远程调用用到了JNDI技术
  
```
Properties props = new Properties();	props.setProperty(Context.INITIAL_CONTEXT_FACTORY,"weblogic.jndi.WLInitialContextFactory");
props.setProperty(Context.PROVIDER_URL, "t3://localhost:7001");
props.setProperty(Context.SECURITY_PRINCIPAL, "weblogic");
props.setProperty(Context.SECURITY_CREDENTIALS, "weblogic123");

Context context = new InitialContext(props);
SayHelloRemoteTypeRemote sayHelloRemoteTypeRemoteRef = (SayHelloRemoteTypeRemote) context.lookup("sayHelloRemoteTypeJNDI#publicinterface.SayHelloRemoteTypeRemote");
```
- @PostConstruct执行，只一次，然后无状态bean进入method-ready pool，准备供外使用。当空闲bean太多或者bean占用大量系统资源时，系统调用@PreDestroy方法，只一次，然后bean变到does not exist状态。很类似于serlvet。
- 可以在无状态bean中使用实例变量，但尽量不要去改变它的值。因为weblogic容器有自己的连接池，类似于缓存，并不是每次都重新创建一个对象。若想要共享数据，在bean中的方法以传递参数的形式进行共享数据。
- 从从实例池取出的数据都是代理对象。

- 有状态bean存在HttpSession中作为一个副本，那么把远程或本地的有状态bean存储到HttpSession中和把普通Service类存储到HttpSession对象中有什么区别呢？
  - 如果使用EJB，可以将业务组件以服务提供者的身份供其他组件调用，而不需要将源代码发布出去。
  
- 如果查找本地的有状态bean，可以在web.xml文件中注册引用，使用<ejb-local-ref>标签。

- 对于有状态bean，若访问量大，服务器很快就溢出。ejb标准可对其实行钝化和激活策略。钝化就是把不常使用的bean序列化到硬盘中，激活就是若业务需要，将刚钝化的bean反序列化到内存。
  - 若要用钝化，则要声明序列化接口Serializable。
  - 实体bean必须实现Serializable接口，并且要有一个参数为空的构造函数。 

- EJB3中实体状态
  - 瞬时态：新建对象，在内存中
  - 托管态：已分配id，由EntityManager对象的上下文接管
  - 游离态：上下文已经关闭
  - 删除状态：与EntityManager对象上下文关联，并要删除对应的数据。
  
- EJB3中的事务
  - 本地资源事务，使用JDBC中的连接来管理当前活动的事务对象
  - JTA事务，也叫容器事务，自动管理事务生命周期并支持分布式的JTA参与。
  
---
title: "《跟开涛学Spring MVC》笔记"
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
# 2016.7.19
- 比较MVC框架的性能
  - SpringMvc使用简单，学习成本低；灵活；性能高。
  
- SpringMvc核心类和接口
  - DispatcherServlet 前置控制器
  
  - HandlerMapping接口 处理请求的映射
    - SimpleUrlHandlerMapping  通过配置文件，把一个URL映射到Controller
    - DefaultAnnotationHandlerMapping  通过注解，把一个URL映射到Controller类上

  - HandlerAdapter接口 处理请求的映射
    - AnnotationMethodHandlerAdapter类，通过注解，把一个URL映射到Controller类的方法上
  
  - Controller接口 由于我们使用了@Controller注解，添加了@Controller注解注解的类就可以担任控制器（Action）的职责,所以我们并没有用到这个接口。
  
  - HandlerInterceptor接口 拦截器
  
  - ViewResolver接口
    - UrlBasedViewResolver类 通过配置文件，把一个视图名交给到一个View来处理
    - InternalResourceViewResolver类，比上面的类，加入了JSTL的支持
    
  - View接口
  
  - LocalResolver接口
  
  - HandlerExceptionResolver接口 异常处理
    - SimpleMappingExceptionResolver实现类
    
# 2016.7.20

- DispatcherServlet是前置控制器，配置在web.xml文件中。

- 每一个DispatcherServlet有自己的WebApplicationContext上下文对象。

- 在DispatcherServlet初始化过程中，默认的，框架会在web应用的web-inf文件夹下寻找名为servlet.xml的配置文件，生成文件中定义的bean。

  - 如果用了监听器，spring会创建WebApplicationContext上下文，保存在ServletContext中，称为父容器key是WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE。
  - 每个 DispatcherServlet有一个自己的WebApplicationContext，称为子容器，也保存在ServletContext中，key是org.springframework.web.servlet.FrameworkServlet.CONTEXT+Servlet。子容器可以访问父容器，反过来不行。
  - 当有一个Request产生时，会把这个WebApplicationContext保存在Request对象中，key是DispatcherServlet.class.getName() + ".CONTEXT"。
- <mvc:annotation-driven />会自动注册DefaultAnnotationHandlerMapping与AnnotationMethodHandlerAdapter 两个bean。
- <mvc:default-servlet-handler/> 使用默认的Servlet来响应静态文件。
- 如果用REST风格拦截所有请求，如何对静态资源访问?
  - 用tomcat的defaultServlet来处理，就是配置servletMapping；要写在DispatcherServlet前面让defaultServlet先拦截请求。
  - 在spring3.0.4以后版本提供了mvc:resources
  - 使用<mvc:default-servlet-handler/>

- 在spring MVC的配置文件中配置有三种方法
  - （近似）总拦截器，拦截所有url
    ```
    <mvc:interceptors>
    <bean class="com.app.mvc.MyInteceptor" />
    </mvc:interceptors>
    ```
  - （近似） 总拦截器，拦截匹配的URL。

    ```
    <mvc:interceptors >
    <mvc:interceptor>
    <mvc:mapping path="/user/*" /> <!-- /user/* -->
    <bean class="com.mvc.MyInteceptor"></bean>
    </mvc:interceptor>
    </mvc:interceptors>
    ```
  - HandlerMappint上的拦截器。如果是REST风格的URL，静态资源就不会被拦截。因为我们精准的注入了拦截器
    ```
    <bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping">
    <property name="interceptors">
    <list>
    <bean class="com.mvc.MyInteceptor"></bean>
    </list>
    </property>
    </bean>
    ```
  - 如果使用了<mvc:annotation-driven />，它会自动注册DefaultAnnotationHandlerMapping与AnnotationMethodHandlerAdapter这两个bean,所以就没有机会再给它注入interceptors属性，就无法指定拦截器。

- 如何获取spring管理的bean
  - servlet方式加载时：

    ```
    ServletContext sc=略  
    WebApplicationContext attr = (WebApplicationContext)sc.getAttribute("org.springframework.web.servlet.FrameworkServlet.CONTEXT.springMVC");
    ```
  - listener方式加载时

    ```
    ServletContext context = getServletContext();  
    WebApplicationContext applicationContext  = WebApplicationContextUtils .getWebApplicationContext(context);
    ```
  - 通用的方法，在配置文件中加入：

    ```
    <!-- 用于持有ApplicationContext,可以使用SpringContextHolder.getBean('xxxx')的静态方法得到spring bean对象 -->  
    <bean class="com.xxxxx.SpringContextHolder" lazy-init="false" />
    ```
- <mvc:annotation-driven /> 到底做了什么工作

    ```
    <!-- 注解请求映射  -->  
    <bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping">          
        <property name="interceptors">  
            <list>    
                <ref bean="logNDCInteceptor"/>   <!-- 日志拦截器，这是你自定义的拦截器 -->  
                <ref bean="myRequestHelperInteceptor"/>   <!-- RequestHelper拦截器，这是你自定义的拦截器-->   
                <ref bean="myPermissionsInteceptor"/>  <!-- 权限拦截器，这是你自定义的拦截器-->   
                <ref bean="myUserInfoInteceptor"/>  <!-- 用户信息拦截器，这是你自定义的拦截器-->   
            </list>          
        </property>          
    </bean>     
    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">  
        <property name="messageConverters">    
            <list>    
                <ref bean="byteArray_hmc" />    
                <ref bean="string_hmc" />    
                <ref bean="resource_hmc" />    
                <ref bean="source_hmc" />    
                <ref bean="xmlAwareForm_hmc" />    
                <ref bean="jaxb2RootElement_hmc" />    
                <ref bean="jackson_hmc" />    
            </list>    
        </property>    
    </bean>    
    <bean id="byteArray_hmc" class="org.springframework.http.converter.ByteArrayHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="string_hmc" class="org.springframework.http.converter.StringHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="resource_hmc" class="org.springframework.http.converter.ResourceHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="source_hmc" class="org.springframework.http.converter.xml.SourceHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="xmlAwareForm_hmc" class="org.springframework.http.converter.xml.XmlAwareFormHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="jaxb2RootElement_hmc" class="org.springframework.http.converter.xml.Jaxb2RootElementHttpMessageConverter" /><!-- 处理.. -->  
    <bean id="jackson_hmc" class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter" /><!-- 处理json-->
    ```

## 2016.8.11
- 核心架构的具体流程步骤如下：
  - 1、 首先用户发送请求——>DispatcherServlet，前端控制器收到请求后自己不进行处理，而是委托给其他的解析器进行处理，作为统一访问点，进行全局的流程控制；
  - 2、 DispatcherServlet——>HandlerMapping，HandlerMapping将会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象、多个HandlerInterceptor拦截器）对象，通过这种策略模式，很容易添加新的映射策略；
  - 3、  DispatcherServlet——>HandlerAdapter，HandlerAdapter将会把处理器包装为适配器，从而支持多种类型的处理器，即适配器设计模式的应用，从而很容易支持很多类型的处理器；
  - 4、  HandlerAdapter——>处理器功能处理方法的调用，HandlerAdapter将会根据适配的结果调用真正的处理器的功能处理方法，完成功能处理；并返回一个ModelAndView对象（包含模型数据、逻辑视图名）；
  - 5、  ModelAndView的逻辑视图名——> ViewResolver，ViewResolver将把逻辑视图名解析为具体的View，通过这种策略模式，很容易更换其他视图技术；
  - 6、  View——>渲染，View会根据传进来的Model模型数据进行渲染，此处的Model实际是一个Map数据结构，因此很容易支持其他视图技术；
  - 7、返回控制权给DispatcherServlet，由DispatcherServlet返回响应给用户，到此一个流程结束。

- spring mvc 优势
  - 1、清晰的角色划分：前端控制器（DispatcherServlet）、请求到处理器映射（HandlerMapping）、处理器适配器（HandlerAdapter）、视图解析器（ViewResolver）、处理器或页面控制器（Controller）、验证器（   Validator）、命令对象（Command  请求参数绑定到的对象就叫命令对象）、表单对象（Form Object 提供给表单展示和提交到的对象就叫表单对象）。
  - 2、分工明确，而且扩展点相当灵活，可以很容易扩展，虽然几乎不需要；
  - 3、由于命令对象就是一个POJO，无需继承框架特定API，可以使用命令对象直接作为业务对象；
  - 4、和Spring 其他框架无缝集成，是其它Web框架所不具备的；
  - 5、可适配，通过HandlerAdapter可以支持任意的类作为处理器；
  - 6、可定制性，HandlerMapping、ViewResolver等能够非常简单的定制；
  - 7、功能强大的数据验证、格式化、绑定机制；
  - 8、利用Spring提供的Mock对象能够非常简单的进行Web层单元测试；
  - 9、本地化、主题的解析的支持，使我们更容易进行国际化和主题的切换。
  - 10、强大的JSP标签库，使JSP编写更容易。
  - 还有比如RESTful风格的支持、简单的文件上传、约定大于配置的契约式编程支持、基于注解的零配置支持等等。
  
## 2016.8.13
### DispatcherServlet初始化顺序
- HttpServletBean继承HttpServlet，将Servlet初始化参数（init-param）设置到该组件上（如contextAttribute、contextClass、namespace、contextConfigLocation），通过BeanWrapper简化设值过程，提供给子类初始化扩展点，initServletBean()。

- FrameworkServlet继承HttpServletBean，初始化web上下文，提供给子类初始化扩展点。

- DispatcherServlet继承FrameworkServlet，DispatcherServlet启动时会进行我们需要的Web层Bean的配置，如HandlerMapping、HandlerAdapter等，而且如果我们没有配置，还会给我们提供默认的配置。
 - 初始化Spring Web MVC使用的Web上下文，并且可能指定父容器为（ContextLoaderListener加载了根上下文）
 - 初始化DispatcherServlet使用的策略，如HandlerMapping、HandlerAdapter等
 
### MVC中完整的C（包含控制逻辑+功能处理）由（DispatcherServlet + Controller）组成。
- Controller控制器
  - 收集、验证请求参数并绑定到命令对象
  - 将命令对象交给业务对象，由业务对象处理并返回模型数据
  - 返回ModelAndView
  
- DispatcherServlet
  - 负责将请求委托给控制器进行处理
  - 根据控制器返回的逻辑视图名选择具体的视图进行渲染（并把模型数据传入）
  
### Controller接口控制器
- Controller接口 
  - 用于进行请求的功能处理，处理完请求后返回ModelAndView
 
- WebContentGenerator
  - 用于提供如浏览器缓存控制、是否必须有session开启、支持的请求方法类型（GET、POST等）等
  
- AbstractController
  - 实现了Controller，并继承了WebContentGenerator
  
- ServletForwardingController
  
- BaseCommandController
  - 命令控制器通用基类，数据绑定，验证功能
  
- AbstractCommandController
  - 命令控制器之一，控制器能把自动封装请求参数到一个命令对象，而且提供了验证功能
  
- AbstractFormController
  - 用于支持带步骤的表单提交的命令控制器基类
  
- SimpleFormController
  - 提供了更好的两步表单支持：1、准备要展示的数据，并到表单展示页面；2、提交数据数据进行处理。  
  
- CancellableFormController
  - 一个可取消的表单控制器，继承SimpleFormController，额外提供取消表单功能。
  
- AbstractWizardFormController
  - 提供了多步骤（向导）表单的支持
  
- ParameterizableViewController
  - 参数化视图控制器，不进行功能处理（即静态视图）
  
- AbstractUrlViewController
  
- UrlFilenameViewController
  - 提供根据请求URL路径直接转化为逻辑视图名的支持基类，即不需要功能处理，直接根据URL计算出逻辑视图名，并选择具体视图进行展示 
  
- MultiActionController
  - 如AbstractCommandController、SimpleFormController等一般对应一个功能处理方法（如新增），如果我要实现比如最简单的用户增删改查（CRUD Create-Read-Update-Delete），那该怎么办呢
  
## 2016.8.15
### 拦截器
- 比如性能监控，记录拦截器preHandle的开始时间和afterCompletion结束时间。但拦截器是单例的，即线程不安全，如何记录时间呢？
  - 解决办法是使用ThreadLocal，它是线程绑定的变量，提供局部变量。
  - 在测试时得把此拦截器放在链的第一个，这样得到的时间比较准确。
  
- 如登录拦截器
  - 能使用servlet规范中的过滤器Filter实现的功能就用Filter实现，因为HandlerInteceptor只有在Spring Web MVC环境下才能使用，因此Filter是最通用的、最先应该使用的。
  
- DefaultAnnotationHandlerMapping（3.1版本前）或者RequestMappingHandlerMapping（3.1版本后）：针对类上拥有注解的。  AnnotationMethodHandlerAdapter（3.1版本前）或者RequestMappingHandlerAdapter（3.1版本后）方法上拥有注解的。

- 组合关系
  -  @RequestMapping的value、method、produces属性组合使用是**或**的关系
  -   @RequestMapping的params、headers属性组合使用是**且**的关系
  
- http相关
  - CharacterEncodingFilter这个过滤器设置了编码(UTF-8)。编码只能被指定一次，即如果客户端设置了编
  - 服务器端通过headers的Content-Type来声明可处理的媒体类型，客户端可以通过headers的Accept声明。
  - 在spring3.1中使用HandlerMapping和HandlerAdapter可支持此特性。
    - consumes={"application/json"}：consumes指定方法能消费的媒体类型，通过Content-Type来判断
    - produces = "application/json"：produces指定方法将产生json格式数据，通过Accept匹配
  - 实际项目使用Accept请求头是比较麻烦的，大多数开放平台如淘宝、腾讯使用如下两种方式：扩展名，参数。
  - @RequestParam原子类型：必须有值，否则抛出异常，如果允许空值请使用包装类代替。Boolean包装类型类型：默认Boolean.FALSE，其他引用类型默认为null。
  
- 注解方式的数据绑定
  - @RequestParam用于将请求参数区数据映射到功能处理方法的参数上
  - @PathVariable用于将请求URL中的模板变量映射到功能处理方法的参数上
  - @CookieValue用于将请求的Cookie数据映射到功能处理方法的参数上
  - @RequestHeader用于将请求的头信息区数据映射到功能处理方法的参数上
  - @ModelAttribute绑定请求参数到命令对象
  - @SessionAttributes绑定命令对象到session
  
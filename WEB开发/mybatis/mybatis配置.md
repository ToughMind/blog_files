---
title: "mybatis配置"
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
## 整合配置的坑
### 读取资源文件问题
- 一般在applicationContext.xml文件里写引入properties文件的代码
```
<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	<property name="locations">
		<list>
			<value>classpath:config/*.properties</value>
			<!--要是有多个配置文件，只需在这里继续添加即可 -->
		</list>
	</property>
</bean>
```

- 通常引入的多个属性文件，如果属性文件里有重名的，则只会读其中一个，个人觉得是最后读取的那个（覆盖）。所以在属性文件中尽量避免重名。

- 在没有spring的时候，要关联mybatis的xml的mapper文件需要配置mybatis-config.xml文件。引入spring后，通常写在application.xml中
```
<!-- 配置Mybatis的文件 ，mapperLocations配置**Mapper.xml文件位置，configLocation配置mybatis-config文件位置 -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="mapperLocations" value="classpath*:lq/justtest/mapper/**/*.xml" />
	//<property name="configLocation" value="classpath:mybatis-config.xml" />
</bean>
```

- 如果上述没有配置mapperLocations属性，则没有关联到mapper文件，有没有办法呢？若采用的是接口编程方式，声明了mapper文件对应的接口后，可以通过接口自动找到对应同名的mapper文件，这是源代码中自动做的。
  - 若采取了接口编程，下面的声明一定要，不然dao层调用mapper文件对应接口的时候，就会注入错误。 
  - 格外注意，若没有声明xml的mapper文件，只声明了接口位置，那么接口名和xml的mapper文件名要相同，不然报错。
```
<!-- 自动扫描了所有的XxxxMapper.xml对应的mapper接口文件，这样就不用一个一个手动配置Mpper的映射了，只要Mapper接口类和Mapper映射文件对应起来就可以了。 -->	
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="lq.justtest.mapper" />
</bean>
```
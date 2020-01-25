---
title: "sql数据库问题"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["mysql"]
categories: ["mysql"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
### 1、mysql的cpu飙到500%怎么处理？

- top查看哪个进程、哪个端口占用cpu最多
- show processeslist查看是否由于大量并发，锁引起的负载问题
- 查看man慢查询，执行时间最长的sql；explain分析是否走索引，sql优化
- 是否缓存失效引起，查看buffer命中率

### 2、InnoDB支持的四种事务隔离级别？

- 未提交读、提交读、 重复读、串行读
- 脏读：同一事务中，会读取到还未提交事务修改的数据。
- 非重复读：同一事务中，两个时刻读取同一行数据不一样（由于其他事务更新）。
- 幻读：同一事务中，同一个查询多次进行，每次结果不一样（由于其他事务提交插入）

### 3、数据库事务四个特性，ACID
- 原子性：组成一个事务的多个数据库操作是一个原子单位。
- 一致性：事务操作成功后数据库所处的状态和它的业务规则是一致的，比如银行转账
- 隔离性：并发数据操作时，不同的事务拥有各自的数据空间，互相不完全干扰
- 持久性：持久化到数据库中
![image](http://upload-images.jianshu.io/upload_images/971185-6c3a6fd6623ed3b3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、MySQL 事务传播级别

- PROPAGATION_REQUIRED：支持当前事务，若没有则新建
- PROPAGATION_SUPPORTS：支持当前事务，若没有，则无事务
- PROPAGATION_MANDATORY：支持当前事务，没有则抛异常
- PROPAGATION_REQUIRESNEW：新建事务，如果当前存在事务，把当前事务挂起。
- PROPAGATION_NOT_SUPPORTED：以非事务执行
- PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常
- PROPAGATION_NESTED：如果一个活动的事务存在，则运行在一个嵌套的事务中；如果没有活动事务, 则按REQUIRED属性执行

### 5、设置innodb事务级别的方法
- SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}
- set global transaction isolation level read committed

### 6、评测mysql
- explain format = json
- mysql workbench的visual explain
- MySQL的CBO和Optimizer Trace

### 7、mysql索引
- 索引分类
  - 普通索引：char和varchar长度可以小于实际长度；blob和text必须制定长度。
  - 唯一索引：值唯一，允许有空值。
  - 主键索引：不允许有空值。
  - 组合索引

- 建立索引的时机：一般来说，在WHERE和JOIN中出现的列需要建立索引。
  
- 索引的不足之处
  - 降低表的更新速度（mysql不仅要保存数据，还要保存索引文件）
  - 会占用磁盘空间
  
- 注意事项
  - 索引不会包含有NULL值的列
  - 尽量使用短索引
  - 索引列排序：MySQL查询只使用一个索引，因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的。
  - like语
  - 句操作
  - 不要在列上进行运算
  - 不使用NOT IN和<>、！=操作
  
- 建立索引的原则
  - 最左前缀匹配：mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止
  - =和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql查询优化器会优化
  - 尽量选择区分度高的列作为索引
  - 索引列不能参与计算
  - 尽量的扩展索引，不要新建索引
  
### 8、锁的分类
锁是作用于索引的,行级锁都是基于索引的。只有通过索引条件检索数据，InnoDB才使用行级锁，否则，InnoDB将使用表锁
- 死锁：当两个事务同时执行，一个锁住了主键索引，在等待其他相关索引。另一个锁定了非主键索引，在等待主键索引。这样就会发生死锁。
- 表锁，分为共享读锁(又称读锁)和拍他锁
  - 读锁：用户可以并发读取数据,但任何事物都不能对数据进行修改,除非释放了所有共享锁
  - 排他锁（exclusive Lock）又称写锁，获准排他锁的事务既能读数据，又能修改数据。其他事务不能再对A加任任何类型的锁。
  - 意向锁
  
### 9、MyISAM和InnoDB
- MyISAM：每一个MyISAM表都对应于硬盘上的三个文件。
  - .frm文件保存表的定义，并不是MyISAM引擎的一部分，而是服务器的一部分
  - .MYD保存表的数据
  - .MYI是表的索引文件
  - ISAM被设计为适合处理读频率远大于写频率
  - MyISAM继承ISAM优点，还提供了表级锁，故障恢复，支持全文搜索，不支持外键
 
- InnoDB：适用于高并发读写，使用MVCC(Multi-Version Concurrency Control)以及行级锁来提供遵从ACID的事务支持。
  - 支持外键参照完整性，具备故障恢复能力
  - 性能不错
  - 备份恢复要麻烦一点，必须在停掉MYSQL后对进行数据恢复

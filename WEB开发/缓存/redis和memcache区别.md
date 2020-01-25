---
title: "redis和memcache区别"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["缓存"]
categories: ["缓存"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
### 两者的区别
- 简单回答
  - redis不仅支持简单的K/V类型，还提供了list、set、hash等
  - redis支持数据的备份，即master-slave模式的数据备份
  - redis支持数据持久化
  
- 对比
  -  性能：差不多
  -  操作便利性：redis丰富一些，较少的网络IO次数
  -  可用性（单点问题）：redis支持（快照、AOF），memcache不支持，通常用在做缓存,提升性能
  -  数据一致性（事务支持）：Memcache 在并发场景下，用cas保证一致性；redis事务支持比较弱，只能保证事务中的每个操作连续执
  
### 区别
- 网络IO模型
  - memcached是多线程，非阻塞IO复用。所有操作有锁的概念，性能损耗。
  - redis使用单线程的IO复用。单线程将I/O速度优势发挥到最大，但提供的计算功能影响了吞吐量，此过程中整个IO调度都是被阻塞的。
  
- 数据支持类型
  - memcached使用key-value形式存储和访问数据，在内存中维护一张巨大的HashTable
  - 还提供list,set,zset,hash等数据结构
  
- 内存管理机制
  - Slab Allocation机制管理内存。还是结合了malloc/free
  - Redis采用的是包装的malloc/free。当物理内存用完时，Redis可以将一些很久没用到的value交换到磁盘。
- 数据存储及持久化
  - memcached不支持内存数据的持久化操作，所有的数据都以in-memoryredis支持持久化操作。的形式存储
  - redis支持持久化操作。一种是快照（snapshotting)，另一种方法叫只追加文件（append-only file， AOF)。
- 数据一致性问题
  - Memcached提供了cas命令，可以保证多个并发访问操作同一份数据的一致性问题。
  - 不过Redis提供了事务的功能。
- 集群管理不同
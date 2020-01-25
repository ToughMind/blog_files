---
title: "JVM学习"
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
### 1、Java程序执行的过程
![image](http://upload-images.jianshu.io/upload_images/1447393-77fe1d81f5fb911a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image](http://img.blog.csdn.net/20160308210409086)

- 方法区存放类信息，常量，静态变量，方法信息等。
- 栈记录了线程的方法调用。有新方法调用，则栈会增加一个存储单元，即帧(frame)。frame中，保存有该方法调用的参数、局部变量和返回地址。
  - 栈中保存基本类型和对象引用。

- 新建一个对象，位置在哪， Dog dog = new Dog();
  - 如果dog局部变量，dog变量在栈帧的局部变量表，这个对象的引用就放在栈帧。
  - 如果dog是实例变量，dog变量在堆中，对象的引用就放在堆。
  - 如果dog是静态变量，dog变量在方法区，对象的引用就放在方法区。
  
### 2、并发编程模型的分类
- 线程之间的通信：命令式编程中，线程间的通信机制有两种：共享内存和消息传递。
  - 共享内存：线程间通过写-读内存中的公共状态来隐式进行通信
  - 消息传递：线程间没有公共状态，通过明确的发送消息来显示通信
  
- 线程同步
  - 是指程序用于控制不同线程之间操作发生的顺序
  - 共享内存模型，同步是显式的，程序员必须指定某个方法或段代码需在线程间互斥
  - 消息传递模型，消息的发送必须在消息的接收之前，是隐式的
  
- java的并发采用的是共享内存模型，线程间通信总是隐式的。若程序员不了解线程间通信的工作机制，可能遇到各种奇怪的内存可见性问题。

### 3、Java内存模型的抽象
- 所有实例域、静态域、数组元素存储堆中，堆内存在线程之间共享
- 局部变量、方法定义参数、异常处理器参数不会线程间共享，不受内存模型的影响
![image](http://wiki.jikexueyuan.com/project/java-memory-model/images/1.png)

### 4、重排序
- 在执行程序时为了提高性能，编译器和处理器常常会对指令做重排序
  - 编译器优化的重排序。
  - 指令级并行的重排序。
  - 内存系统的重排序。
  
- JMM 属于语言级的内存模型，它确保在不同的编译器和不同的处理器平台之上，通过禁止特定类型的编译器重排序和处理器重排序，为程序员提供一致的内存可见性保证。
- 数据依赖性
  - 读后写，写后读，写后写
- as-if-serial 语义：不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。
- 程序顺序规则
  - 如果 A happens- before B，JMM 并不要求 A 一定要在 B 之前执行。JMM 仅仅要求前一个操作（执行的结果）对后一个操作可见，且前一个操作按顺序排在第二个操作之前。
  
- 重排序对多线程的影响
  - 如下，1、2和3、4是可能重排的，结果显而易见
      
    ```
    class ReorderExample {
    int a = 0;
    boolean flag = false;
    
    public void writer() {
        a = 1;                   //1
        flag = true;             //2
    }
    
    Public void reader() {
        if (flag) {                //3
            int i =  a * a;        //4
            ……
        }
    }
    }  
    ```

### 5、顺序一致性内存模型
- 两个特征
  - 一个线程中的所有操作必须按照程序的顺序来执行
  - （不管程序是否同步）所有线程都只能看到一个单一的操作执行顺序。在顺序一致性内存模型中，每个操作都必须原子执行且立刻对所有线程可见。
- 比如线程A:A1-A2-A3，线程B:B1-B2-B3
  - 同步的一致性：A1-A2-A3-B1-B2-B3
  - 未同步的一致性：A1-B1-A2-A3-B2-B3
  
- JMM并不是如此。临界区的代码可以重排，且不可“逸出”到临界区外
![image](http://wiki.jikexueyuan.com/project/java-memory-model/images/13.png)

- JMM和顺序一致性模型区别
  - 在同步情况下，结果一致，JMM可以在临界区内重排
  - 在未同步情况下
    - JMM不保证单线程内操作顺序，但后者保证了
    - JMM不保证所有线程能看到一致的cao'z操作执行顺序，后者保证了
    - JMM不保证64位long、double的读写具有原子性，后者保证了
      - JVMzai在一些32位处理器上，会把64位的long/double拆分两个32位。 
      
### 6、volatile
- 锁的 happens-before 规则保证释放锁和获取锁的两个线程之间的内存可见性，这意味着对一个volatile 变量的读，总是能看到（任意线程）对这个 volatile 变量最后的写入。
- 特性
  - 可见性：对一个 volatile 变量的读，总是能看到（任意线程）对这个 volatile 变量最后的写入。
  - 原子性：对任意单个 volatile 变量的读/写具有原子性，但类似于 volatile++ 这种复合操作不具有原子性。
- 写-读内存语义
  - 从 JSR-133 开始，volatile 变量的写-读可以实现线程之间的通信。
  - 当写时，JMM会把该线程对应的本地内存的共享变量刷新到主内存
  - 当读时，JMM会把该线程对应的本地内存置为无效
  
- volatile内存语义的实现
  - 当第二个操作是 volatile 写时，不管第一个操作是什么，都不能重排序
  - 当第一个操作是 volatile 读时，不管第二个操作是什么，都不能重排序
  - 当第一个操作是 volatile 写，第二个操作是 volatile 读时，不能重排序

- 下面是基于保守策略的 JMM 内存屏障插入策略：
  - 在每个 volatile 写操作的前面插入一个 StoreStore 屏障。
  - 在每个 volatile 写操作的后面插入一个 StoreLoad 屏障。
  - 在每个 volatile 读操作的后面插入一个 LoadLoad 屏障。
  - 在每个 volatile 读操作的后面插入一个 LoadStore 屏障。
  
- volatile和锁
  - volatile 仅仅保证对单个 volatile 变量的读/写具有原子性
  - 锁的互斥执行的特性可以确保对整个临界区代码的执行具有原子性
  - 在功能上，锁比 volatile 更强大；在可伸缩性和执行性能上，volatile 更有优势
  
### 7、锁
- 锁除了让临界区互斥执行外，还可以让释放锁的线程向获取同一个锁的线程发送消息
- ReentrantLock的实现依赖于 java 同步器框架 AbstractQueuedSynchronizer（AQS）
  - AQS 使用一个整型的 volatile 变量（命名为 state）来维护同步状态
![image](http://wiki.jikexueyuan.com/project/java-memory-model/images/27.png)

  - 使用公平锁加锁
    -  ReentrantLock : lock()
    -  FairSync : lock()
    -  AbstractQueuedSynchronizer : acquire(int arg)
    -  ReentrantLock : tryAcquire(int acquires)

  - 使用公平锁解锁
    - ReentrantLock : unlock()
    - AbstractQueuedSynchronizer : release(int arg)
    - Sync : tryRelease(int releases)
    
  - 使用非公平锁加锁
    - ReentrantLock : lock()
    - NonfairSync : lock()AbstractQueuedSynchronizer :
    - compareAndSetState(int expect, int update)
    
  - 总结
    - 公平锁和非公平锁释放时，最后都要写一个 volatile 变量 state
    - 公平锁获取时，首先会去读这个 volatile 变量
    - 非公平锁获取时，首先会用 CAS 更新这个 volatile 变量,这个操作同时具有 volatile 读和 volatile 写的内存语义
    - 公平锁在获取时，没有获取到锁的线程挂起，进入队列，还多了hasQueuedPredecessors()的判断
    
    
 ### 8、CAS如何同时具有 volatile 读和 volatile 写的内存语义
- 程序会根据当前处理器的类型来决定是否为 cmpxchg 指令添加 lock 前缀，多处理器情况下会加

- intel 的手册对 lock 前缀的说明
  - 确保对内存的读-改-写操作原子执行。
    - 在 Pentium之前的处理器中，lock前缀指令会锁住总线。之后优化了：如果要访问的内存区域在lock指令前缀之前，已在处理器缓存中锁定，并且该内存区域被完全包含在单个缓存行中，处理器将直接执行指令
  - 禁止该指令与之前和之后的读和写指令重排序。
  - 把写缓冲区中的所有数据刷新到内存中
  
### concurrent包的实现
- java 的 CAS 同时具有 volatile 读和 volatile 写的内存语义，因此 Java 线程之间的通信现在有了下面四种方式：
  - A 线程写 volatile 变量，随后 B 线程读这个 volatile 变量。
  - A 线程写 volatile 变量，随后 B 线程用 CAS 更新这个 volatile 变量。
  - A 线程用 CAS 更新一个volatile变量，随后 B 线程用 CAS 更新这个 volatile 变量。
  - A 线程用 CAS 更新一个 volatile 变量，随后 B 线程读这个 volatile 变量。
  
- 分析 concurrent 包的源代码实现，会发现一个通用化的实现模式：
  - 首先，声明共享变量为 volatile；
  - 然后，使用 CAS 的原子条件更新来实现线程之间的同步；
  - 同时，配合以 volatile 的读/写和 CAS 所具有的 volatile 读和写的内存语义来实现线程之间的通信。
![image](http://wiki.jikexueyuan.com/project/java-memory-model/images/28.png)

### 9、进程和线程
- 区别
  - 进程是系统进行资源分配和调度的一个独立单位
  - 线程是进程的一个实体，是CPU调度和分派的基本单位
  - 方法区和堆是分配给进程的，而栈和程序计数器则是分配给每个独立线程的
  
### 10、内存溢出
- 堆
  - 无限new对象，存在list中
- 方法区
  - 非常量区：生成大量动态类
  - 常量区：无限调用String的intern()方法，存在list中
- 栈
  - 单线程：递归
  - 多线程：无线循环创造线程

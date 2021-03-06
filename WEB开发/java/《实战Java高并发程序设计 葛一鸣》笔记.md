---
title: "《实战Java高并发程序设计 葛一鸣》笔记"
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
## 2016.12.8
### 一些基本概念
- 同步和异步：用来形容一次调用。

- 并发：偏重于多个任务交替进行，而多个任务之间可能是串行。并行：是真正意义上的“同时执行”。

- 临界区：可以用来表示共享资源。但每次只能有一个线程使用它。

- 阻塞和非阻塞：通常用来形容线程间的相互影响，一个线程影响其他线程的挂起和前进。

- 死锁、饥饿、活锁：多线程活跃性的问题。

- 并发级别：阻塞调度是悲观，非阻塞调度是乐观
  - 阻塞：synchronized或者重入锁，会试图在执行后续代码前，得到临界区的锁。
  
  - 无饥饿：排队。
  
  - 无障碍：最弱的非阻塞调度。多个线程无障碍进入临界区，若一旦发现都有修改，则回滚；若没有发生数据竞争，则顺利完成。
  
  - 无锁：要求有一个线程可以在有限步内完成操作。
  
  - 无等待：要求所有线程必须在有限步内完成操作。
  
### 两个重要定律
- 加速比=优化前系统耗时/优化后系统耗时

- Amdahl定律：当串行比例一定时，加速比是有上限的。

- Gustafson定律：如果可被并行的代码所占比例足够多，那么加速比就可以随cpu数量线性增长。

### JMM，java内存模型
- 原子性：一个操作不可中断，操作不会被其他线程干扰。

- 可见性：一个线程更改了共享变量的值，其他线程是否能够立即知道这个修改。
  - 缓存优化、硬件优化，有些内存读写，指令重排、编辑器优化都可能导致一个线程修改不被另一个线程察觉。
 
- 有序性：程序执行时，有可能会进行指令重排。
  - 指令重排可以保证串行语义一致，但没有义务保证多线程间的语义也一致。
  
- 指令不能重排：Happen-Before规则。如程序顺序、volatile规则、锁规则、线程、finalize()等

## 2016.12.15
### 关于线程
- 进程是系统资源分配和调度的基本单位，是程序执行实体，是关于某数据集合上的一次运行活动。
  - 程序是死的，静态的；进程是活的
- 进程是线程的容器。线程就是轻量级的进程。
  - 使用多线程而不使用多进程，是因为线程间的切换和调度成本远远小于进程。
  
### java实现线程
- 线程的所有状态有5种：NEW,RUNABLE,BLOCKED,WAITING,TIMED_WAITING,TERMINATED
- 默认的Thread.run()就是直接调用内部的Runable接口。因此使用Runable接口更为合理。

## 2016.12.26
### java实现线程相关
- Thread.sleep()方法由于中断而抛出异常，此时，它会清除中断标记。
  - 注意，在catch代码里要重新设置中断。
  
## 2016.1.6
### java实现线程相关
- 当调用object.notify()，会从等待队列中，随机选择一个线程，并将其唤醒。
  - 跟优先级无关，完全随机。
  
- object.wait()必须包含在synchronized语句中，因为wait()和notify()都需要先获得目标对象的监视器。
  - wait()会释放目标对象的锁，但sleep()方法不会释放任何资源。
  - suspend()和resume()是一对，suspend()也不会释放锁。若suspend()之后没有resume()就GG了。
  
- join()的本质还是wait()和notify()机制。
  - 由此值得注意：不要在Thread对象实例上使用类似wait和notify的方法，容易与系统API互相影响。
  
- volatile是无法保证i++的原子性的。不能代替锁，也无法保证一些复合操作的原子性。 

## 2017.1.7
### java实现线程相关
- synchronized实现线程间同步。对同步块加锁，使每次只能有一个线程进入同步块。
  - 作用于对象：要获得给定对象的锁
  - 作用于实例方法：要获得当前实例对象的锁。注意并行操作时一定要保证是同一个实例才有效。
  - 作用于静态方法：要获得当前类的锁
  
## 2017.1.9
### java并发包
- 重入锁，synchronized的功能扩展
  - 为何叫重入？因为一个线程可以连续两次获得同一把锁。
  - 中断响应功能：一个线程在等待，那么它依然可以收到一个通知，被告知无须再等待。
  - 锁申请等待限时：避免死锁，除了上面的等待外部响应外，还可以限时。
  - 三个要素：
    - 原子状态，使用CAS操作来存储当前锁状态
    - 等待队列，未请求到锁的线程会进入等待队列
    - 阻塞原语park()和unpark()
    
  -  使用condition.await()和signal()前，要先获得重入锁，之后也记得要释放锁。
  
- 信号量，Semaphore，允许多个线程同时访问。
- 读写锁，读读之间是非阻塞的。
- CountDownLatch：让某个线程等到倒计时结束再执行（可指定数量的线程完成之后）。
- 循环栅栏CyclicBarrier：功能比CountDownLatch更强大 
  - CountDownLatch就针对一次计时完成，然后执行动作；而CyclicBarrier可指定每一次计时完成触发动作。
  - CyclicBarrier每一轮计数完毕后都会重新开始计数，并且可以指定每一轮完毕后的触发动作。这是很CountDownLatch的最大不同之处。
  - 被中断可能抛出BrokenBarrierException异常，因为此时CyclicBarrier已破损，继续等待已毫无意义。
  
- LockSupport非常实用的线程阻塞工具，支持中断，但park()方法不会抛出异常。

## 2017.1.17
### 回顾复习线程基础和基本并行包
- 所有线程状态，Thread枚举
  - NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED
  
## 2017.2.7
### 线程池
- newFixThreadPool():固定的线程数量
- newSingleThreadExecutor():只有一个线程
- newCachedThreadPool():可根据实际情况调整线程数量
- newSingleThreadScheduledExecutor():一个线程，给定时间执行某任务
- newScheduledThreadPool():功能同上，可指定线程数量

## 2017.2.9
### 计划任务
- newScheduledThreadPool
  - scheduleAtFixedRate()：调度的频率是一定的。
  - scheduleWithFixedDelay()：是上一个任务结束再经过delay时间再执行。
- 如果任务中出现异常，则会停止调度

## 2017.2.10
### BlockingQueue
- 直接提交：SynchronousQueue。没有容量，拒绝策略。
- 有界：ArrayBlockingQueue。
- 无界：LinkedBlockingQueue。
- 优先：PriorityBlockingQueue。
- 个人理解而言，线程池有数量限制，超出了限制，任务会放进队列里（不同队列有不同的处理策略），从队列里挤出来的任务，就需要线程池去复用或者开辟新空间去处理任务了。
  - 队列是在线程池满的情况下使用，线程池开辟新线程也是队列里挤出任务出来时才使用。
- keepAliveTime：超出线程池默认数量的线程空闲后的存活时间。

##  2017.2.13
### 拒绝策略
- 线程池和等待队列都最满状态了。JDK内置4种策略：
  - AbortPolicy：抛出异常，阻止工作。
  - CallerRunsPolicy：直接在调用者线程中运行被丢弃的任务。
  - DiscardOledestPolicy：丢弃最老的一个请求（就是即将被执行的任务），尝试重新提交任务
  - DiscardPolicy：默默丢弃无法处理的任务。
- 均实现RejectedExecutionHandler接口
- shutdown()并不会暴力终止所有，会等待任务完成再关闭线程池。但之后线程池不能再接受其他新的任务了。

##  2017.2.14
### 最优线程池数量
- CPU的数量 * 目标CPU的使用率 * 等待时间与计算时间的比率

##  2017.2.15
### fork/join框架
- 任务划分太深会导致线程量积多，栈溢出
- 使用一个无锁的栈来管理空闲线程。

### 并发集合容器
- ConcurrentHashMap：线程安全的hashmap
- CopyOnWriteArrayList：读多写少的场合，性能超好，远于Vector
  - 读操作完全不加锁
  - 写入不会阻塞读，但写入和写入需要同步等待
  - 写入前，先读数据复制到副本中，修改的是副本，然后在把副本替换掉原有数据。修改完后，读线程会立即“察觉”到这个修改，因为array变量是volatile型

- ConcurrentLinkedQueue：并发队列，链表实现
  - t != t在多线程中可能发生，此队列实现源码里就有此应用
  - 不用锁，用CAS操作实现增加了设计和实现的难度，但性能非常高

- BlockQueue：接口，阻塞队列
  - 类似模式：希望线程A能够通知线程B，又希望线程A不知道线程B的存在。而中间联系点就是这个阻塞队列。
  - 难点：服务线程处理完队列的所有任务后，如何知道下一个任务消息呢？
    - 1、此线程定时循环监控
    - 2、队列为空时，此线程等待；队列有新任务后，自动唤醒线程
  - offer()和poll()都是立即返回（null或false）；而put()和take()则会等待，体现了BlockQueue的关键

- ConCurrentSkipListMap：跳表
  - 空间换时间
  - 平衡树是全局调整，而跳表的插入和删除只需局部调整
  - 对于Node的所有操作都是CAS方法

- 除以上并发包，java.util.Vector也是线程安全。Collections工具类可以帮助将任何集合包装成线程安全。

### 锁性能提高
- 减小锁的持有时间

- 减小锁粒度：缩小锁定对象的范围
  - 当系统需要取得全局锁时，消耗资源会增多，需要获取所有粒度的锁
  - 典型ConcurrentHashMap
    - put()操作，是对通过hashcode得到的某一段加锁，默认16段
    - 当获取全局信息时，比如size()，但此Map未采取直接获取所有子段的锁，而是通过无锁的方式获取求和，若失败才会获取所有粒度的锁
    
- 读写分离锁代替独占锁
  - 可以看做减小锁粒度(根据数据结构分割)的特例，根据功能点分割
  
- 锁分离
  - 例如LinkedBlockingQueue，take()和put()分别操作队列头和尾，采用了两把不同的锁，使得take和put没有竞争关系

- 锁粗化
  - jvm在遇到一连串连续对同一锁不断进行请求和释放的操作时，会把所有锁操作整合成对锁的一次请求
  - 某种程度上，锁粗化和减少锁持有时间的思想是相反的
  - 性能优化是根据运行时的真实情况对各个资源点进行权衡折中的过程
  
## 2017.2.16
### jvm对锁优化
- 锁偏向： 若一个线程获得锁，则进入偏向模式。当这个线程再请求此锁时，无须再做任何同步操作。
  - 在几乎没有锁竞争的场合适用。当锁竞争强力时，可能每次都是不同的线程来请求相同的锁。
  - -XX:+UseBiasedLocking可以开启偏向锁
  
- 轻量级锁（锁偏向失败）：简单将对象头部作为指针，指向持有锁的线程堆栈的内部，来判断一个线程是否持有对象锁。

- 自旋锁（轻量级锁膨胀成重量级锁）
  - 当前线程暂时无法获得锁，什么时候获得也不知道，若直接挂起线程可能又得不偿失
  - jvm会让当前线程做几个空循环（自旋），若经过若干循环后还未获得锁，则将线程在操作系统层面挂起
  
- 锁消除：jvm在JIT编译时，扫描上下文消除不可能存在共享资源竞争的锁
  - 比如局部变量使用了Vector这样的
  - 设计一项关键技术为 逃逸分析
    - 逃逸分析必须在 -server模式
    - -XX:+DoEscapeAnalysis打开逃逸分析
  - -XX:EliminateLocks打开锁消除
  
### ThreadLocal相关
- Thread是一个线程的局部变量，所以自然是线程安全的
- 为每一个线程分配不同对象，需要在应用层面保证。ThreadLocal只起到了简单的容器作用

## 2017.2.17
### ThreadLocal实现原理
- 内部通过ThreadLocalMap管理
- 要注意回收，使用线程池可能线程未退出.把内存变量置为空或者ThreadLocal.remove()可以加速垃圾回收
- ThreadLocalMap的实现使用了弱引用，以ThreadLocal作为key，但实际并不真的持有ThreadLocal的引用。而当ThreadLocal的外部强引用被回收时，ThreadLocalMap的key就置为null了。
- 若在共享过程中，竞争对象会引起较大性能损耗，就应该考虑使用ThreadLocal为每个线程分配单独的对象

### CAS技术
- 非阻塞性，不会有死锁问题
- 没有锁竞争带来的系统开销
- 没有线程间频繁调度带来的开销
- (V,E,N)V是要更新的变量，E是预期值，N是新值。简单而言，CAS需要额外给出一个预期值

## 2017.2.18
### JDK直接使用CAS操作的线程安全类
- AtomicInteger
- Unsafe：看源码可知，这是JDK内部使用专属类，自己写代码获取会抛出异常
  - 应用程序是由App Loader加载的，而系统核心类如rt.jar中的类由BootStrap类加载器加载。BootStrap加载器没有java对象的对象，所以当一个类的类加载器是null时说明它是BootStrap加载的，且这个类极有可能是rt.jar中的类
```
public static Unsafe getUnsafe()
{
  Class localClass = Reflection.getCallerClass();
  if (!(VM.isSystemDomainLoader(localClass.getClassLoader())))
    throw new SecurityException("Unsafe");
  return theUnsafe;
}
```
- AtomicReference，AtomicStampReference
  - 有个例外，当对象的值被其他线程连续修改了两次，而经过这两次修改后，对象值又恢复旧值，这时当前线程无法判断该对象是否被修改过。这个是业务上的需求，需要判断这个对象的值是否被修改过，显示加个时间戳标识可以解决这个问题。

- AtomicStampReference：带有时间戳的对象引用，在更新数据本身之外，还要更新时间戳。

- 数组等复合结构，如AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray等

## 2017.2.20
### AtomicIntegerFieldUpdater类似
- 只能修改其可见范围内的变量，是通过反射得到这个变量的
- 为了确保变量正确可读，必须是volatile类型
- 由于CAS操作会通过对象实例中的偏移量直接进行赋值，因此不支持static
  - Unsafe.objectFieldOffset不支持静态变量
## 自己实现Vector的无锁算法
- 用了二维AtomicReferenceArray数组：便于扩容，扩容是成倍增加
- 利用位运算、前导零数量等判断出新增元素放在第几个数组的第几个偏移量中

## 细看SynchronousQueue实现
- 任何一个写都要等待一个读。与其称为队列，不如称为数据交换通道。
- 大量使用无锁工具。
- 内部会维护一个线程等待队列。
- 核心三步骤
  - 若等待队列为空，或队列结点类型和本次操作一致，那么当前操作压入等待队列
  - 若等待队列和本次操作互补，那么插入一个“完成”状态的结点，并“匹配”到一个等待结点上，接着弹出这两个结点使得对应两个线程继续执行
  - 若等待队列是“完成”结点，那么帮助这个结点完成任务。流程同2.
- 参与工作的所有线程不仅仅是竞争资源的关系，它们还会互相帮助，这种模式更大程度减少了饥饿的可能，提高系统整体并行度。

## 死锁
- 两个或多个线程，相互占用对方需要的资源，而不进行释放，导致彼此都相互等待产生无限等待的现象。
- 通常遇到死锁，表现是进程不再工作，而且CPU占用率为0.此时需要用jps命令得到java进程号，再用jstack命令得到线程堆栈。

## 2017.5.4
### 并行模式与算法，单例模式
- 任何对某个类方法和字段的引用，都会导致类的初始化
- 最佳实践：利用内部类。

### 不变模式
- 当对象被创建，其内部状态和数据不再发生任何改变。
- 对象需要被共享，被多线程频繁访问。
- 广泛应用，如String，基本类型的封装类等
- 不变模式是通过回避问题而不是解决问题的态度来处理多线程并发访问控制。
```
1、去除setter及可以改变自身属性的方法
2、所有属性私有，且private
3、确保没有子类可以重载修改它的行为
4、有可以创建完整对象的构造函数
```

### 生产者和消费者
- 很经典的多线程设计模式
- 生产者-消费者模式中的内存缓存区主要功能是在多线程间共享。此缓冲区可以缓解生产者和消费者的性能差。

### 无锁的缓存框架：Disruptor
- 高性能无锁内存队列。

## 2017.5.5
### 无锁的缓存框架：Disruptor
- 合适的策略
  - BlockingWaitStrategy：最节省CPU，效率最低
  - SleepingWaitStrategy：对延迟要求不高。典型，异步日志
  - YieldingWaitStrategy：低延时场合
  - BusySpinWaitStrategy：最疯狂监控缓冲区的变化
  
## 2017.5.6
### Future模式
- 核心思想：异步调用
- 
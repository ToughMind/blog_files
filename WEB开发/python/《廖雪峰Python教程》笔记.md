---
title: "《廖雪峰Python教程》笔记"
date: 2020-01-17T11:51:28+08:00
description: ""
lastmod: 2018-07-10T00:00:00+08:00
draft: false
tags: ["python"]
categories: ["python"]

weight: 1
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
[博客链接](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)

## 1、字符串编码
### ASCII、Unicode、UTF-8
- ASCII编码是1个字节，而Unicode编码通常是2个字节
- 如果统一成Unicode编码，乱码解决，但存储空间要多一倍，存储和传输上非常不划算
- 本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的UTF-8编码
  - UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节
- 计算机系统通用的字符编码工作方式：
  - 在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或传输的时候，就转换为UTF-8
![image](https://www.liaoxuefeng.com/files/attachments/001387245992536e2ba28125cf04f5c8985dbc94a02245e000/0)
![image](https://www.liaoxuefeng.com/files/attachments/001387245979827634fd6204f9346a1ae6358d9ed051666000/0)

### 编码语法点
- 要注意区分'ABC'和b'ABC'，前者是str，后者虽然内容显示得和前者一样，但bytes的每个字符都只占用一个字节
- 含有中文的str无法用ASCII编码，因为中文编码的范围超过了ASCII编码的范围，Python会报错
- 1个中文字符经过UTF-8编码后通常会占用3个字节，而1个英文字符只占用1个字节
- 通常在文件开头写上这两行
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```
## 2、剩余基础语法
### 列表list和元祖tuple
- 当定义一个元祖的时候`t=(1)`
  - 这个时候，按小括号进行计算
  - 此时若要是元祖，要加上逗号分隔，`t=(1,)`

- tuple一旦初始化就不能修改
  - 是指向不变

### if判断流程
- if条件是非零数值、非空字符串、非空list等，就判断为True，否则为False

### list,tuple,dict,set
- dict和set的key，不能使用可变对象（如List）
  - 内容不可变的tuple可以作为key
  - 内容可变的tuple不能作为key，如tuple中包含有list
- 对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容

### 函数调用
- 参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数
- 定义默认参数要牢记一点：默认参数必须指向不变对象！
- *nums表示把nums这个list的所有元素作为可变参数传入*numbers参数
- **extra表示把extra这个dict的所有key-value用关键字参数传入到函数的**kw参数
- 对于任意函数，都可以通过类似func(*args, **kw)的形式调用它

### 递归函数
- 注意防止栈溢出
  - 每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧
  
- 尾递归优化
  - 在函数返回的时候，调用自身本身，并且，return语句不能包含表达式
  - 大多数编程语言没有做这个优化，python解释器也没有
 
## 3、高级特性
### 迭代
- 如何判断一个对象是否可以迭代
```
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```
### 列表生成器
- `[x * x for x in range(1, 11) if x % 2 == 0]`
- `[m + n for m in 'ABC' for n in 'XYZ']`
- `for i, value in enumerate(['A', 'B', 'C'])`
- `[d for d in os.listdir('.')] # os.listdir可以列出文件和目录`

### 生成器
- 一边循环一边计算的机制，称为生成器：generator
- a, b = b, a + b
  - 相当于t = (b, a + b) # t是一个tuple
- 如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator
  - 在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行
- generator的return如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中

### 迭代器
- 可迭代对象 Iterable
  - 一类是集合数据类型，如list、tuple、dict、set、str等
  - 一类是generator，包括生成器和带yield的generator function
- 可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator
- 注意可迭代对象Iterable和迭代器Iterator
- Iterator可以通过iter()函数获得一个Iterator
- Python的for循环本质上就是通过不断调用next()函数实现的

## 4、函数编程
### 基本概念
- 函数就是面向过程的程序设计的基本单元
- 函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量
  - 允许把函数本身作为参数传入另一个函数，还允许返回一个函数
- 由于Python允许使用变量，因此，Python不是纯函数式编程语言

### 高阶函数
- 变量可以指向函数
- 函数名也是变量
- 既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数
- map，reduce，filter，sorted

### 返回函数
- 闭包：当一个函数返回了一个函数后，其内部的局部变量还被新函数引用
  - 返回函数不要引用任何循环变量，或者后续会发生变化的变量
  
### 装饰器
- 在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）

### 偏函数
- functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数

## 5、模块
### 简介
- 在Python中，一个.py文件就称之为一个模块（Module）
- 提高了代码的可维护性
- 可以避免函数名和变量名冲突
- 每一个包目录下面都会有一个__init__.py的文件，这个文件是必须存在的

### 使用模块
- 当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量__name__置为__main__
- 常用模块
  - Pillow：图片处理
  - mysql-connector-python：mysql驱动
  - numpy：科学计算
  - Jinja2：生成文本的模板工具
  
## 6、OOP面向对象编程
### 面向过程和面向对象
- 面向过程：把计算机程序视为一系列的命令集合，即一组函数的顺序执行
- 面向对象：把计算机程序视为一组对象的集合，计算机程序的执行就是一系列消息在各个对象之间传递

### 类与实例
- 各个实例拥有的数据都互相独立
- 方法就是与实例绑定的函数
- 两个实例变量，拥有的变量名称都可能不同
### 访问限制
- 双下划线开头的实例变量是不是一定不能从外部访问呢
  - 不是，因为Python解释器对外把__name变量改成了_Student__name（注意不同解释器机制不同）
- 一个下划线开头的实例变量名
  - 虽然我可以被访问，但是，请把我视为私有变量，不要随意访问
- 双下划线开头，并且以双下划线结尾的，是特殊变量，不是private
### 继承和多态
- 具体调用的方法，由运行时该对象的确切类型决定
- 静态语言和动态语言
  - 对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类
  - 对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以
    - 这就是动态语言的“鸭子类型”
    
### 获取对象信息
- 判断类型
```
>>> type(fn)==types.FunctionType
True
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True
```
- 类似__xxx__的属性和方法在Python中都是有特殊用途的
  - 在len()函数内部，它自动去调用该对象的__len__()方法
  
- dir(),getattr(),setattr(),hasattr()

- 要注意的是，只有在不知道对象信息的时候，我们才会去获取对象信息 
```
def readImage(fp):
    if hasattr(fp, 'read'):
        return readData(fp)
    return None
```

### 使用__slots__
- 限制该class实例能添加的属性
  - __slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的
### @property
- 注意`@property`本身又创建了另一个装饰器`@某某.setter`，

### 多重继承
- MixIn设计

### 定制类
- __str__和__repr__
  - 前者是返回用户看的，后者是返回开发者看的
- __iter__
  - 如果一个类要被用于for……in循环，类似于list或tuple
- __getattr__
- __getitem__
- __call__

### 元类
- 可创建class类
  - type()
  - metaclass
    - 可以把类看成是metaclass创建出来的“实例”
    
### 调试
- pdb
  - `python3 -m pdb xxx.py`
  - `pdb.set_trace()`
- 单元测试
  - 从unittest.TestCase继承
  - 最常用的断言就是`assertEqual()`
  - 另一种重要的断言就是期待抛出指定类型的Error，比如`with self.assertRaises(KeyError)`
  - 可以在单元测试中编写两个特殊的setUp()和tearDown()方法
  
## 7、IO操作
### 文件读写
- `with open('/path/to/file', 'r', encoding='gbk', errors='ignore') as f`可自动调用close()
### 内存IO
- StringIO
- BytesIO

### 文件和目录操作
- 函数有的在os模块中，有的在os.path模块中

### 序列化
- pickle
- json
  - 序列化，default参数
  - 反序列化，object_hook参数
  
## 8、多进程线程
- 多进程
  - fork
  - multiprocessing
  - Pool
  - subprocess

- 进程间通信
  - multiprocessing模块包装了底层的机制，提供了Queue、Pipes等
  - Queue、Pipes等实现
  
- 多线程
  - 线程是操作系统直接支持的执行单元，因此，高级语言通常都内置多线程的支持
  - Python的线程是真正的Posix Thread，而不是模拟出来的线程
  - 多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享
  
- Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock。
  - 所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核
  - Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。
  
- ThreadLocal

- 多进程与多线程
  - 多任务，通常设计是一个Master多个Worker
  - 多进程模式代价大；多线程模式任何一个线程挂掉可能造成整个进程崩溃
  
- 计算密集型和IO密集型
  - 计算密集型要进行大量的计算，最好用C编写
  - IO密集型99%时间花费在网络IO上，脚本语言是首选
  
- 异步IO
  - 协程：不是线程切换，不需要多线程锁机制
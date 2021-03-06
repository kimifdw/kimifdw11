---
title: java concurrent
date: "2020-03-06"
spoiler: 并发
---

# 一、locks包
## 1.全景图
![image](./locks.png)

## 2. lock（接口）
1. 锁是用于通过多线程控制对共享资源的访问工具，一次只有一个线程可以获取锁，并且对共享资源的所有访问都要求获取锁。
2. 在锁定时执行的代码由**try-finally**或**try-catch**保护，以确保在必要时释放锁定

### 2.1 方法集
1. `lock()`。获得锁，如果锁不可用，则当前线程将被**禁用**以进行线程调度，并处于**休眠状态**
2. `lockInterruptibly`。尝试获取可被中断的锁，除非当前线程被interrupted。
    1. 不可用-->可用
        1. 锁是由当前线程获取
        2. 一些其他线程interrupts当前线程，并且支持中断锁获取
    2. 中断清除
        1. 进入该方法时设置了中断状态
        2. interrupted，同时获取锁，并支持锁中断，然后`InterruptedException`被关上
3. `tryLock()`。非阻塞尝试获取锁
4. `tryLock(long,TimeUnit)`。尝试获取超时锁
    1. 不可用-->可用
        1. 锁是由当前线程获取
        2. 一些其他线程interrupts当前线程，并且支持中断锁获取
        3. 指定的等待时间过去了
    2. 中断清除
        1. 进入该方法时设置了中断状态
        2. interrupted，同时获取锁，并支持锁中断，然后`InterruptedException`被关上
5. `unlock`。释放锁
6. `newCondition()`。返回一个新Condition绑定到新实例Lock实例

### 2.2 内存同步
1. 所有Lock实施必须执行与**内置监视器锁**相同的内存同步语义
    1. 成功的lock操作具有与成功锁定动作相同的内存同步效果。
    2. 成功的unlock操作具有与成功解锁动作相同的内存同步效果。
2. **不成功的锁定和解锁**操作以及**重入锁定/解锁**操作，不需要任何内存同步效果

### 2.3 锁定采集
1. 可中断
2. 不可中断
3. 定时

## 3. ReadWriteLock（接口）
1. 支持**并发访问**共享资源

# 二、synchronized
1. 提供对与每个对象相关联的隐式监视器锁的访问，但强制所有锁获取和释放以块结构的方式发生
2. 当获取多个锁时，它们必须以**相反的顺序**被释放，并且所有的锁都必须被释放在与它们相同的词汇范围内。

# 资料整理
1. [jdk8 内存模型](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4)
2. [java se8 中文版](https://www.matools.com/api/java8)
3. [史上最全的Java并发系列](https://juejin.cn/post/6844904047305031693)
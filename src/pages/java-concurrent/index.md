---
title: java concurrent locks
date: "2020-03-06"
spoiler: 并发
---

# JAVA Current Lock

## LOCKS 全景图

![image](./locks.png)

### 一. lock（接口）

1. 锁是用于通过多线程控制对共享资源的访问工具，一次只有一个线程可以获取锁，并且对共享资源的所有访问都要求获取锁。
2. 在锁定时执行的代码由**try-finally**或**try-catch**保护，以确保在必要时释放锁定

#### 1.1 方法集

1. `lock()`。获得锁，如果锁不可用，则当前线程将被**禁用**以进行线程调度，并处于**休眠状态**
2. `lockInterruptibly`。尝试获取可被中断的锁，除非当前线程被 interrupted。
   1. 不可用-->可用
      1. 锁是由当前线程获取
      2. 一些其他线程 interrupts 当前线程，并且支持中断锁获取
   2. 中断清除
      1. 进入该方法时设置了中断状态
      2. interrupted，同时获取锁，并支持锁中断，然后`InterruptedException`被关上
3. `tryLock()`。非阻塞尝试获取锁（可定时的与可轮询的锁获取模式）
4. `tryLock(long,TimeUnit)`。尝试获取超时锁
   1. 不可用-->可用
      1. 锁是由当前线程获取
      2. 一些其他线程 interrupts 当前线程，并且支持中断锁获取
      3. 指定的等待时间过去了
   2. 中断清除
      1. 进入该方法时设置了中断状态
      2. interrupted，同时获取锁，并支持锁中断，然后`InterruptedException`被关上
5. `unlock`。释放锁
6. `newCondition()`。返回一个新 Condition 绑定到新 Lock 实例

#### 1.2 内存同步

1. 所有 Lock 实施必须执行与**内置监视器锁**相同的内存同步语义
   1. 成功的 lock 操作具有与成功锁定动作相同的内存同步效果。
   2. 成功的 unlock 操作具有与成功解锁动作相同的内存同步效果。
2. **不成功的锁定和解锁**操作以及**重入锁定/解锁**操作，不需要任何内存同步效果

#### 1.3 锁定采集

1. 可中断
2. 不可中断
3. 定时

### 二、 ReadWriteLock（接口）

1. 支持**并发访问**共享资源
2. 一个线程成功读锁定将会看到之前发布的写锁定所做的所有更新
3. 适用场景
   1. 读多写少的场景
      `Determining whether to grant the read lock or the write lock, when both readers and writers are waiting, at the time that a writer releases the write lock. Writer preference is common, as writes are expected to be short and infrequent. Reader preference is less common as it can lead to lengthy delays for a write if the readers are frequent and long-lived as expected. Fair, or "in-order" implementations are also possible.`
   2. 是否存在读锁被激活而写锁在等待，可能会降低并发的可能性
      `Determining whether readers that request the read lock while a reader is active and a writer is waiting, are granted the read lock. Preference to the reader can delay the writer indefinitely, while preference to the writer can reduce the potential for concurrency.`
   3. 锁是否可重入
      `Determining whether the locks are reentrant: can a thread with the write lock reacquire it? Can it acquire a read lock while holding the write lock? Is the read lock itself reentrant?`
   4. 是否允许锁降级
      `Can the write lock be downgraded to a read lock without allowing an intervening writer? Can a read lock be upgraded to a write lock, in preference to other waiting readers or writers?`
4. 方法
   1. `readLock()`。读锁
   2. `writeLock()`。写锁

### 三、 Condition（接口）


### 四、 ReentrantLock/ReentrantReadWriteLock

1. 重进入。任意线程在获取到锁之后能购再次获取该锁而不会被锁所阻塞 
2. 与synchronized类似，具有更好的扩展性
3. 定义。由最后成功锁定但尚未解锁的线程所拥有，可以用`isHeldByCurrentThread`和`getHoldCount`来进行检查
4. 在公平锁场景下，不能保证任何特定的执行顺序，更倾向授予**等待时间最长**的线程的访问
5. 未定义时长的`tryLock`支持不公平锁，同一线程下的锁有限制，超过此限制会导致锁定方法异常
6. 利用**AQS**实现同步（包含公平和非公平），默认ReentrantLock为不公平锁，独占锁
7. 方法（除LOCK接口外）
    1. 测试和调试。
        1. `getHoldCount`。查询当前线程对该锁的保持数目，此信息只用于测试和调试。0表示没有获取到锁
        2. `isHeldByCurrentThread`。查询当前锁是否由当前线程所持有
    2. 系统监控。
        1. `isLocked`。查询此锁是否有任何线程保持
        2. `getOwner`。返回当前拥有此锁的线程，也有可能为空
        3. `hasQueuedThreads`/`hasQueuedThread`/`getQueueLength`/`getQueuedThreads`。查询等待锁的线程队列信息
        4. `hasWaiters`/`getWaitQueueLength`/`getWaitingThreads`。根据条件查询等待线程的信息
8. ReentrantReadWriteLock与ReentrantLock的方法类似
9. RRW当线程获取到写锁后，可以降级为读锁
### 五、 StampedLock（1.8）

1. 功能锁，基于三种模式来控制读/写访问，锁状态基于版本和模式控制，如版本返回0则表示无法获取访问权限，锁的释放和转换需要基于返回版本来控制，不匹配则失败。
2. 三种模式
   1. writting==>1。独占锁类似
   2. reading==>2。读锁类似
   3. Optimistic reading==>3（乐观读模式），乐观读锁在数据一致性上需要复制一个对象
3. 设计为在线程安全组件的开发中用作内部实用程序，采用序列锁的算法，而并非其他锁普遍采用的AQS。利用CLH队列进行线程的管理，通过同步状态值来表示锁的状态和类型。
4. 方法
   1. `tryConvertToReadLock`/`tryConvertToOptimisticRead`/`tryConvertToWriteLock`。锁切换

### 六、AbstractQueuedSynchronizer
1. 在JDK1.6后增加了独占锁功能
2. 一个框架，用于实现依赖于FIFO等待队列的阻塞锁和相关的同步器（semaphores、events等），为依赖单个原子int值表示状态的同步器提供有用的基础
3. 状态字段的同步操作依赖`getState`、`setState`和`compareAndSetState`的原子操作来更新int值
3. 子类应定义为非公共内部帮助类来实现其封闭类的同步属性方法
4. 默认支持互斥模式或共享模式，等待线程共享FIFO队列
5. ConditionObject类由支持独占模式的子类用作Condition实现
6. 用法（使用`getState`、`setState`、`compareAndSetState`检查和修改同步状态），只支持实现以下方法【线程安全】
    1. tryAcquire。排他获取锁
    2. tryRelease。排他释放锁
    3. tryAcquireShared。共享获取锁
    4. tryReleaseShared。共享释放锁
    5. isHeldExclusively。是否为排他状态
7. *核心*
   1. CLH队列【JSR-166】。
        ![image](./CLH.png)
        1. 一个**FIFO双向队列**，队列中每个节点等待前驱节点释放共享状态（锁）被唤醒就可以了
        2. 从tail入队【原子操作】，head出队【原子操作】
        3. `prev`链用于处理取消
        4. `next`链来实现阻止机制，每个节点的线程ID保留在主机的节点上，则前任通过遍历下一个链接来确定自己是哪个线程。
        5. 在构造节点时，设置头和尾指针。
        6. 节点状态标识
            1. CANCELLED[1]。后驱节点被中断或超时，需要移出队列。在同步队列中等待超时或被中断，需要从队列中取消等待，在该状态将不会变化
            2. SIGNAL[-1]。后驱节点被阻塞了。后继节点地线程处于等待状态，当前节点释放获取取消同步状态，后继节点地线程即开始运行
            3. CONDITION[-2]。Condition专用
            4. PROPAGATE[-3]。传播，适用于共享模式，下一次共享式同步状态获取将会无条件地被传播下去
            5. IINITAL[0]。初始值
   2. 方法
      1. `getState`。同步返回当前的值
      2. `setState`。同步修改当前值
      3. `compareAndSetHead`/`compareAndSetState`/`compareAndSetTail`/`compareAndSetWaitStatus`/`compareAndSetNext`。使用unsafe类来实现原子操作
      4. `enq`。插入队列
    


### 七、AbstractOwnableSynchronizer（1.6）
> 基础类，为AQS提供了独占锁等概念。包含定义拥有独占访问权限的锁

### 八、LockSupport
1. 创建锁和其他同步类的基本线程阻塞原语
2. 重点关注`park`方法和`unpark`方法，不能累加许可（与Semaphore不同）
3. 方法（都依赖unsafe包中对应的方法来进行控制）
   1. `park`。禁止当前线程被调度除非得到许可，使用unsafe.pack包来实现，响应中断但不抛出异常
   2. `unpark`。为给定的线程提供许可
   3. `getBroker`。返回提供给最近调用尚未解除阻塞的park方法的阻止程序对象，如果不阻止则返回null。 返回的值只是一个瞬间的快照 - 线程可能已经被阻止或阻止在不同的阻止对象上。
## synchronized

1. 提供对与每个对象相关联的隐式监视器锁的访问，但强制所有锁获取和释放以块结构的方式发生
2. 当获取多个锁时，它们必须以**相反的顺序**被释放，并且所有的锁都必须被释放在与它们相同的词汇范围内。


## 资料整理

1. [jdk8 内存模型](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4)
2. [java se8 中文版](https://www.matools.com/api/java8)
3. [史上最全的 Java 并发系列](https://juejin.cn/post/6844904047305031693)
4. [JUC 同步队列](https://segmentfault.com/a/1190000018948010)
5. [JUC 同步队列](https://segmentfault.com/blog/ressmix_multithread?page=1)

---
title: java concurrent atomic
date: "2021-03-21"
spoiler: atomic
---

# atomic 包

## unsafe 类

1. 封装了直接对内存管理、操纵对象、阻塞/唤醒线程等操作
2. native 方法，需要通过 CAS 原子指令完成
3. 类加载器
   1. `Bootstrap类加载器`。主要加载的是 JVM 自身需要的类
   2. `Extension ClassLoader扩展类加载器`。负责加载 ext 目录下的类库
   3. `Application ClassLoader系统类加载器`。负责加载用户类路径所指定的类
4. `compareAndSwapInt`中的偏移量字段是为了更方便的查找对象

## AtomicInteger/AtomicBoolean/AtomicLong 类

> 三个类只是操作的类型不一样，原理上一致

1. 初始化

```java
static {
        try {
            // value偏移量值的获取
            valueOffset = unsafe.objectFieldOffset
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
```

2. 特殊方法
   - `lazySet`。通过共享变量来改变值，与普通操作变量的方式类似。适用于加锁的场景，可以减少不必要的内存屏障

## AtomicReference

1. 引入类似乐观锁的方式操作共享资源，而非使用锁的方式（悲观）
2. 提供了以无锁方式访问共享资源的能力，以自旋和 CAS 的方式来解决共享变量的线程安全问题
3. 比较的是对象的引用

```java
class Task implements Runnable {
    private AtomicReference<Integer> ref;

    Task(AtomicReference<Integer> ref) {
        this.ref = ref;
    }

    @Override
    public void run() {
        for (; ; ) {    //自旋操作
            Integer oldV = ref.get();
            if (ref.compareAndSet(oldV, oldV + 1))  // CAS操作
                break;
        }
    }
}
```

## AtomicStampedReference

> 加了版本号的`AtomicReference`，可以用来解决 CAS 的 ABA 问题

## AtomicMarkableReference

> 能识别引用变量是否被更改过，使用**boolean**来标识是否有改过，功能与 AtomicStampedReference 类似

## AtomicIntegerArray/AtomicLongArray/AtomicReferenceArray

1. 以原子的方式操作数组中的元素
2. 初始化

```java
static {
        // 获取数组中每个元素的占用内存大小
        int scale = unsafe.arrayIndexScale(int[].class);
        if ((scale & (scale - 1)) != 0)
            throw new Error("data type scale not a power of two");
        // 从最左边起，连续0的个数
        shift = 31 - Integer.numberOfLeadingZeros(scale);
    }
```

3. 计算公式
   ![image](./atomicIntegerArray.png)

## LongAddr/（1.8）

1. 多线程唤醒下会比 AtomicLong 的吞吐量高，典型的以空间换时间
2. 实现思路。**分散热点**，将 value 值分散到一个数组中，不同线程会命中到数组的不同槽中，各个线程只对自己槽中的那个值进行**CAS**操作，减小冲突概率。最后再将值累加起来；只能每次对给定的整数执行一次加法
3. `LongAccumulator`。实现思路与`LongAddr`类似，区别在于可实现任意函数操作
4. 资料

- [Java 多线程进阶（十七）—— J.U.C 之 atomic 框架：LongAdder](https://segmentfault.com/a/1190000015865714)
- [atomicLong vs LongAddr](http://blog.palominolabs.com/2014/02/10/java-8-performance-improvements-longadder-vs-atomiclong/)

## 资料

1. [atomic](https://segmentfault.com/a/1190000015831791)
2. [unsafe 包说明](https://blog.csdn.net/aesop_wubo/article/details/7537278)

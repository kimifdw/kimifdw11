---
title: netty server
date: '2020-11-09'
spoiler: netty channel, 源码学习
---
## channel源码
- 简介到网络套接字或IO操作的组件，如读、写、连接和绑定。类图如下：
![image](./channel-4.1.53.png)
1. `Unsafe`。用于数据传输
1. `AbstractNioChannel`。NIO的基本操作
1. 通过`pipeline`来进行事件操作和流转。

## ChannelPipeline
> 拦截器，维护ChannelHandler列表，类似于servlet和filter的关系
### 创建
- 每个通道都有自己的管道，它会在创建新通道时自动创建
### 特性
1. 支持运行时动态添加或删除`ChannelHandler`,线程安全
### 流程图如下
![image](./channel-pipline-flow.png)
1. `ChannelInboundHandler`。监控Channel状态变化，API如下图所示：
![image](./ChannelInboundHandler-api.png)
1. `ChannelOutboundHandler`。拦截IO事件，API如下图所示：
![image](./ChannelOutboundHandler-api.png)

## Eventloop
- 处理注册在channel上的所有IO操作，事件循环器【单线程】,简单流程图如下：
![image](./eventloop.png)
1. `EventExecutor`。事件执行器，负责处理事件
1. `EventExecutorGroup`。维护了一个EventExecutor链表，继承了ScheduledExecutorService，execute方法通过next方法选择一个EventExecutor，并调用EventLoop#execute处理事件
1. `EventloopGroup`。负责调度Eventloop
1. `NioEventloop`。
    - 启动流程如下图：
    ![image](./NioEventLoop-start.png)
    - 实例化流程图如下：
    ![image](./NioEventLoop-sequence.png)
    - 与`Channel`的关联图如下：
    ![image](./NioEventLoop-Channel.png)
## ByteBuf
### ByteBuf简介
1. 创建buffer使用Unpooled或Pooled。
1. `discardable bytes`。无效空间，可丢弃字节的区域
1. `readable bytes`。内容空间，可读字节的区域，由readerIndex和writerIndex指针控制
1. `writable bytes`。空闲空间，可写入字节的区域，由writerIndex指针和capacity容量控制
1. 索引顺序存储。
![image](./Sequential-Access-Indexing.png)
1. Discardable bytes。回收未使用区域如下图所示：
![image](./discardReadBytes-flow.png)
1. 清除缓存索引。
![image](./clearing-index.png)
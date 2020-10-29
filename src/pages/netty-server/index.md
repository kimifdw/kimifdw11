---
title: netty server
date: '2020-10-28'
spoiler: netty channel, 源码学习
---
## channel源码
- 简介到网络套接字或IO操作的组件，如读、写、连接和绑定。类图如下：
![image](./channel-4.1.53.png)
1. `Unsafe`。数据传输
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

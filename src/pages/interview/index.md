---
title: 面试题收集
date: '2020-10-29'
spoiler: linux
---

## LINUX
- **Q1** 使用Linux epoll模型的LT水平触发模式，当socket可写时，会不停的触发socket可写的事件，如何处理？
- **A1** 向socket写数据时直接调用send()发送，当send()返回错误码EAGAIN，才将socket加入到epoll，等待可写事件后再发送数据，全部数据发送完毕，再移出epoll模型，改进的做法相当于认为socket在大部分时候是可写的，不能写了再让epoll帮忙监控。上面两种做法是对LT模式下write事件频繁通知的修复，本质上ET模式就可以直接搞定，并不需要用户层程序的补丁操作。

## NETTY
- **Q1** Why do we really need multiple netty boss threads?
- **A1** multiple boss threads are useful if we share NioEventLoopGroup between different server bootstraps
[stackoverflow](https://stackoverflow.com/questions/34275138/why-do-we-really-need-multiple-netty-boss-threads)

## java
- **Q1** Java nio在Linux系统下的epoll空轮询问题, select轮询事件返回数量为0，NIO照样不断的从select本应该阻塞的`Selector.select()/Selector.select(timeout)`中`wake up`出来，导致**CPU 100%**问题
- **A1** 升级到高版本的jdk或者使用netty来实现
[java bug](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6670302)

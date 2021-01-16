---
title: 面试题收集
date: "2020-10-29"
spoiler: linux
---

## LINUX

- **Q1** 使用 Linux epoll 模型的 LT 水平触发模式，当 socket 可写时，会不停的触发 socket 可写的事件，如何处理？
- **A1** 向 socket 写数据时直接调用 send()发送，当 send()返回错误码 EAGAIN，才将 socket 加入到 epoll，等待可写事件后再发送数据，全部数据发送完毕，再移出 epoll 模型，改进的做法相当于认为 socket 在大部分时候是可写的，不能写了再让 epoll 帮忙监控。上面两种做法是对 LT 模式下 write 事件频繁通知的修复，本质上 ET 模式就可以直接搞定，并不需要用户层程序的补丁操作。

## NETTY

- **Q1** Why do we really need multiple netty boss threads?
- **A1** multiple boss threads are useful if we share NioEventLoopGroup between different server bootstraps
  [stackoverflow](https://stackoverflow.com/questions/34275138/why-do-we-really-need-multiple-netty-boss-threads)

## java

- **Q1** Java nio 在 Linux 系统下的 epoll 空轮询问题, select 轮询事件返回数量为 0，NIO 照样不断的从 select 本应该阻塞的`Selector.select()/Selector.select(timeout)`中`wake up`出来，导致**CPU 100%**问题
- **A1** 升级到高版本的 jdk 或者使用 netty 来实现
  [java bug](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6670302)

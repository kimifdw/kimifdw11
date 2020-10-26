---
title: java nio selector
date: '2020-10-26'
spoiler: reactor模型
---

# reactor模型
## 单线程reactor模型
![image](./single-thread-reactor.png)
### java nio support
1. `Channels`。连接文件、socket等支持非阻塞读取
1. `Buffers`。表示直接从`channels`中读取或写入的数组对象
1. `Selectors`。表示哪些`Channel`集合存在IO事件
1. `SelectionKeys`。用于维持IO事件的状态和绑定关系

## 多线程单reactor模型
![image](./worker-thread-pool-reactor.png)
1. 工作线程。reactors需要快速触发handlers,非IO处理转移到其他线程上

## 多线程多reactor模型
![image](./multiple-reactors.png)
1. 由主acceptor分配到其他reactor去处理

# nio其他功能
1. 支持每个reactor包含多个selectors
1. 文件转移。自动化从文件到网络或网络到文件的拷贝
1. 内存文件映射。利用`buffers`来处理文件
1. `Direct buffers`。
    1. 支持零拷贝；
    1. 在开启或结束时有开销；
    1. 用于长连接。
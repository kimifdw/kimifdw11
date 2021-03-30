---
title: redis 问题
date: "2021-03-25"
spoiler: redis question
---

# 缓存问题（Redis）

## 缓存踩踏

### 定义

多个线程试图并行访问缓存，如缓存的值不存在，则线程将同时尝试从数据源获取数据

### 核心问题

竞态条件——多个线程争夺共享资源

### 解决方案

1. 增加更多的缓存
2. 使用缓存异步 Promise 的模式来处理缓存
3. 预先重计算【不推荐】。在缓存键正式过期前，重新计算缓存值并延长过期时间
   - 概率性预先重计算。`currentTime-(timeToCompute*beta*log(rand())) > expiry`。每当线程从缓存中获取数据时，都会执行这个算法。如果返回 true，那么该线程将重新计算这个缓存值。离过期时间越近，这个算法返回 true 的几率就会显著增加
   1. currentTime 是当前时间戳。
   2. timeToCompute 是重新计算缓存值所花费的时间。
   3. beta 是一个大于 0 的非负数，默认值为 1，是可配置的。
   4. rand()是一个返回 0 到 1 之间随机数的函数。
   5. expiry 是缓存值未来被设置为过期的时间戳。
4. 回路断路器
   - 将一个受保护的函数调用封装在一个断路器对象中，断路器对象负责监控故障，一旦故障达到某一阈值，断路器就跳闸，所有对断路器的进一步调用都返回错误，根本调用不到受保护的函数。

### 资料

1. [缓存踩踏：Facebook 史上最严重的宕机事件分析](https://www.infoq.cn/article/Bb2YC0yHVSz4qVwdgZmO)
2. [概率性预先重计算论文](https://cseweb.ucsd.edu/~avattani/papers/cache_stampede.pdf)

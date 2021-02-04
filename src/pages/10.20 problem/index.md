---
title: 1020随笔
date: "2020-10-20"
spoiler: zookeeper,uuid,question
---

# zookeeper 问题

1. 问题点![image](./zookeeper-flink.png)
1. 解决方案：不是 zookeeper 问题。
   原因：因为在 flink 或 kafka 向 zookeeper 获取对应路径的时候，zookeeper 发现自身并没有创建这条路径，就会返回以上的错误。其实可以看到这条记录在 zookeeper 日志中是 INFO，并不是 error，所以对应的 flink 或 kafka 会过滤掉这些错误

[stackoverflow 中的解答](https://stackoverflow.com/questions/43559328/got-user-level-keeperexception-when-processing)

# uuid 描述记录

![image](./uuid.png)

# 雪花算法原理

> 雪花算法。基于 64bit 的 long 型的数字

## 构成

- 1bit。无用
- 41bit。毫秒数【时间戳，2^41-1，即 69 年的时间】
- 10bit。工作机器 ID【5bit，机房 ID，2^5 个机房；5bit，机器 ID，2^5 个机器】
- 12bit。序列号【2^12-1】

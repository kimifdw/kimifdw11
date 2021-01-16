---
title: flink笔记（1）
date: "2020-10-21"
spoiler: flink
---

## FLINK 压测记录

### 延时定义

自定义 Source 源解析中加入 Gauge 类型指标埋点，汇报如下指标：

1. 记录最新的一条数据中的 event time，在汇报指标时使用当前系统时间 - event time。
1. 记录读取到数据的系统时间 - 数据中的 event time，直接汇报差值。
   `delay = 当前系统时间 – 数据事件时间 (event time)`
   > 说明：反应处理数据的进度情况。

`fetch_delay = 读取到数据的系统时间 - 数据事件时间 (event time)`

> 说明：反应实时计算的实际处理能力。

## FLINK 误用之痛

1. 是否在乎数据的丢失，不在乎可以不设 checkpoint
1. 是否在乎结果的准确性【可考虑 at-least-once】，sink 端处理数据重复问题
1. 为算子设置 streamApi 的 uid
1. 不要修改 key 的类型
1. 资源评估，事件时间和乱序问题
1. 只能选择 DataStreamAPI
1. 在升级过程中要改变状态
1. 不能丢失迟到的数据
1. 在运行时更改程序的行为
1. 使用 reinterpretAsKeyedStream：避免面对相同的 key 进行多次 shuffle
1. 数据类型，选择尽可能简单的状态类型
1. 序列化：尽量使用 POJO 和 Avro
1. 先进行过滤和投影操作
1. 并发性
1. 产线发布。尽量不使用 rocksdb 状态后端

## 参考资料

1. [Apache Flink 误用之痛](https://zhuanlan.zhihu.com/p/149147527)

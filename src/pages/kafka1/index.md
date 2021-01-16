---
title: kafka原理
date: "2020-11-25"
spoiler: 原理
---

## 消息中间件好处

1. 解耦
1. 异步
1. 削峰

## 消息队列的通信模式

1. 点对点。基于**拉取**或者**轮询**的消息传送模型，发送到队列的消息被一个且只有一个消费者进行处理
   ![image](./msg-model1.png)
1. 发布订阅
   ![image](./msg-model2.png)

## kafka 架构

![image](./kafka-architecture.png)

1. 消费者组。同一个分区的数据只能被消费者组中的一个消费者消费；同一个消费者组的消费者可以消费同一个 topic 的不同分区的数据
1. producer 采用发送 push 的方式将消息发到 broker 上，broker 存储后。由 consumer 采用 pull 模式订阅并消费信息
1. partition 是文件，支持多个副本

## 优点

1. 顺序读写磁盘。
   1. 顺序读写。按记录的逻辑顺序进行读、写操作的存取方法，即按照信息在存储器中的实际位置所决定的顺序使用信息，不容易删除数据。 2.在 Partition 末尾追加
2. MMAP 内存映射文件。直接利用操作系统的 Page 来实现文件到物理内存的直接映射，完成映射后对物理内存的操作会被同步到硬盘上。
   1. 原理。可以像读写硬盘一样读写内存（逻辑内存），不必关心内存的大小
3. 零拷贝。所有数据通过 DMA（直接内存访问）来进行传输，没有在内存层面去复制数据
4. 数据批量处理

## 工作流程分析

### 发送数据

![image](./sendMsg.png)

- 消息写入 leader 后，follower 是主动的去 leader 进行同步。
- producer 采用**push 模式**将数据发布到 broker，每条消息追加到分区中，顺序写入磁盘，保证同一分区内的数据是**有序**的。
  ![image](./writeMsg.png)

1. 写入原则
   - 按指定 partition 写入
   - 按 key 的 hash 值算出 partition 写入【消息被均匀的分布到不同的 partition 中，才能实现了水平扩展】
   - 没有设置 key，轮询选出一个 partition
2. 消息可靠性保证
   - 0。不需要等到集群返回，不能保证消息发送成功。
   - 1。只要 leader 应答就可以发送下一条，只确保 leader 发送成功
   - all[-1]。需要所有 follower 都完成 leader 的同步才会发送下一条，确保 leader 发送成功和所有的副本完成备份
3. CP【一致性和分区容错性】配置
   ```sh
   request.required.acks=-1
   min.insync.replicas = ${N/2 + 1}
   unclean.leader.election.enable = false
   ```
   AP【可用性和分区容错性】配置
   ```sh
   request.required.acks=1
   min.insync.replicas = 1
   unclean.leader.election.enable = false
   ```

### kafka 消息备份和同步

1. 根据分区的多副本策略来解决消息的备份问题
1. 名词解释
   1. ISR : leader 副本保持一定同步的 follower 副本, 包括 leader 副本自己，叫 In Sync Replica，最终会反馈到 zookeeper 上。
   1. AR: 所有副本 (replicas) 统称为 assigned replicas, 即 AR
   1. OSR: follower 同 leader 同步数据有一些延迟的节点
   1. HW: Highwater, 俗称高水位，它表示了一个特定的消息偏移量(offset), 在一个 parttion 中 consumer 只能拉取这个 offset 之前的消息(此 offset 跟 consumer offset 不是一个概念) ；
   1. LEO: LogEndOffset, 日志末端偏移量, 用来表示当前日志文件中下一条写入消息的 offset；
   1. leader HW: 该 Partititon 所有副本的 LEO 最小值；
   1. follower HW: min(follower 自身 LEO 和 leader HW)；
   1. Leader HW = 所有副本 LEO 最小值；
   1. Follower HW = min(follower 自身 LEO 和 leader HW)。

### 保存数据

> 顺序写入的方式将数据保存到磁盘

#### partition 结构

> 以文件夹的方式在服务器中存储

#### message 结构

1. offset。占 8byte 的有序 id 号，唯一确定每条消息在 partition 内的位置
1. 消息大小
1. 消息体。

#### 存储策略

1. 基于时间，默认 7 天；
1. 基于大小，默认 128MB;

### 消费数据

![image](./consumer-msg.png)

1. 点对点模式。由消费者主动去 kafka 集群拉取消息
1. 消费者组 consumer 的数量与 partition 的数量一致

#### 消费场景

1. AutoCommit（实际消息会丢）

```sh
    enable.auto.commit = true
    // 自动提交的时间间隔
    auto.commit.interval.ms
```

2. 手动 commit（at least once，消息重复，重启会丢）

```sh
    // oldest:topic里最早的消息，大于commit的位置，小于HW，也受到broker上消息保留时间和位移影响，不保证一定能消费到topic起始位置的消息
    sarama.offset.initial （oldest, newest）
    // 主题偏移量日志文的保留时长，默认设为1440s
    offsets.retention.minutes
```

#### 消费原理

1. 数据异步批量的存储在磁盘中，采用批量刷盘的做法进行，即按照一定的消息量和时间间隔进行刷盘。先存储到页缓存（Page cache）中，按照时间或者其他条件进行刷盘，或通过 fsync 命令强制刷盘。做不到不丢失消息，只能通过调整刷盘机制的参数缓解该情况
2. 通过 ack 的方式来处理消息丢失的情况

## 指令

1. 创建 topic

```sh
./kafka-topics.sh --create --topic dev2wx --replication-factor 1 --partitions 2 --zookeeper master:2181
```

2. 查看 topic

```sh
./kafka-topics.sh --list --zookeeper master:2181
```

3. 查看 consumer list

```
./kafka-consumer-groups.sh --bootstrap-server master:9092 --list
```

./kafka-topics.sh --list --zookeeper master:2181 4. 消费积压情况

```sh
./kafka-consumer-groups.sh --bootstrap-server master:9092 --describe --group logstash
```

## 资料

1. [简单理解 kafka 的消息可靠性](https://mp.weixin.qq.com/s/T6gCc8OBgyV-yeAg_MUzPQ)

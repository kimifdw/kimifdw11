---
title: redis数据集
date: "2021-03-12"
spoiler: redis
---

# 数据类型

## keys

1. 二进制安全，空字符串也是有效的键
2. 规则
    - 键不能太长。可以使用SHA1来对大值进行处理
    - 键也不能太短。

### 自动创建和删除键

1. 规则。
    - 添加到聚合数据类型时，如果目标键不存在，则在添加元素之前会创建一个空的聚合数据类型。
    - 删除聚合数据类型中的元素时，如果该值保持为空，则键将被自动销毁
    - 调用带空键的只读或写命令删除元素，总会产生与键保持空的聚合类型相同的结果

## expires

1. 到期时间分辨率始终为**1毫秒**

## 外部数据结构

### STRING（字符串）

1. 二进制安全，最大长度为512MB
2. 编码为int/raw/embstr
    - embstr。字符串对象的长度小于**44**字节，使用embstr对象。创建只会分配一次内存

### LIST（列表）

1. 根据插入顺序排序的字符串元素的集合，基于**双向链表**实现。插入或删除快，查找慢
2. `LPUSH`命令将新元素从列表的**头部**添加
3. `RPUSH`命令将新元素从列表的**尾部**添加
4. `LTRIM`命令限制列表可查看的数据量
5. 列表的最大长度是2^32-1个元素（一个列表多达40多亿的数据）
5. 编码为**ziplist**或**linkedlist**
    - ziplist。压缩链表，节省内存空间【数据量不能太大】
    - linkedlist。双向链表


### HASH（哈希）

1. 由与值相关联的字段组成的映射。字段和值都是字符串

### SET（集合）

1. 唯一、未排序的字符串元素的集合

### ZSET（排序集合）

1. 每个字符串元素都与一个浮点数字值相关联

### Bit arrays（位图）

1. 像位数组一样处理字符串

### HyperLogLogs（概率数据结构）

1. 用于估计集合的基数

### streams（流）

1. 抽象日志数据类型，仅追加map数据类型的集合

## 内部数据结构

### dict

### ziplist

## 资料

1. [Redis内部数据结构详解](http://zhangtielei.com/posts/blog-redis-dict.html)
2. [值得一看的35个Redis面试题总结](https://segmentfault.com/a/1190000022381177)
3. [redis文章](https://www.cnblogs.com/shoshana-kong/tag/redis/)
4. [redis官方文档](https://redis.io/topics/data-types-intro)
5. [redis官方文档译文](http://ifeve.com/redis-data-types/)
---
title: DDD随想
date: "2020-10-20"
spoiler: DDD
---

# 防腐层

## 目的

解决外部依赖频繁变更的情况下，如何将核心业务逻辑和外部依赖隔离

## Domain Primitive

> 定义。在一个特定领域里，拥有精准定义的、可自我验证的、拥有行为的 Value Object

### 问题分析角度

1. 接口的清晰度（可阅读性）
2. 数据验证和错误处理
3. 业务逻辑代码的清晰度
4. 可测试性。运行每个测试用例所花费的时间*每个需求所需要增加的测试用例数量
5. 可维护性。当依赖变化时，有多少代码需要随之改变
6. 可扩展性。做新需求或改逻辑时，需要新增/修改多少代码

### 解决方案

1. 将隐性的概念显性化
2. 将隐性的上下文显性化
3. 封装多对象行为

### DRY 原则

> 【don't repeat yourself】

### Single Responsibility 原则

> 单一功能原则。要求一个对象/类应该只有一个变更的原因

### 依赖反转原则

> 要求在代码中依赖抽象，而不是具体的实现

### 开放封闭原则

> 开放扩展，封闭修改


## REPOSITORY模式
### 数据模型（Data Model）

> 业务数据该如何持久化，以及数据之间的关系，即传统的ER模型

### 业务模型/领域模型（Domain Model）

> 业务逻辑中，相关联的数据该如何联动

### 模型对象规范
1. Data Object（DO、数据对象）：数据库表里的物理映射
2. Entity(实体对象)：正常的业务模型
3. DTO（传输对象）：入参和出参
4. DTO Assembler。将1个或多个相关联的Entitiy转化为1个或多个DTO
5. DTO Converter。
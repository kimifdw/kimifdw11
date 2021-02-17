---
title: OLAP笔记
date: "2021-02-16"
spoiler: OLAP
---

# 概念

## OLTP

> （Online Transaction Processing）在线事务处理，实时提供服务

## OLAP

> （Online Analytical Processing）在线分析处理，批量处理用户数据

## 行式存储

> 以数据行或实体为逻辑单元管理数据，数据行的存储是连续的

## 列式存储

> 以数据列为逻辑单元管理数据，相邻的数据都是具有相同类型的数据

# 列式存储

## 优势

1. 满足快速读取特定列的需求——按需读取
2. 就近存储同一列的数据，减少存储占用的磁盘空间——数据压缩

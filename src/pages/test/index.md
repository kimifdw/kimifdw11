---
title: 测试驱动
date: '2020-11-02'
spoiler: TDD
---
## 定义
> 测试在先，编码灾后，采用“红-绿-重构”三个步骤的循环往复。
1. 红：测试先行，现在还没有任何实现，跑UT的时候肯定不过，测试状态是红灯。编译失败也属于“红”的一种情况。
1. 绿：当我们用最快，最简单的方式先实现，然后跑一遍UT，测试会通过，变成“绿”的状态。
1. 重构：看一下系统中有没有要重构的点，重构完，一定要保证测试是“绿”的。
1. TDD比较适合用于输入输出很明确的CASE

## 特性
1. 隔离性。单元测试之间应该隔离，不要互相干扰

## 资料
1. [practical-test-pyramid](https://martinfowler.com/articles/practical-test-pyramid.html)
1. [中文翻译](http://insights.thoughtworks.cn/practical-test-pyramid/)
1. [测试资料](https://mp.weixin.qq.com/s/TjJ31yWTMwr4szz1JqtKcQ)
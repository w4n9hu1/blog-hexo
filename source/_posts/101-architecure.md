---
title: 什么是架构
description: 怎么写好可维护的代码？
categories:
  - 101
tags:
  - 架构
  - DDD
date: 2022-11-08 22:40:25
---

> 架构的目标应该是以低成本满足业务需求。—— 《架构整洁之道》

## 业务代码

{% asset_img core.png %}

无论是六边形架构，整洁架构还是DDD，程序的核心在于业务代码，业务逻辑应该与实现细节、交互隔离，并且不依赖于具体实现。

## 演进式架构

{% asset_img archi.png %}
数据库，服务拆分，分布式，DIP等具体架构细节应当等中后期更多信息后作出合理决策。

## 代码结构

{% asset_img value.png %}

健康的代码应该大都分布于core和services，这也是 UT 的核心覆盖区域。

## More

1. [The Clean Architecture - Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
2. [MonolithFirst - Martin Fowler](https://martinfowler.com/bliki/MonolithFirst.html)

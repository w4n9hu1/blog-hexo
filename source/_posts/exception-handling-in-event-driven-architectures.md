---
title: 事件驱动架构中的异常处理
description: 事件驱动架构中的异常处理最佳实践。
categories:
  - 架构
tags:
  - 微服务
  - 架构
  - 分布式
  - 设计模式
date: 2023-05-26 19:01:05
---

## Event-Driven System

{% asset_img event.png %}

## 1. Retry

当遇到一些简单的异常，比如Http请求超时，这时重试是最简单的机制，可以用一些第三方库，比如Polly，来处理重试逻辑。

## 2. Retry Queue

当遇到数据一致性问题，导致Porcessor无法立即处理时候，可以将消息丢到Retry队列，防止消息的阻塞。

重试达到一定次数的消息，也应该丢入到Retry队列。

## 3. Dead Letter Queue

当Retry队列中的消息再次被消费，仍旧发生未知的异常，应将消息丢入到Dead队列，人工分析找出原因。若Processor问题导致，等到问题修复后，再将Dead队列的数据丢入到Retry队列。

## 4. Drop

当遇到一些已知异常，比如消息格式异常，枚举超范围等，可以直接将消息丢弃。

{% asset_img event2.png %}
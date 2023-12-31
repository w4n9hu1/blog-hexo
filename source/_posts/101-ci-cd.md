---
title: 什么是持续交付
description: 真正的价值+小步快跑+好的自动化测试+持续监测与反馈。
categories:
  - 基础
tags:
  - DevOps
date: 2022-07-27 17:13:35
---

{% asset_img ci.png %}
《持续交付2.0》

全书围绕双环模型展开：

1. 价值探索
  1. 提问
  2. 锚定
  3. 共创
  4. 精炼
2. 价值验证
  1. 构建
  2. 运行
  3. 监测
  4. 决策

概述了软件工程中的一些概念和一些落地方法。

## 真正的价值

需求分析，拆解故事，要通过提问与讨论，找出本质问题，根据用户需求发现真正的需求，识别出价值指标。

## 小步快跑

包括软件架构的解耦，软件团队的职责边界清晰，小的需求迭代，才能持续探索，快速验证。

## 好的自动化测试

持续集成的基石，质量大于数量，好的自动化测试应该是快速的，耐重构，单元测试和集成测试比例合理。自动化测试应该避免缓慢，低质量，随机失败等坏味道。

## 持续监测与反馈

持续，完善的监测应该包括基础监测，应用监测与业务监测，完善的监测，日志可以及时发现问题，并快速定位问题，根据监测结果的反馈，还能够使业务与IT快速闭环。

一些企业往往忽视其中一些关键步骤，比如还停留在纯测试人员的手工测试，耗时且上线质量不稳定，比如忽视监控与反馈，导致问题排查困难，参与人员并不清楚自己所开发的功能是否产生了价值。
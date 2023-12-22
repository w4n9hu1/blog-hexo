---
title: 什么是kubernetes
description: 什么是kubernetes，传统行业是否需要kubernetes？
categories:
  - 基础
tags:
  - DevOps
  - Docker
date: 2022-07-24 15:31:38
---

## 为什么需要kubernetes

1. 传统单体项目逐渐拆分为微服务，应用部署愈加难以维护
2. Docker等容器化部署，及云服务的流行
3. 业务的发展带来的持续部署/交付，弹性扩容的需求

## 什么是kubenetes

1. 抽象计算机硬件为单一的计算资源
2. 自动部署，扩展和管理容器化应用

## 传统行业是否需要kubernetes

一些疑问：

1. 用户结构，使用场景单一的传统企业应用是否有必要拆分为 10+ 数量级的微服务？带来额外的开发，运维成本
2. 小百万甚至十万级的数据是否真的有瓶颈？有的话也应该是在数据库，而不是应用服务
3. 真正需要水平扩展的是数据库服务，一些教程中的最佳实践，stateful的应用并不推荐使用kubernetes

## Overview

{% asset_img kubernetes.png %}



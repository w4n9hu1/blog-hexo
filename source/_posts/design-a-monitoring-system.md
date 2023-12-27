---
title: 设计一个监控系统
categories:
  - 系统设计
tags:
  - Monitoring
date: 2023-12-22 14:31:29
---

## 需求

当业务数据存在异常，比如某个订单超时未处理，需要及时通知相关人员。

## 初步设计

创建一个定时任务，定时查询数据库，如果有超时订单，就给相关人员发送通知。

{% asset_img step1.png %}
配置文件：
``` json
{
  "sqls":[{"timeout_orders":"select ..."}],
  "subscription":["username"]
}
```
代码：
``` c#
public void Run()
{
  var timeoutOrdersCount = _db.Query(sql);
  if(timeoutOrdersCount > 0){
    Alert(user);
  }
}
```

## 模块划分

当业务监控需求增多，以及查看历史异常等功能需求，需要对系统进行拆分和进一步设计。

> version 1
{% asset_img step2.png %}

可以将系统拆解为以下几个模块：

- 基础数据：管理相关配置数据，例如监控的服务端点，数据收集的计划，异常指标配置，通知订阅用户等。
- 数据收集：从不同来源收集业务指标数据。
- 数据分析：分析收集的指标数据。
- 数据存储：存储收集到的指标数据。
- 告警通知：通过不同通信渠道通知相关人员。
- 可视化：展示和分析指标的历史数据。

> version 2
{% asset_img step3.png %}

## 进一步优化

当系统应用规模逐步增大，为了减轻数据处理的压力，可以加入消息队列系统。
数据量达到一定规模，可以单独出来一个查询服务，并引入缓存。

> version 3
{% asset_img step4.png %}

## 开源方案

> prometheus architecture

{% asset_img architecture.svg %}
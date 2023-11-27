---
title: 使用Docker/Kafka/.Net6搭建流处理系统
description: 一个使用Docker、Kafka、.Net6搭建，基于事件驱动的Demo。
categories:
  - 架构
tags:
  - Docker
  - Kafka
  - .Net
date: 2022-10-24 13:18:52
---

> 基于事件流处理的微服务架构可以使程序有更低的耦合度，更高的吞吐量和更好的扩展性，本文将使用Docker安装Kafka环境，并基于.Net6搭建一个WebApi的Producer和一个控制台Consumer程序。

## 一、安装Apache Kafka

使用[Docker-Compose](https://docker.easydoc.net/doc/81170005/cCewZWoN/IJJcUk5J)安装Apache Kafka只需要简单2个步骤。
1. 下载`docker-compose.yml`文件，`curl --silent --output docker-compose.yml https://raw.githubusercontent.com/confluentinc/cp-all-in-one/6.1.1-post/cp-all-in-one/docker-compose.yml`
2. 在docker-compose.yml所在目录运行`docker-compose up -d`，等待镜像拉取和运行。

{% asset_img docker-compose.png %}

## 二、使用Confluent Control Center

[Confluent Control Center](https://docs.confluent.io/platform/current/control-center/index.html)是一个基于Web，管理和监控Apache Kafka的工具，根据docker-compose.yml相关配置，打开http://localhost:9021/clusters。

{% asset_img control-center.png %}

## 三、创建基于WebApi的生产者

1. 基于.Net6创建一个ASP.NET Core Web API项目和一个控制台应用项目。
{% asset_img projects.png %}

2. 安装Confluent.Kafka和Newtonsoft.Json Nuget包。
{% asset_img nuget.png %}

3. 新建KafkaProducer类，注入`IProducer<Null, string> _producer`。

``` C#
 public class KafkaProducer : IKafkaProducer
    {
        private readonly IProducer<Null, string> _producer;

        public KafkaProducer(IProducer<Null, string> producer)
        {
            _producer = producer;
        }

        public async Task ProduceAsync(Order order)
        {
            await _producer.ProduceAsync("order-topic",
               new Message<Null, string>
               {
                   Value = JsonConvert.SerializeObject(order)
               });
        }
    }
```

4. Program.cs类中注入`IProducer`和`IKafkaProducer`。

``` C#
var kafkaConfig = new ProducerConfig { BootstrapServers = "localhost:9092" };
builder.Services.AddSingleton<IProducer<Null, string>>(x =>
new ProducerBuilder<Null, string>(kafkaConfig).Build());

builder.Services.AddSingleton<IKafkaProducer, KafkaProducer>();
```

## 四、创建基于控制台的消费者

创建Consumer。

``` C#
var config = new ConsumerConfig
{
    GroupId = "order-consumer-group",
    BootstrapServers = "localhost:9092",
    AutoOffsetReset = AutoOffsetReset.Earliest
};

using (var consumer = new ConsumerBuilder<Null, string>(config).Build())
{
    consumer.Subscribe("order-topic");
    CancellationTokenSource token = new CancellationTokenSource();
    try
    {
        while (true)
        {
            var response = consumer.Consume(token.Token);
            if (response.Message != null)
            {
                var order = JsonConvert.DeserializeObject<Order>(response.Message.Value);
                Console.WriteLine($"OrderId: {order.OrderId} OrderCode: {order.OrderCode}");
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message); ;
    }
}
```
## 五、运行测试

运行起来2个项目，并向/Order接口Post测试数据，可以看到Consumer应用实时接收到相关消息。
Producer
{% asset_img producer.png %}
Consumer
{% asset_img consumer.png %}
打开Confluent Control Center，可以看到相关Topic和消息。
{% asset_img topic.png %}

[GitHub](https://github.com/w4n9hu1/KafkaDemo)

## 引用

1. [Docker快速入门](https://docker.easydoc.net/doc/81170005/cCewZWoN/lTKfePfP)

---
title: 使用DDD方法论搭建一个项目
description: 从零搭建一个.net core的项目，探索DDD落地的最佳实践。
categories:
  - 架构
tags:
  - DDD
  - 架构
date: 2023-03-12 15:42:11
---

## **主要思想：业务逻辑，领域逻辑**

DDD聚焦于业务，业务逻辑落地到代码，主要关注两层，Application Layer和Domain Layer。
分别对应着测试用例（use cases）和领域逻辑（domian logic）。

{% asset_img business_logic.png %}

## **核心概念：实体，聚合，应用服务**

1. 实体：有独立标识，包含领域逻辑的领域对象。
2. 聚合：相关的一系列实体。
3. 仓储接口：隐藏数据持久化细节的接口。
4. 应用服务：实现用例的服务，和展示层以Dto进行交互。
5. ...

## **DDD落地**

[DDD-DEMO](https://github.com/w4n9hu1/order-service-core)

{% asset_img architecture.png %}

*虚线箭头和理论有所区别，因为webapi层实际在解决方案中还承担着host的作用。*

### Webapi

很薄的一层，主要负责依赖注入和控制器，使用Dto和Application层交互。

``` C#
[HttpPost]
public async Task<ActionResult> CreateAsync(OrderCreationDto orderCreationDto)
{
    _logger.LogInformation("Create {@orderCreationDto}", orderCreationDto);
    await _orderService.CreateAsync(orderCreationDto);
    return Ok();
}
```

### Application

包含Contracts,Mappers和Application services，基于Domain层实现用例逻辑。

``` C#
public async Task<OrderDto> CreateAsync(OrderCreationDto orderCreationDto)
{
    var order = await orderManager.CreateAsync(orderCreationDto.OrderCode);
    order.OrderItems = orderCreationDto.OrderItems.Select(i => new Domain.Order.OrderItem(i.CommodityId, i.CommodityName, i.Amount)).ToList();
    order.CreatedBy = orderCreationDto.CreatedBy;
    order.Weight = orderCreationDto.Weight;

    var savedOrder = await _orderRepository.InsertAsync(order);
    order.OrderId = savedOrder.OrderId;

    await distributedEventBus.PublishAsync(_mapper.Map<OrderChangedEto>(order));
    return _mapper.Map<OrderDto>(order);
}
```
*Use auto object mapping only for Entity to output DTO mappings.*

### Domain

主要的业务逻辑，包括实体，值对象，仓储接口等，复杂逻辑可添加领域服务。

``` C#
public class Order : IAggregateRoot
{
    public int OrderId { get; set; }

    public string OrderCode { get; set; }

    internal Order(string orderCode)
    {
        if (string.IsNullOrEmpty(orderCode))
        {
            throw new OrderException("OrderCode should have value!");
        }

        OrderCode = orderCode;
    }

    public Weight Weight { get; set; }

    public List<OrderItem> OrderItems { get; set; } = new List<OrderItem>();

    public int TotalAmount
    {
        get
        {
            return OrderItems.Sum(i => i.Amount);
        }
    }

    public int CreatedBy { get; set; }

    public DateTimeOffset CreatedTime { get; set; } = DateTimeOffset.Now;

    public void AddOrderItem(OrderItem orderItem)
    {
        OrderItems.Add(orderItem);
    }
}
```

### Infrastructure

接口的具体实现，比如持久化数据库，发送Event等。

``` C#
public class KafkaEventBus : IDistributedEventBus
{
    public Task PublishAsync<T>(T message)
    {
        throw new NotImplementedException();
    }
}
```

1. [Implementing Domain Driven Design - ABP](https://abp.io/books/implementing-domain-driven-design)
2. [领域驱动设计](https://book.douban.com/subject/26819666/)
3. [实现领域驱动设计](https://book.douban.com/subject/25844633/)
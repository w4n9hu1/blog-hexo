---
title: 什么是单元测试
description: 《Unit Testing Principles, Practices, and Patterns》笔记，讲单元测试思想的一本书，侧重怎么去写一个好的单元测试，什么时候应该使用mock，涉及到一些架构相关知识。
categories:
   - 基础
date: 2022-04-30 15:49:58
tags:
  - UT
  - TDD
---
{% asset_img ut.jpg %}

*Unit Testing Principles, Practices, and Patterns* 

## 单元测试概述

单元测试的目标：使软件项目能够 可持续 的迭代，尤其是业务逻辑复杂，周期长的企业级应用。

好的测试应当有如下特征：
- 被集成进开发周期
- 聚焦核心领域部分
- 最小的维护成本和最大的价值

单元测试的定义：
- 验证一小块代码
- 快速执行
- 独立隔离运行

测试不应该去验证 代码单元 ，而应该是去验证 行为单元 ，对领域有意义的事情。

一个测试应该讲述一个可观测的，有意义的 故事。

{% asset_img figure2_3.png %}

London 和 Classical 风格的区别

| School    | Isolation of | A unit is                   | Uses test doubles for          |
| --------- | ------------ | --------------------------- | ------------------------------ |
| London    | Units        | A class                     | All but immutable dependencies |
| Classical | Unit tests   | A class or a set of classes | Shared dependencies            |

TDD Test-driven development, 在你的测试中 *重复* 3个步骤：

1. 写一个失败的测试，表明你需要添加的功能和如何表现。
2. 编写足够的代码去让测试通过，这一步骤，并不需要巧妙，干净的代码。
3. 在单元测试的保护下重构，保证代码的可读和可维护性。

## 单元测试的最佳实践

### AAA模式

```  C#
public class CalculatorTests 
{
  [Fact] 
  public void Sum_of_two_numbers() 
  {
    // Arrange
    // 准备待测试想系统（SUT）和它的依赖的期望状态。
    double first = 10; 
    double second = 20; 
    var calculator = new Calculator(); 
    // Act
    // 调用SUT的方法，传入准备好的参数，捕获输出结果。
    double result = calculator.Sum(first, second); 
    // Assert
    // 验证输出
    Assert.Equal(30, result); 
  }
}
```

### 命名

[MethodUnderTest]_[Scenario]_[ExpectedResult]
方法名_场景_期望结果

好的单元测试的四个特性

{% asset_img figure4_2.png %}

- 防止BUG的产生
- 耐重构
- 快速反馈
- 可维护

> 耐重构，要求代码注重 可观测行为 而非 实现细节，与具体实现解耦，相对于白盒测试，倾向于黑盒测试。

### Mock

{% asset_img figure5_2.png %}

Mock: 向外输出交互（outcoming）。
Stub: 向内输入数据（incoming）。

作者偏向只在集成测试中使用mocks，单元测试不应该使用。因为理想模式下，领域负责处理复杂，控制器负责处理交互。
使用第三方库应当编写自己的适配器，并且去mock这些适配器，而不是直接去mock。

### 六边形架构

{% asset_img figure5_9.png %}

- 领域层和应用服务层关注点分离
- 应用内部，服务层单向依赖领域层
- 应用间只通过服务层沟通

### 三种测试风格

- 基于输出
- 基于状态
- 基于交互

### 代码价值

{% asset_img figure7_1.png %}

基于复杂度领域价值，和合作者数量划分为4种类型。
其中左上的代码是最具有测试价值的部分。

使用 是否能执行/执行模式（CanExecute/Execute pattern） 来减轻控制器的复杂度。

## 集成测试

不符合单元测试任一条特性的测试都可以被称作集成测试，集成测试一般测试happy path。

外部依赖分为：
- 可控依赖(Managed dependencies)：只用于应用内部交互，典型的比如数据库。
- 不可控依赖(Unmanaged dependencies)：应用外部可观测的交互，比如第三方系统。
可控依赖使用真实实体，不可控依赖使用mocks。

系统应当尽可能减少分层，大多数系统可分为 domain model, service layer(controller), infrastructure layer。

> All problems in computer science can be solved by another layer of indirection, except for the problem of too many layers of indirection. — David J. Wheeler

## 测试反模式

1. 测试私有方法
2. 暴露私有状态
3. 泄露领域知识（实现细节）
4. mock具体的类

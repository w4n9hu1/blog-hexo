---
title: 什么是多线程编程
description: 什么是多线程编程，为什么会有线程安全问题。
categories:
  - 基础
tags:
date: 2022-12-22 20:26:04
---

## 什么是线程（Thread）

线程是运行在进程上下文中的逻辑流，每个线程都拥有自己的线程上下文，包括唯一的线程ID，通用目的寄存器等。多个线程运行在同一进程的上下文中，共享这个进程的虚拟地址空间。

可以基于线程创建并发逻辑流，实现并发编程。

在C#中，可以使用`Thread.CurrentThread.ManagedThreadId`显示出当前线程ID，使用 [Task.Run Method](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.run?view=net-6.0) 将耗时任务放到线程池中执行。

``` C#
Console.WriteLine(Thread.CurrentThread.ManagedThreadId);
var task1 = Task.Run(async () =>
{
    Console.WriteLine(Thread.CurrentThread.ManagedThreadId);
    await Task.Delay(1000);
});
var task2 = Task.Run(async () =>
{
    Console.WriteLine(Thread.CurrentThread.ManagedThreadId);
    await Task.Delay(2000);
});

Task.WaitAll(new Task[] { task1, task2 });
// output: 1 5 9
```
## 什么是线程安全

线程安全指在多线程环境中，公用变量能够被正确处理。

C#中使用 [Parallel.For Method](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.parallel.for?view=net-6.0) 编写并行代码：
``` C#
var count = 0;
Parallel.For(0, 10000, t =>
{
  count++;
});
Console.WriteLine(count);
// output: 6463
```
可以使用 [lock](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/lock) 来确保单个线程具有对线的独占访问权。
``` C#
var lockObject = new object();
var count = 0;
Parallel.For(0, 10000, t =>
{
    lock (lockObject)
    {
        count++;
    }
});
Console.WriteLine(count);
// output: 10000
```
此外，.Net中也提供了多个线程安全集合类 [System.Collections.Concurrent Namespace](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent?view=net-6.0)

## 为什么会有线程安全问题

代码中`count++`并非原子操作，大致分为3条指令：

1. 读：内存》寄存器
2. 执行：寄存器 `+1`
3. 写：寄存器》内存

如果多个线程同时执行，则可能发生：1读，2读，1执行，1写，2执行，2写。则执行后，count=count+1。


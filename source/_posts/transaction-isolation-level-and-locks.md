---
title: 事务隔离级别与锁
description: 事务和锁的基础概念。
categories:
  - 数据库
tags:
  - Database
date: 2022-10-15 14:41:55
---

数据库的事务隔离允许各事务单独执行好像没有并发一样。

## 事务隔离级别

按隔离级别由低到高可分为：

1. READ UNCOMMITTED 可以读取未提交的数据（脏读）。
2. READ COMMITTED 不可重复读，一般公司采用的隔离级别。
3. REPEATABLE READ MySQL默认隔离级别，可重复读。
4. SERIALIZABLE 串行化，最高隔离级别。

## 什么是锁

锁机制用于管理对共享资源的并发访问。

存储引擎提供了2种标准的行级锁：共享锁（S Lock） 和 排他锁（X Lock）。

| | READ | WRITE |
|---|---|---|
| READ UNCOMMITTED | NoLock | XLock |
| READ COMMITTED | MVCC | XLock |
| REPEATABLE READ | MVCC | XLock |
| SERIALIZABLE | SLock | XLock |

| | X | S |
|---|---|---|
| X | 不兼容 | 不兼容 |
| S | 不兼容 | 兼容 |

> MVCC，通过`transaction_id`进行多版本并发控制，InnoDB存储引擎通过读取快照数据极大的提高了数据库的并发性。

## 乐观锁与悲观锁

一种思想，乐观锁最后去判断数据是否被修改，适合冲突低的场景，性能好。悲观锁则是提前加锁，适合写多，冲突高的场景。


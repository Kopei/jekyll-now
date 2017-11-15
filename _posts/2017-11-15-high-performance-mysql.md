---
layout: post
title: 高性能mysql笔记
---

## 此笔记只针对mysql5.5

### mysql的架构
- mysql采用存储和处理分离的架构。
- mysql可以开启线程池，处理客户端的请求。
- select语句会在缓存中先查找，没有hit才会到解释器。
- 解释器创建内部数据结构并做优化，最后才到存储器API。优化决策时用户可以使用hint关键字来影响mysql的决策过程，也可能使用explain来看看mysql是怎么决策的。
优化器会请求存储引擎提供一些信息来帮助优化。

### mysql的并发控制
- mysql在服务层和存储层都做了并发控制， 一般使用锁来控制并发。
- 存储引擎有自己的锁策略和颗粒度，但是服务层的策略高于存储层。比如alter table 使用应用层的表锁，忽略存储层锁机制。
- 行锁只有在存储层实现。

### ACID一个事务的标准特征
- mysql主要关心两个隔离等级，Read commited 和 repeatable read。 read commited也是nonrepeatable read, 一个事务在commit之前对其他事务都是不可见的。
repeatable read是mysql默认隔离等级，保证同一个事务多次读取同样记录结果一致。
- 在多个事务里可能出现死锁现象, innoDB处理的方式是将最少行级排它锁进行回滚。

### 使用事务日志可以提高存储引擎修改表数据效率
- 做法类似redis, 仅仅持久化事务日志，在内存中更新数据，然后后台再慢慢写入磁盘。

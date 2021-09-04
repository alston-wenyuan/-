# 性能调优 - 容量评估


## 上节知识点回顾
### 1. QPS与TPS的区别

如果请求只是一个接口的话，那么QPS=TPS，一般接口的性能我们都用QPS(TPS与之相同)来衡量。

比如一个页面的性能的话就不能用QPS来衡量，因为一个页面上将有很过的请求，比如：js,css,数据接口请求等，所有的其你去都完成了才能算这个页面打开的事物完成。因此，这个时候一般都用TPS来衡量。

---

### 2. 并发与并行的区别

并发，指的是合理的分配CPU时间段来从逻辑上完成看似一起完成多个任务，实际上单核模式下，多个任务是被轮询执行一段时间的。

并行，指的是通过硬件来从物理上同时完成多个任务，比如CPU多核模式，在某一个时刻，每一个核都能处理不同的任务。

---

### 3. MySQL中通过DELETE + WHERE删除数据是真的删除吗？为什么？

mysql执行delete操作后，存储在硬盘上的数据没有被删除，只是在记录行上做了删除标记，即通过删除标识位实现。

因为移除它们之后，很多其他的记录需要在磁盘上重新排列，这样会消耗大量的性能，比如如果是一张大表，存在索引，删除了其中一行，那么整个索引结构就会发生变化，随之而来的是要改变索引结构，必将带来磁盘IO，很浪费性能。所有被删除的记录都会组成一个垃圾链表，这个链表中记录占用的空间叫做可重用空间，之后有新记录插入的时候可能把已删除记录占用的空间覆盖掉。

---


## 容量评估的步骤与方法

    并发量/数据量/带宽/CPU/内存/磁盘等
    
    以秒杀抢购活动为例，预估每场参与用户数将达到5W左右，主要涉及页面渲染、商品详情接口，订单详情接口，商品抢购接口等除此之外还需提供对外订单查询接口预计1000QPS；
    页面所有静态资源6.4MB，商品信息11.5KB，订单信息981B，所有数据均为原始大小没有进行压缩；
    活动开放时间为每日的早上10:00到10:10，下午14:00到14:10，持续一个月（实际抢购库存上午场2000个，下午场5000个）。
    以上诉需求为例，设计整体技术实现方案并手绘物理架构图，结合架构图进行完整链路的容量评估，架构图可参考以下例子并结合实际设计方案：
![](https://file.40017.cn/scyx/nce/test/8f63bf8a-eeb9-11eb-a550-66f90aa60c7d.png)

- ## 评估总访问量

  每场用户为5W，所以访问量未5W

- ## 评估平均访问量QPS

  5W*3/(10x60) = 250，因此平均访问量QPS为250

- ## 评估高峰QPS

  5W*3=15W，因此高峰QPS为150000

- ## 评估系统、单机极限QPS

  单机压测，秒杀商品服务器单机最高QPS，下单服务器单机最高QPS，全局计数器单机最高QPS，核心业务服务器单机最高QPS。

- ## 资源配置极端

  线上系统是不会跑满到极限的，根据每个节点最高QPS的80%来计算出节点集群需要的机器数量。

  根据以上计算出来的机器除外，每个节点建议多备一台机器以防万一。

---

## 常用服务器/中间件的核心评估指标

- 应用服务器
  
  - 每天请求量
  
  - 每次请求大小
  
  - 资源数量与配置
  
  - IO模型（BIO/NIO/AIO）
  
    - BIO ： 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
    - NIO ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
    - AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理，
  
  - GC收集器的选型和配置
  
    Serial收集器是单线程的收集器。
  
    ParNew收集器是Serial收集器多线程版本（是GC线程的多线程，并行）。
  
    Parallel Scavenge收集器是一个新生代收集器，使用复制算法的收集器，并行的多线程收集器。更关注吞吐量。
  
    Serial Old收集器是Serial收集器的老年代版本，是一个单线程收集器，使用“标记-整理”算法。
  
    Parallel Old是Parallel Scavenge收集器的老年代版本，是一个多线程收集器，使用“标记-整理”算法。
  
    CMS收集器（Concurrent Mark Sweep）是一种以获取最短回收停顿时间为目标的收集器，是基于“标记-清除”算法。
  
    G1（Garbage-First）收集器是一款面向服务端应用的垃圾收集器，保留新生代和老年代概念，但不再是物理隔离，是一部分Region的集合。
  
  - 内存大小分配
  
    程序计数器：是一块较小的内存空间，它可以看做当前线程所执行的字节码的行号指示器，在jvm中，虚拟机通过改变程序计数器的值来选取下一条需要执行的字节码的指令，分支，循环，跳转，异常处理，线程恢复等基础功能都需要依赖它。
  
    java虚拟机栈：是线程私有的，它的生命周期与线程相同。
  
    本地方法栈：与虚拟机栈的作用相似，它们之间的区别在于java虚拟机栈为虚拟机执行java方法（字节码）服务，而本地方法栈为虚拟机栈使用到的Native方法服务。
  
    Java堆：是在这部分内存中最大的一块，java堆是被所有线程共享的一块内存区域，这部分内存用于存放对象的实例，几乎所有对象的实例都在这里分配内存。java堆同时也是垃圾收集器管理的主要区域，因此很多时候也被称为“GC堆。
  
    方法区：是线程共享的内存区域，它用于存储已被虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等数据。
  
  - CPU核数配置
  
- 数据库

  当前数据容量、每天数据增量（预估容量）、每秒读峰值、每秒写峰值、事务量

  查询是否走索引、有没有大数据量的查询、有没有多表关联，关联是否用到索引、有没有使用悲观锁，是否可以改造成乐观锁，或者是否可以利用数据库内置行级锁、事务和一致性级别、使用的JDBC数据源类型，连接数等配置、是否开启JDBC诊断日志、有没有存储过程、伸缩策略（分区表，自然时间分表，水平分库分表）

  容灾策略、归档策略、读写分离策略、分库分表(分片)策略

  

- 缓存

  缓存内容的大小、缓存内容的数量、缓存内容的过期时间、缓存的数据结构、每秒读峰值、每秒写峰值

  是否有可能缓存穿透、是否有大对象、是否使用缓存实现分布式锁、是否使用缓存支持的脚本、缓存分片方法

  失效转移、持久策略、淘汰策略、线程模型、预热方法、分片Hash策略、复制模型

  

- 消息队列

  每天平均数据增量、消息持久的过期时间、每秒读峰值、每秒写峰值、每条消息的大小、平均延迟、最大延迟

  消费线程池模型、分片策略、消息的可靠投递

  复制模型、失效转移、持久策略

  

## 目前公司提供的服务器/中间件默认配置下最大承受能力（QPS、储存容量等）

- 应用服务器
    - 可选/默认CPU、内存等配置
    - 对应单机最大可承受QPS
    - 默认网络带宽等
    - ......
- 缓存
    - 内存默认3G
    - 超时时间默认5000毫秒
- 数据库
    - mysql默认2C4G
    - 默认QPS为5000
    - 默认磁盘50G
- 消息队列
    - 单机QPS是10W+

---

## 对目前负责的一个主要系统/模块按实际情况重新做容量评估（尽量结合实际架构图进行展开）

- 最大性能方案
    - 
- 最小资源方案
    - 

---
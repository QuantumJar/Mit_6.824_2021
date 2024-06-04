# 6.824 分布式系统

## Lecture 1 intro&MapReduce

### Q: 什么是分布式系统？

![](https://github.com/QuantumJar/Mit_6.824_2021/blob/main/asserts/2024-05-25%20173434.png?raw=true)

可以比较简单的概括为：分布式系统的主要特点是应用的各个服务节点（如服务器，数据中心，客户端）是通过网络相互连接的相互合作组成的一个系统。

### Q: 为什么需要分布式系统? 分布式系统因何出现？

1. 用于连接物理上隔离的机器
   - 比如上网课，学生通过电脑，ipad等设备通过网络上课，提供这种服务的app就是一种分布式系统的实现
2. 通过并行计算来提高性能
   - mapreduce 就是一个使用并行计算来提高性能的例子

3. 希望系统是**容错**的 (Fault Tolerance)
   - 因为计算机总有可能失效宕机，我们希望及时部分机器的失效不会影响服务本身的继续。

4. 安全性
   - 举了一个例子，对于用户登录服务，因为这个服务器中保存的信息有很高的安全性要求，可以把这个服务抽取出来部署到单独的一个机器上，通过隔离来提高安全性。这样这个服务就只需要暴露非常少的接口对外提供服务。



### 历史原因

- 局域网分布式系统的服务 (1980s)

  

- 互联网规模的分布式系统，比如DNS（域名服务系统）、email

  

- 数据中心 (Data center)，伴随大型网站而生 (1990s)

  - 常见的有网页搜索（爬虫实现，然后需要建立大量倒排索引）

  - 商城购物系统（海量商品、订单、用户信息等数据）

    

- 云计算 (Cloud computing) (2000s)

  - 本地运算/本地运行应用，转移到云服务上运算/运行应用

    

- 如今也是研究和发展的热点话题

### 分布式系统面临的挑战，难点

1. 高并发

2. 如何处理机器宕机（how to deal partial failure）
   - 当部分机器失效时，整个系统如何处理来保证能正确的继续提供服务？用其他的机器来代替失效的机器继续提供服务？甚至可能是网络分区导致了A服务认为B服务失效，实际上并没有失效。这种情况可能导致系统的脑裂
3. 网络分区
4. 很难实现扩容带来的计算力的优势（tricky to realize performance benefits）
   - 单独通过增加设备带来的计算性能不容易转换为可以被使用的计算力





### 实验 labs

1. lab0: mapreduce
2. lab1: raft to replication under partition
3. lab2: replicated k/v service
4. lab3: sharded k/v service (并行的 replicated k/v service来提高性能)



### 课程关注的重心

​	本课程重点在于分布式系统的技术架构，而不是应用程序

#### 	存储：文件系统，键值对服务器

#### 	计算：编排或者构建分布式程序，如mapreduce

#### 	通信: MIt cs 144

### 课程主题

#### 容错：

- 可用性（availability）：可用性的定义为当部分失效发生的时候，服务还能够继续进行，实现的手段是 **复制**
- 可恢复性：定义为当失效后，状态机能够通过 日志，事物，持久存储等手段来恢复服务。

#### 一致性：想的一致性是任何复制机表现得都和只有一台机器一样（不追求这样的效果，不现实）

- 强一致性：多个机器执行的行为，就类似串行执行
- 最终一致性：较为宽松，多个行为执行后，只需要保证最后一个行为的结果在多台服务器都得到相同的表现

#### 性能：

- 性能指标和一致性几乎可以认为是矛盾的。因为强一致性的代价就是使用通信手段来复制各个机器的状态，这就会导致性能的下降。

#### 总结：

分布式系统的复杂性来源于对于以上特性的追求，实现以上特性时彼此就会带来需要解决的问题。在现实中，这几个指标通常会有权衡来实现一个真实的分布式系统。



### MapReduce 



## Lab 1 分布式MapReduce

- 要求：

  -  第一个实验要求完成一个分布式的MapReduce，包含两个程序：一个coordinator一个worker（启动多个线程）。整个lab中，会启动一个coordinator线程和一个或者多个worker线程并行执行任务。在真实的系统中worker运行在多个机器中，在本实验中worker只需要运行在单个机器中。
  - worker通过RPC和coordinator通信，每一个worker线程会在一个循环中向coordinator索要任务，并且从一个或者多个文件中读取任务输入，执行任务，把执行结果保存到一个或者多个文件，然后再次向coordinator获取任务。
  - 本实验中coordinator被要求具备一定的容错，coordinator需要注意每个协调的任务都需要在一定的时间内完成，（本实验推荐10秒），在规定的时间内如果任务结果没有返回，那么coordinator会把这个任务重新交给其他空闲的worker。
  - 实验提供了几个文本文件作为输入，每一个文件被视为一份map任务。

- 额外的规则：

  - map阶段必须把生成的中间键值对切分为n个buckets被n个reduce任务处理（参数是nReduce），每一个map执行线程都必须创建nReduce个中间键值对文件给n个reduce任务使用。

  - 

    

- 思考：
  - coordinator启动时，应该有一个队列来保存map任务？
  - 使用RPC分发map任务时，如何定义任务成功和失败？
  - worker上线时，如何向coordinator注册？





## Lecture 2 Threads&RPC

### Q: 为什么本课程选择go作为编程语言

- 对线程/rpc良好的支持， 提供了rpc包

- 垃圾回收

- 类型安全

- 编译型语言，有编译器

  

  



## Lecture 3 GFS

本节主要讲解：

- 存储系统
- GFS
- 一致性

### 存储系统

- 如果可以构建一个分布式容错的存储系统，那么构建一个无状态的app就是可能的，因为所有的状态都保存在持久存储中。app本身不保存任何状态

### 分布式存储系统很难

- 对高性能的追求：单个磁盘的吞吐有限，需要多个机器同时服务。 对于很多文件不是保存在某个服务器中，而是需要对他们进行分片。
- 多个服务器就可能出现部分失效，只要出现失效就必须要考虑容错
- 容错：容错本质就是通过复制状态机来实现的，出现复制就会出现一致性问题。
- 一致性的实现方法各有不同：强一致性或者弱一致性

### 理想的一致性模型

多个机器组成的服务集群表现如同一台机器。 behave as a single server

- 如何定义什么是一致性：

  ![](https://raw.githubusercontent.com/QuantumJar/Mit_6.824_2021/main/asserts/2024-05-26%20174853.png)

理想中的一致性模型现实中很难实现，主要的原因有两点：

- 并发

  即使在单个机器的写操作也会出现并发带来的问题。这里老师举例了一个服务器并发的处理来自两个客户端C1和C2的写请求。其中C1写入1，C2写入2。在完成各自的写入后，客户端C3和C4来读这个值。

  - **Q1**: 在C1和C2完成写入之后,C3可能读取到什么值？

  - **A1**：可能读取到1或者2。因为在单击环境中的并发问题通常是由锁在处理的，在不考虑C1,C2写操作的先后顺序时，可能性就是1或者2。其他任何非1或者2的值都应该被考虑是错误的。 

  - Q2: C4的读操作应该读取到什么值？
  - A2: C3读取到什么值，C4就应该是什么值。

- failure 机器宕机失效

  ​	为了处理失效带来的问题，复制就是手段。在现实问题中，设计者必须要考虑复制的策略。



### 案例学习 GFS

- GFS 是一个成功的系统，他实现了分布式存储会面临的所有核心的问题，容错，复制，一致性 

### 什么是Google File System?

- Big: large Data set 一个大型的数据集
- fast: 通过自动分片把文件保存到各个磁盘
- global: 全局的，所有的app看到的都是相同的文件系统
- 容错：自动恢复

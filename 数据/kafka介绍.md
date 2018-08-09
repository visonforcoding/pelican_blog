Title: kafka介绍
Date: 2018-08-09 11:08:45
Category: 数据
keywords: kafka,消息队列

> 在一上篇文章介绍了消息队列的使用场景，现在介绍下kafka


## kafka

### Kafka主要特点：

- 同时为发布和订阅提供高吞吐量。据了解，Kafka每秒可以生产约25万消息（50 MB），每秒处理55万消息（110 MB）。

- 可进行持久化操作。将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。通过将数据持久化到硬盘以及replication防止数据丢失。

- 分布式系统，易于向外扩展。所有的producer、broker和consumer都会有多个，均为分布式的。无需停机即可扩展机器。

- 消息被处理的状态是在consumer端维护，而不是由server端维护。当失败时能自动平衡。

- 支持online和offline的场景。

### kafka 架构

![2018-08-09-11-16-19](http://img.rc5j.cn/2018-08-09-11-16-19.png)


Kafka的整体架构非常简单，是显式分布式架构，producer、broker（kafka）和consumer都可以有多个。Producer，consumer实现Kafka注册的接口，数据从producer发送到broker，broker承担一个中间缓存和分发的作用。broker分发注册到系统中的consumer。broker的作用类似于缓存，即活跃的数据和离线处理系统之间的缓存。客户端和服务器端的通信，是基于简单，高性能，且与编程语言无关的TCP协议。

**几个基本概念**：

- Topic：特指Kafka处理的消息源（feeds of messages）的不同分类。
  
- Partition：Topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。
  
- Message：消息，是通信的基本单位，每个producer可以向一个topic（主题）发布一些消息。

- Producers：消息和数据生产者，向Kafka的一个topic发布消息的过程叫做producers。
  
- Consumers：消息和数据消费者，订阅topics并处理其发布的消息的过程叫做consumers。

- Broker：缓存代理，Kafka集群中的一台或多台服务器统称为broker。


### 消息发送流程

![2018-08-09-11-21-17](http://img.rc5j.cn/2018-08-09-11-21-17.png)


1.Producer根据指定的partition方法（round-robin、hash等），将消息发布到指定topic的partition里面

2.kafka集群接收到Producer发过来的消息后，将其持久化到硬盘，并保留消息指定时长（可配置），而不关注消息是否被消费。

3.Consumer从kafka集群pull数据，并控制获取消息的offset


**消息存储策略**

![2018-08-09-11-25-33](http://img.rc5j.cn/2018-08-09-11-25-33.png)

谈到kafka的存储，就不得不提到分区，即partitions，创建一个topic时，同时可以指定分区数目，分区数越多，其吞吐量也越大，但是需要的资源也越多，同时也会导致更高的不可用性，kafka在接收到生产者发送的消息之后，会根据均衡策略将消息存储到不同的分区中。


![2018-08-09-11-27-49](http://img.rc5j.cn/2018-08-09-11-27-49.png)

在每个分区中，消息以顺序存储，最晚接收的的消息会最后被消费。

**与生产者的交互**

![2018-08-09-11-28-42](http://img.rc5j.cn/2018-08-09-11-28-42.png)

- 生产者在向kafka集群发送消息的时候，可以通过指定分区来发送到指定的分区中

- 也可以通过指定均衡策略来将消息发送到不同的分区中
  
- 如果不指定，就会采用默认的随机均衡策略，将消息随机的存储到不同的分区中

**与消费者的交互**

![2018-08-09-11-30-06](http://img.rc5j.cn/2018-08-09-11-30-06.png)

- 在消费者消费消息时，kafka使用offset来记录当前消费的位置

- 在kafka的设计中，可以有多个不同的group来同时消费同一个topic下的消息，如图，我们有两个不同的group同时消费，他们的的消费的记录位置offset不互相干扰。

- 对于一个group而言，消费者的数量不应该多余分区的数量，因为在一个group中，每个分区至多只能绑定到一个消费者上，即一个消费者可以消费多个分区，一个分区只能给一个消费者消费

- 若一个group中的消费者数量大于分区数量的话，多余的消费者将不会收到任何消息。


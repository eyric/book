# 消息队列

### 消息队列的使用场景

#### 为什么使用消息队列

*   解耦：多应用间通过消息队列对同一消息进行处理，避免直接调用接口失败导致整个过程失败

    ![img](https://upload-images.jianshu.io/upload\_images/2509688-f3bddbdea97bb30c?imageMogr2/auto-orient/strip%7CimageView2/2/w/790/format/webp)
*   异步：多应用对消息队列中同一消息进行处理，应用间并发处理消息，相比串行处理，减少处理时间

    ![1556515404010](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556515404010.png)

    ![1556515420697](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556515420697.png)
*   削峰：**通过异步处理，将短时间高并发产生的事务消息存储在消息队列中，从而削平高峰期的并发事务。** 举例：在电子商务一些秒杀、促销活动中，合理使用消息队列可以有效抵御促销活动刚开始大量订单涌入对系统的冲击。如下图所示：

    ![1556515504533](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556515504533.png)

#### 消息队列带来的问题

* **系统可用性降低：** 系统可用性在某种程度上降低，在加入MQ之前，你不用考虑消息丢失或者说MQ挂掉等等的情况，但是，引入MQ之后你就需要去考虑了！
* **系统复杂性提高：** 加入MQ之后，你需要保证消息没有被重复消费、处理消息丢失的情况、保证消息传递的顺序性等等问题！
* **一致性问题：** 我上面讲了消息队列可以实现异步，消息队列带来的异步确实可以提高系统响应速度。但是，万一消息的真正消费者并没有正确消费消息怎么办？这样就会导致数据不一致的情况了!

### 消息队列对比分析

#### 消息队列的两种模式

消息队列包括两种模式，点对点模式（point to point， queue）和发布/订阅模式（publish/subscribe，topic）。

**点对点模式**

点对点模式下包括三个角色：

* 消息队列
* 发送者 (生产者)
* 接收者（消费者）

![1556515852269](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556515852269.png)

消息发送者生产消息发送到queue中，然后消息接收者从queue中取出并且消费消息。消息被消费以后，queue中不再有存储，所以消息接收者不可能消费到已经被消费的消息。

点对点模式特点：

* 每个消息只有一个接收者（Consumer）(即一旦被消费，消息就不再在消息队列中)；
* 发送者和接收者间没有依赖性，发送者发送消息之后，不管有没有接收者在运行，都不会影响到发送者下次发送消息；
* 接收者在成功接收消息之后需向队列应答成功，以便消息队列删除当前接收的消息；

**发布/订阅模式**

发布/订阅模式下包括三个角色：

* 角色主题（Topic）
* 发布者(Publisher)
* 订阅者(Subscriber)

![1556515958791](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556515958791.png)

发布者将消息发送到Topic,系统将这些消息传递给多个订阅者。

发布/订阅模式特点：

* 每个消息可以有多个订阅者；
* 发布者和订阅者之间有时间上的依赖性。针对某个主题（Topic）的订阅者，它必须创建一个订阅者之后，才能消费发布者的消息。
* 为了消费消息，订阅者需要提前订阅该角色主题，并保持在线运行；

#### 几种消息队列的对比

![1556516080741](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556516080741.png)

**RabbitMQ**

RabbitMQ 2007年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

主要特性：

1. 可靠性: 提供了多种技术可以让你在性能和可靠性之间进行权衡。这些技术包括持久性机制、投递确认、发布者证实和高可用性机制；
2. 灵活的路由： 消息在到达队列前是通过交换机进行路由的。RabbitMQ为典型的路由逻辑提供了多种内置交换机类型。如果你有更复杂的路由需求，可以将这些交换机组合起来使用，你甚至可以实现自己的交换机类型，并且当做RabbitMQ的插件来使用；
3. 消息集群：在相同局域网中的多个RabbitMQ服务器可以聚合在一起，作为一个独立的逻辑代理来使用；
4. 队列高可用：队列可以在集群中的机器上进行镜像，以确保在硬件问题下还保证消息安全；
5. 多种协议的支持：支持多种消息队列协议；
6. 服务器端用Erlang语言编写，支持只要是你能想到的所有编程语言；
7. 管理界面: RabbitMQ有一个易用的用户界面，使得用户可以监控和管理消息Broker的许多方面；
8. 跟踪机制：如果消息异常，RabbitMQ提供消息跟踪机制，使用者可以找出发生了什么；
9. 插件机制：提供了许多插件，来从多方面进行扩展，也可以编写自己的插件；

优点：

1. 由于erlang语言的特性，mq 性能较好，高并发；
2. 健壮、稳定、易用、跨平台、支持多种语言、文档齐全；
3. 有消息确认机制和持久化机制，可靠性高；
4. 高度可定制的路由；
5. 管理界面较丰富，在互联网公司也有较大规模的应用；
6. 社区活跃度高；

缺点：

1. 尽管结合erlang语言本身的并发优势，性能较好，但是不利于做二次开发和维护；
2. 实现了代理架构，意味着消息在发送到客户端之前可以在中央节点上排队。此特性使得RabbitMQ易于使用和部署，但是使得其运行速度较慢，因为中央节点增加了延迟，消息封装后也比较大；
3. 需要学习比较复杂的接口和协议，学习和维护成本较高；

**ActiveMQ**

ActiveMQ是由Apache出品，ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的 JMS Provider实现。它非常快速，支持多种语言的客户端和协议，而且可以非常容易的嵌入到企业的应用环境中，并有许多高级功能。

主要特性：

1. 服从 JMS 规范：JMS 规范提供了良好的标准和保证，包括：同步或异步的消息分发，一次和仅一次的消息分发，消息接收和订阅等等。遵从 JMS 规范的好处在于，不论使用什么 JMS 实现提供者，这些基础特性都是可用的；
2. 连接性：ActiveMQ 提供了广泛的连接选项，支持的协议有：HTTP/S，IP 多播，SSL，STOMP，TCP，UDP，XMPP等等。对众多协议的支持让 ActiveMQ 拥有了很好的灵活性。
3. 支持的协议种类多：OpenWire、STOMP、REST、XMPP、AMQP ；
4. 持久化插件和安全插件：ActiveMQ 提供了多种持久化选择。而且，ActiveMQ 的安全性也可以完全依据用户需求进行自定义鉴权和授权；
5. 支持的客户端语言种类多：除了 Java 之外，还有：C/C++，.NET，Perl，PHP，Python，Ruby；
6. 代理集群：多个 ActiveMQ 代理可以组成一个集群来提供服务；
7. 异常简单的管理：ActiveMQ 是以开发者思维被设计的。所以，它并不需要专门的管理员，因为它提供了简单又使用的管理特性。有很多中方法可以监控 ActiveMQ 不同层面的数据，包括使用在 JConsole 或者 ActiveMQ 的Web Console 中使用 JMX，通过处理 JMX 的告警消息，通过使用命令行脚本，甚至可以通过监控各种类型的日志。

优点：

1. 跨平台(JAVA编写与平台无关有，ActiveMQ几乎可以运行在任何的JVM上)
2. 可以用JDBC：可以将数据持久化到数据库。虽然使用JDBC会降低ActiveMQ的性能，但是数据库一直都是开发人员最熟悉的存储介质。将消息存到数据库，看得见摸得着。而且公司有专门的DBA去对数据库进行调优，主从分离；
3. 支持JMS ：支持JMS的统一接口;
4. 支持自动重连；
5. 有安全机制：支持基于shiro，jaas等多种安全配置机制，可以对Queue/Topic进行认证和授权。
6. 监控完善：拥有完善的监控，包括Web Console，JMX，Shell命令行，Jolokia的REST API；
7. 界面友善：提供的Web Console可以满足大部分情况，还有很多第三方的组件可以使用，如hawtio；

缺点：

1. 社区活跃度不及RabbitMQ高；
2. 根据其他用户反馈，会出莫名其妙的问题，会丢失消息；
3. 目前重心放到activemq6.0产品-apollo，对5.x的维护较少；
4. 不适合用于上千个队列的应用场景；

**RocketMQ**

RocketMQ出自 阿里公司的开源产品，用 Java 语言实现，在设计时参考了 Kafka，并做出了自己的一些改进，消息可靠性上比 Kafka 更好。RocketMQ在阿里集团被广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog分发等场景。

主要特性：

1. 是一个队列模型的消息中间件，具有高性能、高可靠、高实时、分布式特点；
2. Producer、Consumer、队列都可以分布式；
3. Producer向一些队列轮流发送消息，队列集合称为Topic，Consumer如果做广播消费，则一个consumer实例消费这个Topic对应的所有队列，如果做集群消费，则多个Consumer实例平均消费这个topic对应的队列集合；
4. 能够保证严格的消息顺序；
5. 提供丰富的消息拉取模式；
6. 高效的订阅者水平扩展能力；
7. 实时的消息订阅机制；
8. 亿级消息堆积能力；
9. 较少的依赖；

优点：

1. 单机支持 1 万以上持久化队列
2. RocketMQ 的所有消息都是持久化的，先写入系统 PAGECACHE，然后刷盘，可以保证内存与磁盘都有一份数据，访问时，直接从内存读取。
3. 模型简单，接口易用（JMS 的接口很多场合并不太实用）；
4. 性能非常好，可以大量堆积消息在broker中；
5. 支持多种消费，包括集群消费、广播消费等。
6. 各个环节分布式扩展设计，主从HA；
7. 开发度较活跃，版本更新很快。

缺点：

1.支持的客户端语言不多，目前是java及c++，其中c++不成熟；

2.RocketMQ社区关注度及成熟度也不及前两者；

3.没有web管理界面，提供了一个CLI(命令行界面)管理工具带来查询、管理和诊断各种问题；

4.没有在 mq 核心中去实现JMS等接口；

**Kafka**

Apache Kafka是一个分布式消息发布订阅系统。它最初由LinkedIn公司基于独特的设计实现为一个分布式的提交日志系统( a distributed commit log)，之后成为Apache项目的一部分。Kafka系统快速、可扩展并且可持久化。它的分区特性，可复制和可容错都是其不错的特性。

主要特性：

1. 快速持久化，可以在O(1)的系统开销下进行消息持久化；
2. 高吞吐，在一台普通的服务器上既可以达到10W/s的吞吐速率；
3. .完全的分布式系统，Broker、Producer、Consumer都原生自动支持分布式，自动实现负载均衡；
4. 支持同步和异步复制两种HA；
5. 支持数据批量发送和拉取；
6. zero-copy：减少IO操作步骤；
7. 数据迁移、扩容对用户透明；
8. 无需停机即可扩展机器；
9. 其他特性：严格的消息顺序、丰富的消息拉取模型、高效订阅者水平扩展、实时的消息订阅、亿级的消息堆积能力、定期删除机制；

优点：

1. 客户端语言丰富，支持java、.net、php、ruby、python、go等多种语言；
2. 性能卓越，单机写入TPS约在百万条/秒，消息大小10个字节；
3. 提供完全分布式架构, 并有replica机制, 拥有较高的可用性和可靠性, 理论上支持消息无限堆积；
4. 支持批量操作；
5. 消费者采用Pull方式获取消息, 消息有序, 通过控制能够保证所有消息被消费且仅被消费一次;
6. 有优秀的第三方Kafka Web管理界面Kafka-Manager；
7. 在日志领域比较成熟，被多家公司和多个开源项目使用；

缺点：

1. Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长
2. 使用短轮询方式，实时性取决于轮询间隔时间；
3. 消费失败不支持重试；
4. 支持消息顺序，但是一台代理宕机后，就会产生消息乱序；
5. 社区更新较慢；

### 消息队列面试题分析

#### 如何保证消息队列的高可用

**RabbitMQ的高可用性**

rabbitmq有三种模式：单机模式，普通集群模式和镜像集群模式，可以采用镜像集群模式来保证高可用。

![1556517445121](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556517445121.png)

**Kafka的高可用性**

kafka 0.8以后，提供了HA机制，就是replica副本机制。每个partition的数据都会同步到吉他机器上，形成自己的多个replica副本。然后所有replica会选举一个leader出来，那么生产和消费都跟这个leader打交道，然后其他replica就是follower。写的时候，leader会负责把数据同步到所有follower上去，读的时候就直接读leader上数据即可。

![1556517676996](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556517676996.png)

#### 重复消费和如何保证消息队列的幂等性

rabbitmq、rocketmq、kafka，都有可能会出现消费重复消费的问题，因为这问题通常不是mq自己保证的，是给你保证的。以Kafka为例：

#### ![1556517991451](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556517991451.png)

其实还是得结合业务来思考，我这里给几个思路：

（1）比如你拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update一下好吧

（2）比如你是写redis，那没问题了，反正每次都是set，天然幂等性

（3）比如你不是上面两个场景，那做的稍微复杂一点，你需要让生产者发送每条数据的时候，里面加一个`全局唯一的id`，类似订单id之类的东西，然后你这里消费到了之后，先根据这个id去比如redis里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个id写redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。

#### 如何保证消息的可靠性传输

**rabbitMQ**

*   生产者丢失数据

    `事务机制`和`cnofirm机制`最大的不同在于，事务机制是同步的，你提交一个事务之后会阻塞在那儿，但是confirm机制是异步的，你发送个消息之后就可以发送下一个消息，然后那个消息rabbitmq接收了之后会异步回调你一个接口通知你这个消息接收到了。所以一般在生产者这块避免数据丢失，都是用confirm机
*   rabbitmq弄丢了数据

    开启rabbitmq的持久化，消息写入之后会持久化到磁盘，哪怕是rabbitmq自己挂了，恢复之后会自动读取之前存储的数据，一般数据不会丢。除非极其罕见的是，rabbitmq还没持久化，自己就挂了，可能导致少量数据会丢失的，但是这个概率较小。
*   消费者丢失数据

    使用rabbitmq提供的ack机制，关闭rabbitmq自动ack，可以通过一个api来调用就行，然后每次在代码里确保处理完的时候，再程序里ack一下。这样的话，如果你还没处理完，不就没有ack？那rabbitmq就认为你还没处理完，这个时候rabbitmq会把这个消费分配给别的consumer去处理，消息是不会丢的。

    ![1556518813744](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556518813744.png)

**Kafka消息丢失**

*   消费端丢失数据

    唯一可能导致消费者弄丢数据的情况，就是说，你那个消费到了这个消息，然后消费者那边自动提交了offset，让kafka以为你已经消费好了这个消息，其实你刚准备处理这个消息，你还没处理，你自己就挂了，此时这条消息就丢咯。kafka会自动提交offset，那么只要关闭自动提交offset，在处理完之后自己手动提交offset，就可以保证数据不会丢。但是此时会重复消费，比如你刚处理完，还没提交offset，结果自己挂了，此时肯定会重复消费一次，需要自己保证幂等性。
*   Kafka丢失数据

    比较常见的一个场景，就是kafka某个broker宕机，然后重新选举partiton的leader时。而此时其他的follower刚好还有些数据没有同步，然后选举某个follower成leader之后，就丢了一些数据。

    所以此时一般是要求起码设置如下4个参数：

    * 给这个topic设置replication.factor参数：这个值必须大于1，要求每个partition必须有至少2个副本
    * 在kafka服务端设置min.insync.replicas参数：这个值必须大于1，这个是要求一个leader至少感知到有至少一个follower还跟自己保持联系，没掉队，这样才能确保leader挂了还有一个follower吧
    * 在producer端设置acks=all：这个是要求每条数据，必须是写入所有replica之后，才能认为是写成功了
    * 在producer端设置retries=MAX（很大很大很大的一个值，无限次重试的意思）：这个是要求一旦写入失败，就无限重试，卡在这里了
*   生产者丢失数据

    如果按照上述的思路设置了ack=all，一定不会丢，要求是，你的leader接收到消息，所有的follower都同步到了消息之后，才认为本次写成功了。如果没满足这个条件，生产者会自动不断的重试，重试无限次。

#### 如何保证消息的顺序性

**rabbitmq保证数据的顺序性**

如果存在多个消费者，那么就让每个消费者对应一个queue，然后把要发送 的数据全都放到一个queue，这样就能保证所有的数据只到达一个消费者从而保证每个数据到达数据库都是顺序的。

![1556520082708](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556520082708.png)

**kafka保证数据的顺序性**

kafka 写入partion时指定一个key，列如订单id，那么消费者从partion中取出数据的时候肯定是有序的，当开启多个线程的时候可能导致数据不一致，这时候就需要内存队列，将相同的hash过的数据放在一个内存队列里，这样就能保证一条线程对应一个内存队列的数据写入数据库的时候顺序性的，从而可以开启多条线程对应多个内存队列

![img](https://upload-images.jianshu.io/upload\_images/7918686-755410a74bf7cd57.png?imageMogr2/auto-orient/)

#### 如何处理消息的队列的延时和积压

![1556520590395](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556520590395.png)

如果消息已经过期失效，可以采取**批量重导**

#### 如何设计消息队列

**消息队列的组成**

![1556521737517](https://c/Users/lwt/AppData/Roaming/Typora/typora-user-images/1556521737517.png)

**1.Producer(消息生产者)**：发送消息到Broker。

**2.Broker(服务端)**：Broker这个概念主要来自于Apache的ActiveMQ，特指消息队列的服务端。

主要功能就是：把消息从发送端传送到接收端，这里会涉及到消息的存储、消息通讯机制等。

**3.Consumer(消息消费者)**：从消息队列接收消息，consumer回复消费确认。

**Broker(消息队列服务端)设计重点**

**1）消息的转储**：在更合适的时间点投递，或者通过一系列手段辅助消息最终能送达消费机。

**2）规范一种范式和通用的模式**，以满足解耦、最终一致性、错峰等需求。

3）其实简单理解就是一个消息转发器，把一次RPC做成两次RPC，发送者把消息投递到broker，broker再将消息转发一手到接收端。

**总结起来就是两次RPC加一次转储，如果要做消费确认，则是三次RPC。**

为了实现上述消息队列的基础功能：

* 消息的传输
* 存储
* 消费

**就需要涉及到如下三个方面的设计：**

*   通信协议

    > **JMS**

    JMS（Java MessageService）实际上是指JMS API。JMS是由Sun公司早期提出的消息标准，旨在为java应用提供统一的消息操作，包括创建消息、发送消息、接收消息等。

    **JMS提供了两种消息模型：**

    * 点对点
    * 以及publish-subscribe（发布订阅）模型。

    > **AMQP**

    AMQP是 Advanced Message Queuing Protocol，即高级消息队列协议。

    > **JMS和AMQP比较**

    JMS: 只允许基于JAVA实现的消息平台的之间进行通信

    AMQP: AMQP允许多种技术同时进行协议通信
*   存储选择

    对于分布式系统，存储的选择有以下几种

    * 内存
    * 本地文件系统
    * 分布式文件系统
    * nosql
    * DB
*   消费关系维护

    JMS 规范中的Topic/Queue，Kafka里面的Topic/Partition/ConsumerGroup，RabbitMQ里面的Exchange

    抛开现象看本质，无外乎是**单播与广播**的区别。所谓单播，就是点到点；而广播，是一点对多点。

    为了实现广播功能，我们必须要维护消费关系，通常消息队列本身不维护消费订阅关系，可以利用zookeeper等成熟的系统维护消费关系，在消费关系发生变化时下发通知。

**高级功能**

* 消息的顺序
* 投递可靠性保证
* 消息持久化
* 支持不同消息模型
* 多实例集群功能
* 事务特性等
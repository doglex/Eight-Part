参考 https://zhuanlan.zhihu.com/p/388206784
## Kafka使用注意事项
+ 配好特定的一个(或多个)topic
+ 一个唯一的group_id就是一套完整的数据
+ 用earliest就是从头(未被删的)消费，用latest就是从当前末尾开始消费，或者自己定义每个partition从多少offset开始消费
+ Flink的source并行度可以设置为partition能整除的因子，比较均衡且没有悬空的线程

## 为什么需要 Kafka 中间件
+ Kafka是一个消息队列(MQ, Message Queue)
+ MQ的作用在于**解耦上下游业务**。即，上游只用管数据生产，而不必受制于下游的处理压力；下游只用管数据消费，只是订阅轮询topic，而不用依赖数据是怎么生产出来的；一次生产，可以多次消费(使用不同的group_id)
+ 相对于RabbitMq、Redis、Mysql等其他MQ，Kafka对大规模、实时(流式Flink/Spark)数据很友好，也是目前最流行的
+ 可持久化：当然是可持久化，不然那么大的数据堆在内存里放不下，redis也是可以定期持久化的

## 基本概念
+ Producer ：生产者，负责将消息发送到 Broker
+ Consumer ：消费者，从 Broker 接收消息。以group的形式去消费topic中的数据
+ Consumer Group ：消费者组，由多个 Consumer 组成。消费者组内每个消费者负责消费不同分区的数据，**一个分区只能由一个组内消费者消费**；消费者组之间互不影响。所有的消费者都属于某个消费者组，即消费者组是逻辑上的一个订阅者。
> 假设有5个partition，而flink消费开了6个线程，那么1个线程只能悬空。因为5个partition至多分给5个消费者线程
+ Broker ：中间人。可以看做一个独立的 Kafka 服务节点或 Kafka 服务实例。如果一台服务器上只部署了一个 Kafka 实例，那么我们也可以将 Broker 看做一台 Kafka 服务器。
+ Topic ：一个逻辑上的概念，包含很多 Partition，同一个 Topic 下的 Partiton 的消息内容是不相同的。
+ Partition ：为了实现扩展性，一个非常大的 topic 可以分布到多个 broker 上，一个 topic 可以分为多个 分区，每个 partition 是一个有序的队列。 通过增加partition是可以扩容的。
+ Replica ：副本，同一分区的不同副本保存的是相同的消息，为保证集群中的某个节点发生故障时，该节点上的 partition 数据不丢失，且 kafka 仍然能够继续工作，kafka 提供了副本机制，一个 topic 的每个分区都有若干个副本，一个 leader 和若干个 follower。
+ Leader ：每个分区的多个副本中的"主副本"，生产者以及消费者只与 Leader 交互。
+ Follower ：每个分区的多个副本中的"从副本"，负责实时从 Leader 中同步数据，保持和 Leader 数据的同步。Leader 发生故障时，从 Follower 副本中重新选举新的 Leader 副本对外提供服务。
+ zookeeper：负责leader、follower投票
+ AR==Replica, ISR就是紧跟Leader副本的副本，OSR就是落后Leader很多的副本。Leader会维护哪些是ISR、哪些是OSR，OSR是可能追上ISR的。选Leader的时候优先从ISR里选。

## 生产者发送消息模式
+ 发后即忘：发了就不管了，不管Kafka有没有收到
+ 同步：发了一条，等待成功写入的消息，之后再发下一条 
+ 异步：发了一条，就继续发或者干别的，不会阻塞，是否成功可以回头查看日志




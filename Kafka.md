参考 https://zhuanlan.zhihu.com/p/388206784
## 为什么需要 Kafka 中间件
+ Kafka是一个消息队列(MQ, Message Queue)
+ MQ的作用在于**解耦上下游业务**。即，上游只用管数据生产，而不必受制于下游的处理压力；下游只用管数据消费，只是订阅轮询topic，而不用依赖数据是怎么生产出来的；一次生产，可以多次消费(使用不同的group_id)
+ 相对于RabbitMq、Redis、Mysql等其他MQ，Kafka对大规模、实时(流式Flink/Spark)数据很友好，也是目前最流行的
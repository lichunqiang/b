title: "kafka入门"
date: 2018-09-26 10:17:39
tags: ["kafka", "zookeeper"]
category: ["kafka", "zookeeper"]
---

安装kakfa

```bash
$ wget http://mirrors.hust.edu.cn/apache/kafka/2.0.0/kafka_2.11-2.0.0.tgz
$ tar zxvf kafka_2.11-2.0.0.tgz
$ cd kafka_2.11-2.0.0/
```

启动zookeeper，如果使用的是单独kafka包，则包含了zookeeper

```bash
$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

如果默认的端口2181被占用，则需要修改__config/zookeeper.properties__ 来更改默认端口号.

启动kafka

```bash
$ bin/kafka-server-start.sh config/server.properties
```

创建一个topic - test

```
$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

查看创建的topic

```bash
$ bin/kafka-topics.sh --list --zookeeper localhost:2181
```

除去手动创建topic之外还可以通过配置broker自动创建当topic不存在时。

发送消息

```bash
$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

消费消息

```
$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

模拟多broker集群

复制多分配置文件:

```
$ cp config/server.properties config/server-1.properties
$ cp config/server.properties config/server-2.properties
```

修改两份配置文件，如下：

```
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dirs=/tmp/kafka-logs-1
 
config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dirs=/tmp/kafka-logs-2
```

`broker.id` 需要在整个集群中是唯一的。

启动broker

```
$ bin/kafka-server-start.sh config/server-1.properties &
$ bin/kafka-server-start.sh config/server-2.properties &
```

创建相应topic

```
$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```

查看各个broker情况

```
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:
    Topic: my-replicated-topic  Partition: 0    Leader: 1   Replicas: 1,2,0 Isr: 1,2,0
```


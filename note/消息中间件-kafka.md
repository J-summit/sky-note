## 1.kafka

### 1.单击安装

<http://kafka.apachecn.org/quickstart.html> quickstart

```
https://kafka.apache.org/downloads
先安装zookeeper（为了简单只起一台zookeeper）

cd /usr/local/mq
tar -zxvf kafka_2.13-2.4.0.tgz
cd kafka_2.13-2.4.0/config/
vi server.properties
修改zookeeper地址
zookeeper.connect=192.168.43.111:2181

然后直接
cd ../bin/
sh kafka-server-start.sh ../config/server.properties
或者后台启动 
sh kafka-server-start.sh -daemon ../config/server.properties
启动完成

然后测试，创建一个 topic
sh kafka-topics.sh --create --zookeeper 192.168.43.160:2181 --replication-factor 1 --partitions 1 --topic test
查看topic列表
sh kafka-topics.sh --list --zookeeper 192.168.43.160:2181

创建生产者
sh kafka-console-producer.sh --broker-list 192.168.43.160:9092 --topic test
创建消费者
sh kafka-console-consumer.sh --bootstrap-server 192.168.43.160:9092 --topic test --from-beginning

结果
在生产者发送消息，消费端可以接受消息
```

### 2.cluster搭建

```
准备三台
broker.id=0
listeners=PLAINTEXT://192.168.43.160:9092
zookeeper.connect=192.168.43.160:2181
每台修改这三个地方

然后zkCli.sh
查看
zk: localhost:2181(CONNECTED) 2] ls /brokers/ids
[0, 1, 2]
说明搭建完成

```

### 4.topic&partition


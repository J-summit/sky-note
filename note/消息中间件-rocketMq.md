## 1.rocketMq

阿里出品，java编写，集群高可用，可靠性高，源码可读性好（面向过程编写），经历双十一

10万级，RocketMQ也是可以支撑高吞吐的一种MQ

**无单点问题**

## 2.部署

### 1）高可用搭建

单机

2m2s 同步/异步

2m  同步/异步

多主多从 同步/异步

### 2）部署

#### a 单机启动

```
1.下载解决看下面
2.启动nameServer
nohup mqnamesrv &

查看日志
logs/rocketmqlogs/namesrv.log
3.启动broker
nohup mqbroker -n localhost:9876 &
查看日志
logs/rocketmqlogs/broker.log 
4.启动ok

5.测试消息发送和消费
> export NAMESRV_ADDR=localhost:9876
 > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
 SendResult [sendStatus=SEND_OK, msgId= ...

 > sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
 ConsumeMessageThread_%d Receive New Messages: [MessageExt...
 
 6.Shutdown Servers
> sh bin/mqshutdown broker
The mqbroker(36695) is running...
Send shutdown request to mqbroker(36695) OK

> sh bin/mqshutdown namesrv
The mqnamesrv(36664) is running...
Send shutdown request to mqnamesrv(36664) OK
```

##### 控制台搭建

```
1.下载
https://github.com/apache/rocketmq-externals
进入rocketmq-console
的application.properties
rocketmq.config.namesrvAddr=localhost:9876
rocketmq.config.isVIPChannel=false
2. mvn clean package -D skipTests
找到target的jar
然后直接
java -jar rocketmq-console-ng-1.0.1.jar &

然后打开http://192.168.1.102:8080/ 
```



#### b 2m-2s-sync两主两从同步写

![](D:\idea-project\sky-note\note\img\两主两从同步写部署.jpg)

```
1.下载
https://rocketmq.apache.org/dowloading/releases/
2.然后解压
unzip rocketmq-all-4.6.0-bin-release.zip -d /usr/local/mq/
3.cd /usr/local/mq/
修改文件名
mv rocketmq-all-4.6.0-bin-release/ rocketmq-4.6.0

4.vim /etc/hosts
192.168.43.160 rocketmq1
192.168.43.222 rocketmq2

5.vim /etc/profile
export ROCKETMQ_HOME=/usr/local/mq/rocketmq-4.6.0
export PATH=$PATH::$ROCKETMQ_HOME/bin
source /etc/profile



```

6.修改conf/2m-2s-sync目录下文件

```
cd 2m-2s-sync

brokerClusterName=tl-rocketmq-cluster
brokerName=broker-a
brokerId=0
namesrvAddr=rocketmq1:9876;rocketmq2:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=10911
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
destroyMapedFileIntervalForcibly=120000
redeleteHangedFileInterval=120000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/mq/rocketmq-4.6.0/data/store
storePathCommitLog=/usr/local/mq/rocketmq-4.6.0/store/commitlog
maxMessageSize=65536
flushCommitLogLeastPages=4
flushConsumeQueueLeastPages=2
flushCommitLogThoroughInterval=10000
flushConsumeQueueThoroughInterval=60000
checkTransactionMessageEnable=false
sendMessageThreadPoolNums=128
pullMessageThreadPoolNums=128
#Broker 的角色 
#- ASYNC_MASTER 异步复制Master 
#- SYNC_MASTER 同步双写Master 
#- SLAVE
brokerRole=SYNC_MASTER 
#- ASYNC_FLUSH 异步刷盘 
#- SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH
```

7.vi broker-b-s.properties

```
brokerClusterName=tl-rocketmq-cluster
brokerName=broker-b
#0 表示 Master，>0 表示 Slave
brokerId=1 
namesrvAddr=rocketmq1:9876;rocketmq2:9876
defaultTopicQueueNums=4
autoCreateTopicEnable=true
autoCreateSubscriptionGroup=true
listenPort=10921
deleteWhen=04
fileReservedTime=120
mapedFileSizeCommitLog=1073741824
mapedFileSizeConsumeQueue=300000
destroyMapedFileIntervalForcibly=120000
redeleteHangedFileInterval=120000
diskMaxUsedSpaceRatio=88
storePathRootDir=/usr/local/mq/rocketmq-4.6.0/data/store
storePathCommitLog=/usr/local/mq/rocketmq-4.6.0/commitlog
maxMessageSize=65536
flushCommitLogLeastPages=4
flushConsumeQueueLeastPages=2
flushCommitLogThoroughInterval=10000
flushConsumeQueueThoroughInterval=60000
checkTransactionMessageEnable=false
sendMessageThreadPoolNums=128
pullMessageThreadPoolNums=128
#角色
brokerRole=SYNC_SLAVE
flushDiskType=SYNC_FLUSH
```

```
mkdir -p /usr/local/mq/rocketmq-4.6.0/data/store/commitlog
mkdir -p /usr/local/mq/rocketmq-4.6.0/store/consumequeue
mkdir -p /usr/local/mq/rocketmq-4.6.0/store/index

sed -i 's#${user.home}#/usr/local/mq/rocketmq-4.6.0#g' *.xml

修改jvm参数
cd bin
修改JVM配置
vi runbroker.sh
vi runserver.sh



复制到其他服务
scp -r rocketmq-4.6.0/ root@192.168.43.222:/usr/local/mq/rocketmq-4.6.0

sh mqbroker -c /root/svr/rocketmq/conf/2m-2s-sync/broker-a.properties&
```

### 启动服务

```
启动nameserver
进入bin目录下
启动nameServer
sh mqnamesrv &
sh mqbroker -c /usr/local/mq/rocketmq-4.6.0/conf/2m-2s-async/broker-a.properties &
sh mqbroker -c /usr/local/mq/rocketmq-4.6.0/conf/2m-2s-async/broker-b-s.properties &
 jps -vm
 
sh mqnamesrv &
sh mqbroker -c /usr/local/mq/rocketmq-4.6.0/conf/2m-2s-async/broker-b.properties &
sh mqbroker -c /usr/local/mq/rocketmq-4.6.0/conf/2m-2s-async/broker-a-s.properties &
```

集群监控查询

```
sh mqadmin clusterlist -n 192.168.43.160:9876
```


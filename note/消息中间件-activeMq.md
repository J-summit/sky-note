## 安装

### 1.activemq linux部署

java编写

下载地址 https://activemq.apache.org/download.html

或者直接 wget http://apache.fayea.com/activemq/5.15.11/apache-activemq-5.15.11-bin.tar.gz

```
cd /usr/local
tar -zxvf apache-activemq-5.15.11-bin.tar.gz
cd ./apache-activemq-5.15.11/bin/
sh activemq start
浏览器打开
192.168.1.109:8161
ip为虚拟机本机ip
默认账号密码为admin admin
```

#### java代码演示

```
  <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>activemq-all</artifactId>
        <version>5.15.11</version>
    </dependency>
```

##### 1.生产者

```java
    public static void main(String[] args) throws JMSException {
        ConnectionFactory conn = new ActiveMQConnectionFactory("tcp://192.168.1.109:61616");
        Connection connection = null;
        try {
            connection = conn.createConnection();
            connection.start();

            Session session = connection.createSession(Boolean.TRUE, Session.AUTO_ACKNOWLEDGE);
            Destination destination = session.createQueue("myqueue");
            MessageProducer producer = session.createProducer(destination);
            TextMessage message = session.createTextMessage("hellow word1");
            producer.send(message);
            session.commit();
            session.close();
        } catch (JMSException e) {
            e.printStackTrace();
        } finally {
            if (connection != null) {
                connection.close();
            }
        }

    }
```

##### 2.消费者

两种模式一种receive 阻塞，直到获取到消息

一种Listener，while true一直监听数据

```java
    public static void main(String[] args) throws JMSException {
        ConnectionFactory conn = new ActiveMQConnectionFactory("tcp://192.168.1.109:61616");
        Connection connection = null;
        try {
            connection = conn.createConnection();
            connection.start();

            Session session = connection.createSession(Boolean.TRUE, Session.AUTO_ACKNOWLEDGE);
            Destination destination = session.createQueue("myqueue");
            MessageConsumer producer = session.createConsumer(destination);
            TextMessage receive = (TextMessage)producer.receive();
            System.out.println(receive.getText());
            session.commit();
            session.close();
        } catch (JMSException e) {
            e.printStackTrace();
        } finally {
            if (connection != null) {
                connection.close();
            }
        }

    }
```

```
public static void main(String[] args) throws JMSException {
        ConnectionFactory conn = new ActiveMQConnectionFactory("tcp://192.168.1.109:61616");
        Connection connection = null;
        try {
            connection = conn.createConnection();
            connection.start();

            Session session = connection.createSession(Boolean.TRUE, Session.AUTO_ACKNOWLEDGE);
            Destination destination = session.createQueue("myqueue");
            MessageConsumer producer = session.createConsumer(destination);
            MessageListener listener = new MessageListener() {
                @Override
                public void onMessage(Message message) {
                    try {
                        System.out.println(((TextMessage) message).getText());
                    } catch (JMSException e) {
                        e.printStackTrace();
                    }
                }
            };
            while (true) {
                producer.setMessageListener(listener);
                session.commit();
            }

          //  session.close();
        } catch (JMSException e) {
            e.printStackTrace();
        } finally {
            if (connection != null) {
                connection.close();
            }
        }

    }
```

### 2.消息持久化非持久化发送策略

```
同步发送过程中，发送者发送一条消息会阻塞直到broker反馈一个确认消息，表示消息已经被broker处理。这个机
制提供了消息的安全性保障，但是由于是阻塞的操作，会影响到客户端消息发送的性能
异步发送的过程中，发送者不需要等待broker提供反馈，所以性能相对较高。但是可能会出现消息丢失的情况。所
以使用异步发送的前提是在某些情况下允许出现数据丢失的情况。

非持久化消息是异步发送的，持久化消息并且是在非事务模式下是同步发送的
设置异步发送
1.ConnectionFactory connectionFactory=new ActiveMQConnectionFactory("tcp://192.168.11.153:61616?
jms.useAsyncSend=true");
2.((ActiveMQConnectionFactory) connectionFactory).setUseAsyncSend(true);
3.((ActiveMQConnection)connection).setUseAsyncSend(true);
```

**消息的发送原理分析图解**

![](D:/idea-project/sky-note/note/img/%E6%B6%88%E6%81%AF%E7%9A%84%E5%8F%91%E9%80%81%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90%E5%9B%BE%E8%A7%A3.jpg)

```
ProducerWindowSize的含义
producer每发送一个消息，统计一下发送的字节数，当字节数达到ProducerWindowSize值时，需要等待broker的
确认，才能继续发送。
代码在：ActiveMQSession的1957行
主要用来约束在异步发送时producer端允许积压的(尚未ACK)的消息的大小，且只对异步发送有意义。每次发送消
息之后，都将会导致memoryUsage大小增加(+message.size)，当broker返回producerAck时，memoryUsage尺
寸减少(producerAck.size，此size表示先前发送消息的大小)。
可以通过如下2种方式设置:
Ø 在brokerUrl中设置: "tcp://localhost:61616?jms.producerWindowSize=1048576",这种设置将会对所有的
producer生效。
Ø 在destinationUri中设置: "test-queue?producer.windowSize=1048576",此参数只会对使用此Destination实例
的producer失效，将会覆盖brokerUrl中的producerWindowSize值。
注意：此值越大，意味着消耗Client端的内存就越大
```

### 3.消费端消费消息的原理

![](D:/idea-project/sky-note/note/img/%E6%B6%88%E8%B4%B9%E7%AB%AF%E6%B6%88%E8%B4%B9%E6%B6%88%E6%81%AF%E7%9A%84%E5%8E%9F%E7%90%86.jpg)


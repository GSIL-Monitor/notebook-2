# **kafka**

## 1. Kafka

### 1.1 线上kafka配置：
```
broker.id=1
#port=9092
#advertised.host.name=183.131.54.163

listener.security.protocol.map=PLAINTEXT:PLAINTEXT,PLAINTEXT1:PLAINTEXT,PLAINTEXT2:PLAINTEXT,PLAINTEXT3:PLAINTEXT
listeners=PLAINTEXT://183.131.54.163:9092,PLAINTEXT1://120.199.83.35:9093,PLAINTEXT2://101.71.76.35:9094,PLAINTEXT3://172.18.54.163:9095
advertised.listeners=PLAINTEXT://183.131.54.163:9092,PLAINTEXT1://120.199.83.35:9093,PLAINTEXT2://101.71.76.35:9094,PLAINTEXT3://172.18.54.163:9095

num.network.threads=25
num.io.threads=24
socket.request.max.bytes=104857600
socket.receive.buffer.bytes=1048576
socket.send.buffer.bytes=1048576
log.dirs=/bbd/sata01/kafka01,/bbd/sata02/kafka01,/bbd/sata03/kafka01,/bbd/sata04/kafka01,/bbd/sata05/kafka01,/bbd/sata06/kafka01,/bbd/sata07/kafka01,/bbd/sata08/kafka01,/bbd/sata09/kafka01,/bbd/sata10/kafka01,/bbd/sata11/kafka01,/bbd/sata12/kafka01
#log.dirs=/bbd/ssd01/kafka/kafkadir
num.partitions=66
default.replication.factor=2
num.recovery.threads.per.data.dir=1
log.retention.hours=72
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
log.cleaner.enable=false
zookeeper.connect=172.18.54.162:2181,172.18.54.163:2181,172.18.54.164:2181
zookeeper.connection.timeout.ms=6000
delete.topic.enable=true
#log.flush.interval.messages=10000
#log.flush.interval.ms=1000
queued.max.requests=2000
offsets.storage=zookeeper
#heartbeat.interval.ms=30000
#session.timeout.ms=100000

#max.partition.fetch.bytes=20971520
#max.poll.records=400
#max.poll.interval.ms=200000

#offsets.topic.num.partitions=66
#offsets.topic.replication.factor=3
```
### 1.2 虚拟机中的kafka配置
```
broker.id=0
#port=9092
#advertised.host.name=183.131.54.163

listener.security.protocol.map=PLAINTEXT:PLAINTEXT
listeners=PLAINTEXT://master:9092
advertised.listeners=PLAINTEXT://master:9092

num.network.threads=2
num.io.threads=2
socket.request.max.bytes=104857600
socket.receive.buffer.bytes=1048576
socket.send.buffer.bytes=1048576
log.dirs=/zz/data/kafka
num.partitions=6
default.replication.factor=1
num.recovery.threads.per.data.dir=1
log.retention.hours=72
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
log.cleaner.enable=false
zookeeper.connect=master:2181,work1:2181,work2:2181
zookeeper.connection.timeout.ms=6000
delete.topic.enable=true
#log.flush.interval.messages=10000
#log.flush.interval.ms=1000
queued.max.requests=2000
offsets.storage=zookeeper
#heartbeat.interval.ms=30000
#session.timeout.ms=100000

#max.partition.fetch.bytes=20971520
#max.poll.records=400
#max.poll.interval.ms=200000

#offsets.topic.num.partitions=66
#offsets.topic.replication.factor=3
```

[kakfa设计解析（一）- Kafka背景及架构介绍](http://www.jasongj.com/2015/03/10/KafkaColumn1/)

## 2. kafka streams

官网：

> [Streams API](http://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/streams/KafkaStreams.html)    
> [Kafka Streams Configs](http://kafka.apache.org/10/documentation#streamsconfigs)  
> [Step 8: Use Kafka Streams to process data](http://kafka.apache.org/0102/documentation/#quickstart_kafkastreams)  
> [kafka streams](http://kafka.apache.org/documentation/streams/)  
> &emsp; [Streams DSL](http://kafka.apache.org/10/documentation/streams/developer-guide/dsl-api.html)  
> [kafka streams 0.10.2](http://kafka.apache.org/0102/documentation/streams/)  

参考网址：
> [kafka Steams详解](http://blog.csdn.net/u013919201/article/details/78480668)  
> [kafka stream 简易示例代码](http://blog.csdn.net/lmh94604/article/details/53187935)  
>

生产：

    /bbd/ssd01/app/kafka/bin/kafka-console-producer.sh --broker-list 172.18.54.181:9095,183.131.54.182:9095,183.131.54.183:9095 \
    	--sync \
    	--topic topic_stream_input < input_file

消费：

      /bbd/ssd01/app/kafka/bin/kafka-console-consumer.sh --zookeeper 183.131.54.181:2181,183.131.54.182:2181,183.131.54.183:2181 \
          --topic topic_stream_output \
          --property print.key=true \
          --property print.value=true \
          --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer \
          --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer


> [Kafka三款监控工具比较(转) ](http://www.cnblogs.com/zdfjf/p/5717993.html)  
> [Confluent介绍（一）](http://www.cnblogs.com/zdfjf/p/5646525.html)  
> [Confluent介绍（二）](http://www.cnblogs.com/zdfjf/p/5696921.html)  
> [github 上一些kafka streams的例子](https://github.com/JohnReedLOL/kafka-streams/tree/master/src/main/java/io/confluent/examples/streams)
>

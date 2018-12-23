# 1. Kafka
## 1.1 安装

### 1.1.1 安装启动

- 下载
  ```
  wget https://archive.apache.org/dist/kafka/0.10.2.0/kafka_2.11-0.10.2.0.tgz
  ```
- 安装
  ```
  tar -zxvf kafka_2.11-0.10.2.0.tgz
  mkdir -p /zz/data/kafka
  ```
- 修改配置
  - 以下是master节点的配置，work节点的配置修改对应的`broker.id` `listeners` `advertised.listeners`
  - vim bin/kafka-server-start.sh
  ```
  export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G -Xmn1G -XX:PermSize=64m -XX:MaxPermSize=128m  -XX:SurvivorRatio=6  -XX:CMSInitiatingOccupancyFraction=70 -XX:+UseCMSInitiatingOccupancyOnly"
  export JMX_PORT="9999"
  ```
  - vim config/server.properties
  ```
  broker.id=0

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

  offsets.topic.num.partitions=6
  offsets.topic.replication.factor=3
  ```
- 启动
  ```
  nohup /zz/app/kafka/bin/kafka-server-start.sh /zz/app/kafka/config/server.properties &
  ```
- 查看进程
  ```
  jps|grep Kafka
  ```
- kill kafka进程
  ```
  bin/kafka-server-stop.sh
  #或者
  jps | grep Kafka | awk '{print $1}' | xargs kill -9
  ```


### 1.1.2 配置文件
- [kafka-server-start.sh](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/kafka/kafka-server-start.sh)
- [server.properties](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/kafka/server.properties)

### 1.1.3 kafka配置
replica.log.time.max.ms: 指定了副本在复制消息时可被允许的最大延迟时间


## 1.2 服务器是多线-配置
- 如果是多线（多运营商）的，修改：`listener.security.protocol.map` `listeners` `advertised.listeners`,其中ip1234分别是对应的移动、联通、电信、内网ip
  ```
  listener.security.protocol.map=PLAINTEXT:PLAINTEXT,PLAINTEXT1:PLAINTEXT,PLAINTEXT2:PLAINTEXT,PLAINTEXT3:PLAINTEXT
  listeners=PLAINTEXT://ip1:9092,PLAINTEXT1://ip2:9093,PLAINTEXT2://ip3:9094,PLAINTEXT3://ip4:9095
  advertised.listeners=PLAINTEXT://ip1:9092,PLAINTEXT1://ip2:9093,PLAINTEXT2://ip3:9094,PLAINTEXT3://ip4:9095
  ```

## 1.3 kafka相关命令
```
#创建主题
/zz/app/kafka/bin/kafka-topics.sh --create --topic test --replication-factor 1 --partitions 6 --zookeeper master:2181,work1:2181,work2:2181

#查看主题列表
/zz/app/kafka/bin/kafka-topics.sh --list --zookeeper master:2181,work1:2181,work2:2181

#kafka查看消费情况
/zz/app/kafka/bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --group test_g --zookeeper master:2181,work1:2181,work2:2181

#生产
/zz/app/kafka/bin/kafka-console-producer.sh --broker-list master:9092,work1:9092,work2:9092 --topic test

#消费
/zz/app/kafka/bin/kafka-console-consumer.sh --zookeeper master:2181,work1:2181,work2:2181 --topic test --from-beginning
/zz/app/kafka/bin/kafka-console-consumer.sh --bootstrap-server master:9092,work1:9092,work2:9092 --topic test --from-beginning

#从指定分区消费
/kafka-console-consumer.sh --bootstrap-server master:9092,work1:9092,work2:9092 --offset 101 --topic test --partition 0 --max-messages 10
```

## 参考地址
> [kakfa设计解析（一）- Kafka背景及架构介绍](http://www.jasongj.com/2015/03/10/KafkaColumn1/)


# 2 kafka-manager
## 2.1 编译、安装
### 2.1.1 下载源码编译
> 参考地址：[http://blog.csdn.net/isea533/article/details/73727485](http://blog.csdn.net/isea533/article/details/73727485)
> kafka-manager github地址：[https://github.com/yahoo/kafka-manager](https://github.com/yahoo/kafka-manager)
> 下载：wget https://github.com/yahoo/kafka-manager/archive/1.3.3.16.zip

### 2.1.2 配置sbt的maven仓库
- cd ~; mkdir .sbt

- `vim ~/.sbt/repositories`

- ```
  [repositories]
  local
  aliyun: http://maven.aliyun.com/nexus/content/groups/public
  typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  ```

- `./sbt clean dist # 进入kafka-manager源码`
> 配置参考：http://www.scala-sbt.org/0.13.2/docs/Detailed-Topics/Library-Management.html#override-all-resolvers-for-all-builds

### 2.1.3 解压编译后的包
```
cp /zz/app/kafka-manager-1.3.3.16_src/target/universal/kafka-manager-1.3.3.16.zip /zz/app
unzip kafka-manager-1.3.3.16.zip

ln -s  /zz/app/kafka-manager-1.3.3.16 /zz/app/kafka-manager
```

### 2.1.4 修改配置
```
cd kafka-manager
vim bin/kafka-manager  #加入JAVA_HOME，如果环境变量中有，不用设置了
vim conf/application.conf #修改kafka-manager.zkhosts，zookeeper的连接
```

### 2.1.5 启动
```
vim start.sh
rm -rf RUNNING_PID
kafka_manager_path=/zz/app/kafka-manager
nohup $kafka_manager_path/bin/kafka-manager -Dconfig.file=$kafka_manager_path/conf/application.conf -Dhttp.port=9001 &
```

### 2.1.6 编译问题
- 问题：无法下载依赖jar包
```

        ==== local: tried

          /root/.ivy2/local/org.scalamacros/quasiquotes_2.10/2.0.1/jars/quasiquotes_2.10.jar

        ==== aliyun: tried

          http://maven.aliyun.com/nexus/content/groups/public/org/scalamacros/quasiquotes_2.10/2.0.1/quasiquotes_2.10-2.0.1.jar

        ==== typesafe: tried

          http://repo.typesafe.com/typesafe/ivy-releases/org.scalamacros/quasiquotes_2.10/2.0.1/jars/quasiquotes_2.10.jar

                ::::::::::::::::::::::::::::::::::::::::::::::

                ::              FAILED DOWNLOADS            ::

                :: ^ see resolution messages for details  ^ ::

                ::::::::::::::::::::::::::::::::::::::::::::::

                :: org.scalamacros#quasiquotes_2.10;2.0.1!quasiquotes_2.10.jar

                ::::::::::::::::::::::::::::::::::::::::::::::



:: USE VERBOSE OR DEBUG MESSAGE LEVEL FOR MORE DETAILS
download failed: org.scalamacros#quasiquotes_2.10;2.0.1!quasiquotes_2.10.jar
Error during sbt execution: Error retrieving required libraries
  (see /root/.sbt/boot/update.log for complete log)
Error: Could not retrieve sbt 0.13.9
```
- 处理
```
wget http://maven.aliyun.com/nexus/content/groups/public/org/scalamacros/quasiquotes_2.10/2.0.1/quasiquotes_2.10-2.0.1.jar	
mv quasiquotes_2.10-2.0.1.jar /root/.ivy2/cache/org.scalamacros/quasiquotes_2.10/jars/
```


# 3. kafka streams

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
> [Kafka三款监控工具比较(转) ](http://www.cnblogs.com/zdfjf/p/5717993.html)  
> [Confluent介绍（一）](http://www.cnblogs.com/zdfjf/p/5646525.html)  
> [Confluent介绍（二）](http://www.cnblogs.com/zdfjf/p/5696921.html)  
> [github 上一些kafka streams的例子](https://github.com/JohnReedLOL/kafka-streams/tree/master/src/main/java/io/confluent/examples/streams)


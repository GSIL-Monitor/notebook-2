# 1. spark安装

## 1.1 下载
源码：wget https://www.apache.org/dyn/closer.lua/spark/spark-2.1.2/spark-2.1.2.tgz
二进制文件：wget https://www.apache.org/dyn/closer.lua/spark/spark-2.1.2/spark-2.1.2-bin-hadoop2.6.tgz

## 1.2 修改配置
- 已安装jdk、scala、hadoop

- vim /etc/profile
  ```
  export SCALA_HOME=/zz/app/scala
  export SPARK_HOME=/zz/app/spark

  export PATH=$SPARK_HOME/bin:$PATH
  ```

- slaves
  ```
  cp slaves.template slaves
  ```
  ```
  #vim slaves
  work1
  worke
  ```

- spark-env.sh
  ```
  cp spark-env.sh.template spark-env.sh
  ```

  ```
  #vim spark-env.sh
  export JAVA_HOME=/zz/app/jdk
  export SCALA_HOME=/zz/app/scala
  export HADOOP_HOME=/zz/app/hadoop
  export HADOOP_CONF_DIR=/zz/app/hadoop/etc/hadoop
  ```

- spark-defaults.conf
  ```
  spark.executor.extraJavaOptions         -XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"
  spark.eventLog.enabled                  true
  spark.eventLog.dir                      hdfs://master:9000/spark/historyserver
  spark.yarn.historyServer.address        master:18080
  spark.history.fs.logDirectory           hdfs://master:9000/spark/historyserver
  spark.default.parallelism              100
  ```

## 1.3 文件夹、权限
```
hdfs dfs -mkdir -p /spark/historyserver

chown -R hadoop:hadoop /zz/app/spark
chown -R hadoop:hadoop /zz/app/spark-2.1.0-bin-hadoop2.6
```

## 1.4 启动、停止
- 启动
  ```
  ./sbin/start-all.sh
  ./sbin/start-history-server.sh
  ```
- 停止
  ```
  ./sbin/stop-all.sh
  ```

## 1.5 web ui
- 主页面
  http://master:8080/

- 历史：
  http://master:18080/

- 主从验证：
  http://master:8080/
    Status: ALIVE
  http://work1:8080/
    Status: STANDBY

- spark-shell
  http://master:4040

## 1.6 shell、sql
- spark-shell
  ```
  ./bin/spark-shell
  ```
- spark-sql
  ```
  ./bin/spark-sql
  ```

# 2. 相关概念
## 2.1 RDD
RDD：弹性 分布式 数据集
- 弹性：
  - 虽然 RDD 内部存储的数据是只读的，但是，我们可以去修改（例如通过 repartition、coalesce 转换操作）并行计算计算单元的划分结构，也就是分区的数量。
  - 当集群中的一台机器挂掉而导致存储在其上的RDD丢失后，Spark还可以重新计算出这部分的分区的数据。但用户感觉不到这部分的内容丢失过。这样RDD数据集就像块带有弹性的海绵一样，不管怎样挤压（分区遭到破坏）都是完整的
- 分布式：RDD特性中的 支持跨集群的分布式数据操作
- 数据集：数据集合的抽象
## 2.2 repatition和coalesce区别
- 都是进行RDD的重新分区操作
- repartition只是coalesce接口中shuffle为true的简易实现

  ------

- 如果父RDD为1000分区，filter转换操作后过滤了50%的数据，想把数据重新分布在500分区中，这个时候是去减少分区这个时候使用coalesce(500)，能够避免引起shuffle
- 分区由少变多，或者在一些不是键值对的ＲＤＤ中想要重新分区的话，就需要使用repartition了

  ------

- 他们两个都是RDD的分区进行重新划分，repartition只是coalesce接口中shuffle为true的简易实现，（假设RDD有N个分区，需要重新划分成M个分区）
- 1）、N<M。一般情况下N个分区有数据分布不均匀的状况，利用HashPartitioner函数将数据重新分区为M个，这时需要将shuffle设置为true。
- 2）如果N>M并且N和M相差不多，(假如N是1000，M是100)那么就可以将N个分区中的若干个分区合并成一个新的分区，最终合并为M个分区，这时可以将shuff设置为false，在shuffl为false的情况下，如果M>N时，coalesce为无效的，不进行shuffle过程，父RDD和子RDD之间是窄依赖关系。
- 3）如果N>M并且两者相差悬殊，这时如果将shuffle设置为false，父子ＲＤＤ是窄依赖关系，他们同处在一个Ｓｔａｇｅ中，就可能造成spark程序的并行度不够，从而影响性能，如果在M为1的时候，为了使coalesce之前的操作有更好的并行度，可以讲shuffle设置为true。
- 总之：如果shuff为false时，如果传入的参数大于现有的分区数目，RDD的分区数不变，也就是说不经过shuffle，是无法将RDDde分区数变多的。

## 2.3 Lineage
源码实例：wordcount中flatMap中的创建MapPartitionRDD时传入this（当前RDD，是hadoopRDD，也就知道了上一个rdd是谁）
## 2.4 RDD的缓存

# 3. 相关流程
## 3.1 worldcount执行流程
## 3.2 spark提交任务流程



# 4. spark hive整合
## 整合
- 配置文件
  hive-site复制到spark conf下
  cp /zz/app/hive/conf/hive-site.xml /zz/app/spark/conf

- 添加mysql驱动到spark jars里
  ln -s /zz/app/mysql-connector-java-5.1.45-bin.jar /zz/app/spark/jars/mysql-connector-java-5.1.45-bin.jar

## 参考地址
spark-shell 中查询hive：http://dblab.xmu.edu.cn/blog/1086-2/
spark-sql 中查询hive：https://www.cnblogs.com/juncaoit/p/6545092.html



# 资源地址
[spark基本架构及原理](http://www.cnblogs.com/tgzhu/p/5818374.html)

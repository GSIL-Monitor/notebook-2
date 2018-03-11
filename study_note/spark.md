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
  worke1
  worke2
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

# 2. spark hive整合

## 2.1 整合
- 配置文件
  hive-site复制到spark conf下
  cp /zz/app/hive/conf/hive-site.xml /zz/app/spark/conf

- 添加mysql驱动到spark jars里
  ln -s /zz/app/mysql-connector-java-5.1.45-bin.jar /zz/app/spark/jars/mysql-connector-java-5.1.45-bin.jar

## 2.2 参考地址
spark-shell 中查询hive：http://dblab.xmu.edu.cn/blog/1086-2/
spark-sql 中查询hive：https://www.cnblogs.com/juncaoit/p/6545092.html



# 资源地址
[spark基本架构及原理](http://www.cnblogs.com/tgzhu/p/5818374.html)

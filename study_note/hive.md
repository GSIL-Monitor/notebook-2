# 1. 安装
## 1.1 下载
wget https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-1.2.2/apache-hive-1.2.2-bin.tar.gz

## 1.2 vim hive-site.xml
- cp conf/hive-default.xml.template conf/hive-site.xml，hive-site.xml配置如下：
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
	<name>javax.jdo.option.ConnectionURL</name>
	<value>jdbc:mysql://master:3306/hive?createDatabaseIfNotExist=true</value>
	<description>JDBC connect string for a JDBC metastore</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionDriverName</name>
	<value>com.mysql.jdbc.Driver</value>
	<description>Driver class name for a JDBC metastore</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionUserName</name>
	<value>hive</value>
	<description>username to use against metastore database</description>
</property>
<property>
	<name>javax.jdo.option.ConnectionPassword</name>
	<value>hive</value>
	<description>password to use against metastore database</description>
</property>

<property>
	<name>hive.metastore.warehouse.dir</name>
	<value>hdfs://master:9000/hive/warehouse</value>
</property>
<property>
	<name>hive.exec.scratchdir</name>
	<value>hdfs://master:9000/hive/scratchdir</value>
</property>

<property>
	<name>hive.server2.logging.operation.log.location</name>
	<value>/zz/data/hive/operation_logs</value>
	<description>Top level directory where operation logs are stored if logging functionality is enabled</description>
</property>
<property>
	<name>hive.exec.local.scratchdir</name>
	<value>/zz/data/hive/scratchdir</value>
	<description>Local scratch space for Hive jobs</description>
</property>
<property>
	<name>hive.downloaded.resources.dir</name>
	<value>/zz/data/hive/resources</value>
	<description>Temporary local directory for added resources in the remote file system.</description>
</property>
<property>
	<name>hive.querylog.location</name>        
	<value>/zz/data/hive/querylog</value>    
</property>
</configuration>
```

## 1.3 vim /etc/profile
```
export HIVE_HOME=/zz/app/hive
export PATH=$HIVE_HOME/bin:$PATH
```

## 1.4 hive-env.sh
- cp hive-env.sh.template hive-env.sh
```
export HADOOP_HOME=/zz/app/hadoop
export SPARK_HOME=/zz/app/spark
export HIVE_HOME=/zz/app/hive
export HIVE_CONF_DIR=/zz/app/hive/conf
```

## 1.5 hive-config.sh
- vim hive-config.sh
```
export JAVA_HOME=/zz/app/jdk
export HADOOP_HOME=/zz/app/hadoop
export SPARK_HOME=/zz/app/spark
export HIVE_HOME=/zz/app/hive
```

## 1.6 创建目录
```
#创建目录
hadoop fs -mkdir -p /hive/warehouse
hadoop fs -mkdir -p /hive/scratchdir

hdfs dfs -chmod g+w /hive/warehouse
hdfs dfs -chmod g+w /hive/scratchdir
hdfs dfs -chmod 777 /hive/scratchdir

#各节点都创建对应的目录
mkdir -p /zz/data/hive/operation_logs
mkdir -p /zz/data/hive/scratchdir
mkdir -p /zz/data/hive/resources
mkdir -p /zz/data/hive/querylog

chown -R hadoop:hadoop /zz/data/hive
chown -R hadoop:hadoop /zz/app/apache-hive-1.2.2-bin
```

## 1.7 添加mysql驱动
```
cd /zz/app/hive/lib
ln -s /zz/app/mysql-connector-java-5.1.45-bin.jar mysql-connector-java-5.1.45-bin.jar
/zz/shell/scp.sh lib/mysql-connector-java-5.1.45-bin.jar /zz/app/hive/lib/
```

## 1.8 创建mysql中hive用户
```
grant all privileges on *.* to hive@"%" identified by "hive" with grant option;
flush privileges;
```

## 1.9 替换hadoop低版本的jar包jline
```
mv hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar.bak
cp hive/lib/jline-2.12.jar hadoop/share/hadoop/yarn/lib
```

## 1.9 启动测试
```
nohup hive --service metastore -v &

#shell 客户端
hive
```

## 1.10 hive log配置
- 默认位置
  默认是在目录：/tmp/${user.name}
  如果hadoop用户启动的hive，则目录在/tmp/hadoop

- vim conf/hive-site.xml，修改下面的配置
  ```
  hive.log.dir=/zz/app/hive/logs
  ```


## 1.11 安装参考地址
https://www.jianshu.com/p/763d5c665a23
http://kevin12.iteye.com/blog/2280777
http://blog.csdn.net/linlinv3/article/details/49512587




# 2. 异常
## 2.1 启动异常
### 2.1.1 xml解析异常
org.xml.sax.SAXParseException: The markup in the document following the root element must be well-fo
处理：检查xml文件头

### 2.1.2 mysql驱动异常
javax.jdo.JDOFatalInternalException: Error creating transactional connection
处理：mysql驱动放到hive/lib下

### 2.1.3 hdfs权限问题
Exception in thread "main" java.lang.RuntimeException: java.lang.RuntimeException: The root scratch dir: hdfs://master:9000/hive/scratchdir on HDFS should be writable. Current permissions are: rwxrwxr-x
处理：hdfs dfs -chmod 777 /hive/scratchdir


### 2.1.4 jar包版本问题
ERROR] Terminal initialization failed; falling back to unsupported
java.lang.IncompatibleClassChangeError: Found class jline.Terminal, but interface was expected
处理：
```
mv hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar.bak
cp hive/lib/jline-2.12.jar hadoop/share/hadoop/yarn/lib
```





# 1. 安装

## 1.1 下载
源码：wget https://archive.apache.org/dist/hadoop/common/hadoop-2.6.5/hadoop-2.6.5-src.tar.gz
二进制文件：wget https://archive.apache.org/dist/hadoop/common/hadoop-2.6.5/hadoop-2.6.5.tar.gz

## 1.2 修改配置
- core-site.xml
```
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://master:9000</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>file:/zz/data/hadoop/tmp</value>
		<description>Abase for other temporary directories.</description>
	</property>
</configuration>
```

- hdfs-site.xml
```
<configuration>
	<!-- 设置hdfs副本数量 -->
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
	
	<!-- 设置namenode的http通讯地址 -->
	<property>
		<name>dfs.namenode.http-address</name>
		<value>master:50070</value>
	</property>

	<!-- 设置secondarynamenode的http通讯地址 -->
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>work1:50090</value>
	</property>
	
	<!-- 设置namenode存放的路径 -->
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>/zz/data/hadoop/dfs/namenode</value>
	</property>
	
	<!-- 设置datanode存放的路径 -->
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>/zz/data/hadoop/dfs/datanode</value>
	</property>
	
	<property>
		<name>dfs.namenode.checkpoint.dir</name>
		<value>file:///usr/local/software/hadoop/dfs/namesecondary</value>
		<description>Determines where on the local filesystem the DFSsecondary name node should store the temporary images to merge. If this is acomma-delimited list of directories then the image is replicated in all of thedirectories for redundancy.</description>
	</property>
</configuration>
```

- mapred-site.xml
```
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.address</name>
		<value>master:10020</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>master:19888</value>
	</property>
</configuration>
```

- yarn-site.xml
```
<configuration>
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>master</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
```

- hadoop-env.sh、yarn-env.sh加入JAVA_HOME
```
export JAVA_HOME=/zz/app/jdk
```

- vim etc/hadoop/slaves
```
work1
work2
```

- 同步配置
```
/zz/shell/scp.sh core-site.xml /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh hdfs-site.xml /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh mapred-site.xml /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh yarn-site.xml /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh hadoop-env.sh /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh yarn-env.sh /zz/app/hadoop/etc/hadoop/
/zz/shell/scp.sh slaves /zz/app/hadoop/etc/hadoop/
```

## 1.3 添加用户和组
- 添加用户、组
```
groupadd hadoop
useradd -s /bin/bash -d /home/hadoop -m hadoop -g hadoop
passwd hadoop
```
- 修改权限
```
mkdir -p /zz/data/hadoop/tmp
mkdir -p /zz/data/hadoop/dfs/namenode
mkdir -p /zz/data/hadoop/dfs/datanode
mkdir -p /zz/data/hadoop/dfs/namesecondary
chown -R hadoop:hadoop /zz/data/hadoop
chown -R hadoop:hadoop /zz/app/hadoop
chown -R hadoop:hadoop /zz/app/hadoop-2.6.5
```

## 1.4 环境变量
```
vim /etc/profile
export JAVA_HOME=/zz/app/jdk
export HADOOP_HOME=/zz/app/hadoop
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```
`source /etc/profile`

## 1.5 切换到hadoop用户，配置ssh
```
ssh-keygen
ssh-copy-id work1 #复制公匙到work1上
```

## 1.6 启动
- haoop用户格式化namenode（主节点和分节点都格式化）
```
./bin/hdfs namenode -format
```
- haoop用户启动：
```
./sbin/start-dfs.sh
./sbin/start-yarn.sh
```

- 进程
  - master
    NameNode
    SecondaryNameNode
    ResourceManager
  - work1
    DataNode
    NodeManager
  - work2
    DataNode
    NodeManager

## 1.7 run 自带wordcount
```
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.5.jar wordcount /input/wordcount /output/wordCount
```
## 1.8 web ui
- 查看hdfs
  http://master:50070
- 查看RM
  http://master:8088

## 1.9 window本地运行mr
- 环境变量
  ```
  HADOOP_HOME
  C:\study\tools_free\bigdata\hadoop\hadoop-2.6.0
  
  path
  %HADOOP_HOME%\bin
  %HADOOP_HOME%\sbin
  ```

- 添加文件到bin目录
  > winutils.exe
  > hadoop.dll

- 放文件到system32下

  将hadoop.dll放到c:/windows/System32下

- 注销window
  重新运行mr任务


# 2. 异常

## 2.1 安装异常
### 2.1.1 ssh问题：
没有生成authorized_keys，复制一个
`cp id_rsa.pub authorized_keys`
使用的时候直接cat时，ssh localhost不通，还是需要密码，使用cp后问题解决

### 2.1.2 安装后命令有warn
- 异常信息：
  18/02/24 09:38:15 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
- 处理：
  log4j.logger.org.apache.hadoop.util.NativeCodeLoader=ERROR

## 2.2  运行异常
### 2.2.1 运行mr连不上master
- 异常信息：
  18/02/24 10:03:51 INFO client.RMProxy: Connecting to ResourceManager at master/192.168.229.129:8032
  18/02/24 10:03:52 INFO ipc.Client: Retrying connect to server: master/192.168.229.129:8032. Already tried 0 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
  18/02/24 10:03:53 INFO ipc.Client: Retrying connect to server: master/192.168.229.129:8032. Already tried 1 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
- 处理
  没有启动yarn导致，启动yarn

### 2.2.2 mr内存不足
- 异常：
```
18/01/31 15:29:09 INFO mapreduce.Job: Task Id : attempt_1513065790067_2120_m_000259_0, Status : FAILED
Container [pid=113078,containerID=container_e44_1513065790067_2120_01_000248] is running beyond physical memory limits. Current usage: 1.0 GB of 1 GB physical memory used; 4.5 GB of 2.1 GB virtual memory used. Killing container.
Dump of the process-tree for container_e44_1513065790067_2120_01_000248 :
        |- PID PPID PGRPID SESSID CMD_NAME USER_MODE_TIME(MILLIS) SYSTEM_TIME(MILLIS) VMEM_USAGE(BYTES) RSSMEM_USAGE(PAGES) FULL_CMD_LINE
        |- 113131 113078 113078 113078 (java) 7113 597 4775051264 264593 /usr/java/jdk1.7.0_67-cloudera/bin/java -Djava.net.preferIPv4Stack=true -Dhadoop.metrics.log.level=WARN -Djava.net.preferIPv4Stack=true -Xmx820m -Djava.io.tmpdir=/bbd/sata05/yarn/nm/usercache/hdfs/appcache/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/bbd/sata01/yarn/container-logs/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog org.apache.hadoop.mapred.YarnChild 172.18.77.115 59885 attempt_1513065790067_2120_m_000259_0 48378511622392
        |- 113078 113067 113078 113078 (bash) 0 0 108666880 306 /bin/bash -c /usr/java/jdk1.7.0_67-cloudera/bin/java -Djava.net.preferIPv4Stack=true -Dhadoop.metrics.log.level=WARN  -Djava.net.preferIPv4Stack=true -Xmx820m -Djava.io.tmpdir=/bbd/sata05/yarn/nm/usercache/hdfs/appcache/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/bbd/sata01/yarn/container-logs/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog org.apache.hadoop.mapred.YarnChild 172.18.77.115 59885 attempt_1513065790067_2120_m_000259_0 48378511622392 1>/bbd/sata01/yarn/container-logs/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248/stdout 2>/bbd/sata01/yarn/container-logs/application_1513065790067_2120/container_e44_1513065790067_2120_01_000248/stderr
Container killed on request. Exit code is 143
Container exited with a non-zero exit code 143
```
- 处理:
  http://cache.baiducontent.com/c?m=9f65cb4a8c8507ed19fa950d100b96204a05d93e788090412189d65f93130a1c187ba0fc7063565f8e993d7a01ac4b5eedf430732a527debdd9bd3168afb852859d36623706ac6064e8f42f39e5125b770c711b4f34ff0ba866dc4f18f93944353bc0d5e25dbb6ca4e45498a39ff456cbfe0ca13490850aded413ebe4e7758df641cea1ca5b5256e76d1a1960110953dd01053cde474eb6f41eb04a46e44731bfd0cfa1f51576ebb107cb9052a05e2fc5d903d793172f108f3ee9df0fc48fd9bab62cdaddcae5f826195d2ea8872557122ed25c9bcbcc23f7345738c9ac916b56fe8&p=976fc64ad49c11a05bed9437455786&newp=9e759a46d6c333fc57efd0214f0886231610db2151d4d7156b82c825d7331b001c3bbfb423251303d8c0796607a54a5aeaf233713d012ba3dda5c91d9fb4c57479fa&user=baidu&fm=sc&query=Container+is+running+beyond+physical+memory+limits%2E+Current+usage%3A+1%2E0+GB+of+1+GB+physical+memory+used&qid=e932452900015c27&p1=1

```
#加大mr的内存
conf.set("mapreduce.map.memory.mb", "2048");
```
### 2.2.3 mr window本地运行异常
- 参考地址：
  https://blog.csdn.net/congcong68/article/details/42043093
  https://blog.csdn.net/cor_twi/article/details/40891393

  ```
  //设置hadoop的家目录
  System.setProperty("hadoop.home.dir", "D:\\bigdata\\tools-free\\hadoop\\hadoop-2.6.0");
  System.setProperty("HADOOP_MAPRED_HOME", "D:\\bigdata\\tools-free\\hadoop\\hadoop-2.6.0");

  //强制加载hadoop.dll; https://blog.csdn.net/cor_twi/article/details/40891393
  System.load("D:\\bigdata\\tools-free\\hadoop\\hadoop-2.6.0\\bin\\hadoop.dll");
  ```

- 异常处理
  1. hadoop.dll、winutils.exe 放到hadoop的bin目录，hadoop.dll放到window/system32下面；
  2. 配置hadoop环境变量
  3. 修改 src/org/apache/hadoop/io/nativeio/NativeIO.java 中的557 行，返回true
  - 如果本地运行mr还是异常，考虑强行加载hadoop.dll 和 hadoop家目录
  - 还有一种可能异常：hadoop.dll 是32位，jdk是64位，那么就要换hadoop.dll（如果有这个问题，会在强行加载hadoop.dll时报位数异常）
------
- 异常信息
本地运行报：nativeio.NativeIO$Windows.createDirectoryWithMode0(Ljava/lang/String;I)
- 处理
参考：https://www.cnblogs.com/pu20065226/p/8583267.html  
删除`c:\windows\system32下放hadoop.dll`

# 3. map、reduce
## 3.1 向map、reduce传递参数
[hadoop 如何向map和reduce脚本传递参数,加载文件和目录](https://www.cnblogs.com/zhengrunjian/p/4536572.html)  


# 4. it-hadoop
## 4.1 本地mr用户问题
![](../../study_note_access/hadoop/hadoop本地操作虚拟机中的hdfs用户问题.png)


## 4.2 hdfs写文件
![](../../study_note_access/hadoop/hdfs写数据流程示意图.png)


## 4.2 hdfs读文件
![](../../study_note_access/hadoop/hdfs读数据流程示意图.png)


## 4.3 namenode和secondarynamenode的元数据管理机制
![](../../study_note_access/hadoop/secondarynamenode元数据checkpoint机制.png)


# 五、hadoop 命令
## 1.1 hadoop job相关命令
- 参考地址：  
[hadoop命令 -- job相关](https://www.cnblogs.com/zyzloner/p/6405290.html)
1. 查看 Job 信息：
  ```
  hadoop job -list 
  ```
2. 杀掉 Job： 
  ```
  hadoop  job –kill  job_id
  ```
3. 指定路径下查看历史日志汇总：
  ```
  hadoop job -history output-dir 
  ```
4. 作业的更多细节： 
  ```
  hadoop job -history all output-dir 
  ```
5. 打印map和reduce完成百分比和所有计数器：
  ```
  hadoop job –status job_id 
  ```
6. 杀死任务。被杀死的任务不会不利于失败尝试：
  ```
  hadoop jab -kill-task <task-id> 
  ```
7. 使任务失败。被失败的任务会对失败尝试不利：
  ```
  hadoop job  -fail-task <task-id>
  ```

# 六. hdfs写数据

```java
// https://blog.csdn.net/qq_28767763/article/details/65444975
Configurationconf = new Configuration();
 
conf.set("fs.hdfs.impl", org.apache.hadoop.hdfs.DistributedFileSystem.class.getName());
conf.set("fs.file.impl",org.apache.hadoop.fs.LocalFileSystem.class.getName());
conf.setBoolean("dfs.support.append",true);    
 
hdfs = FileSystem.get(conf);
 
String hdfs ="hdfs://10.20.30.91:8020/kafka/a.log";
filePath = new Path(hdfs);
 
FSDataOutputStream outputStream = hdfs.create(filePath);
 
outputStream.write(“hello”)
 
outputStream.flush();
 
outputStream.close();
hdfs.close();
```


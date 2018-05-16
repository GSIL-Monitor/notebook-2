# 1. 安装

- 已安装 hadoop、zookeep

## 1.1 下载：
wget http://mirror.bit.edu.cn/apache/hbase/1.2.6/hbase-1.2.6-bin.tar.gz

## 1.3 修改配置
- hbase-env.sh
```
export JAVA_HOME=/zz/app/jdk
export HBASE_MANAGES_ZK=false
```

- hbase-site.xml
```
<configuration>
	<property>
		<name>hbase.master</name>
		<value>master:60000</value>
	</property>
	<!-- 主备间隔时间 -->
	<property>
		<name>hbase.master.maxclockskew</name> 
		<value>180000</value>
	</property>
	
	<property>
		<name>hbase.rootdir</name>
		<value>hdfs://master:9000/hbase</value>
	</property>
	<property>
		<name>hbase.cluster.distributed</name>
		<value>true</value>
	</property>
	
	<property>
		<name>hbase.zookeeper.quorum</name>
		<value>master,work1,work2</value>
	</property>
	<property>
		<name>hbase.zookeeper.property.dataDir</name>
		<value>/servers/hbase/zookeeper</value>
	</property>
</configuration>
```

- vim conf/regionservers
```
work1
work2
```

## 1.3 同步、修改权限
```
/zz/shell/scp.sh /etc/profile /etc/
/zz/shell/scp.sh hbase/conf/hbase-site.xml /zz/app/hbase/conf/
/zz/shell/scp.sh hbase/conf/hbase-env.sh /zz/app/hbase/conf/

chown -R hadoop:hadoop /zz/app/hbase
chown -R hadoop:hadoop /zz/app/hbase-1.2.6
```
`source /etc/profile`

## 1.4 启动、停止
```
#启动
./bin/start-hbase.sh
#停止
./bin/stop-hbase.sh
```
## 1.5 进程
- master节点
  HMaster
- work节点
  HRegionServer

## 1.6 web ui
- HMaster
  http://master:16010
- RegionServer
  http://master:16030

## 1.7 jdk、hadoop支持

### 1.7.1 This section lists required services and some required system configuration.

| HBase Version | JDK 7                                    | JDK 8                                    |
| ------------- | ---------------------------------------- | ---------------------------------------- |
| 2.0           | [Not Supported](http://search-hadoop.com/m/YGbbsPxZ723m3as) | yes                                      |
| 1.3           | yes                                      | yes                                      |
| 1.2           | yes                                      | yes                                      |
| 1.1           | yes                                      | Running with JDK 8 will work but is not well tested. |

HBase will neither build nor compile with Java 6.
You must set `JAVA_HOME` on each node of your cluster. *hbase-env.sh* provides a handy mechanism to do this.


### 1.7.2 Hadoop version support matrix

- "S" = supported
- "X" = not supported
- "NT" = Not tested

|                    | HBase-1.1.x | HBase-1.2.x | HBase-1.3.x | HBase-2.0.x |
| ------------------ | ----------- | ----------- | ----------- | ----------- |
| Hadoop-2.0.x-alpha | X           | X           | X           | X           |
| Hadoop-2.1.0-beta  | X           | X           | X           | X           |
| Hadoop-2.2.0       | NT          | X           | X           | X           |
| Hadoop-2.3.x       | NT          | X           | X           | X           |
| Hadoop-2.4.x       | S           | S           | S           | X           |
| Hadoop-2.5.x       | S           | S           | S           | X           |
| Hadoop-2.6.0       | X           | X           | X           | X           |
| Hadoop-2.6.1+      | NT          | S           | S           | S           |
| Hadoop-2.7.0       | X           | X           | X           | X           |
| Hadoop-2.7.1+      | NT          | S           | S           | S           |
| Hadoop-2.8.0       | X           | X           | X           | X           |
| Hadoop-2.8.1       | X           | X           | X           | X           |
| Hadoop-3.0.0       | NT          | NT          | NT          | NT          |


# 2. 
主HMaster：不存任何信息，只管理HReginServer挂掉后数据迁移和表的信息，不管理数据信息；
集群运行起来，表创建完了，HMaster挂了照样跑

从HReginServer：缓存数据

![hbase架构图](D:\study\mygit\notebook\study_note_access\hbase\hbase架构图.png)
- hbase是写快，读慢（慢是相对于写来说的）
- 写数据
  - 来了一条数据通过zookeeper定位写到哪台HReginServer上
  - 
- 读数据

# 3. 

hbase shell
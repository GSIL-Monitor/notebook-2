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
### 1.9.1 方式一单机的启动
- 启动：`nohup hive --service metastore -v &`
- 连接shell 客户端: `bin/hive`

### 1.9.2 作为服务启动
- 启动
  ```
  nohup /zz/app/hive/bin/hiveserver2 1 > /zz/app/hive/logs/hiveserver2.log 2>/zz/app/hive/logs/hiveserver2.error.log &
  ```
- 连接
  ```
  #master是服务器名
  bin/beeline -u jdbc:hive2://master:10000 -n hadoop
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
```
org.xml.sax.SAXParseException: The markup in the document following the root element must be well-fo
```
处理：检查xml文件头

### 2.1.2 mysql驱动异常
```
javax.jdo.JDOFatalInternalException: Error creating transactional connection
```
处理：mysql驱动放到hive/lib下

### 2.1.3 hdfs权限问题
```
Exception in thread "main" java.lang.RuntimeException: java.lang.RuntimeException: The root scratch dir: hdfs://master:9000/hive/scratchdir on HDFS should be writable. Current permissions are: rwxrwxr-x
```
处理：hdfs dfs -chmod 777 /hive/scratchdir


### 2.1.4 jar包版本问题
```
ERROR] Terminal initialization failed; falling back to unsupported
java.lang.IncompatibleClassChangeError: Found class jline.Terminal, but interface was expected
```
- 处理：
  ```
  mv hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar hadoop/share/hadoop/yarn/lib/jline-0.9.94.jar.bak
  cp hive/lib/jline-2.12.jar hadoop/share/hadoop/yarn/lib
  ```

### 2.1.5 启动无法加载spark jar包
- 问题：
  ```
  初次启动hive,解决 ls: cannot access /home/hadoop/spark-2.2.0-bin-hadoop2.6/lib/spark-assembly-*.jar: No such file or directory问题
  ```
- 处理：
  ```
  #修改前
  sparkAssemblyPath=`ls ${SPARK_HOME}/lib/spark-assembly-*.jar`
  #修改后
  sparkAssemblyPath=`ls ${SPARK_HOME}/jars/*.jar`
  ```

# 3 Hive架构

## 3.1
![hive架构图](D:\study\mygit\notebook\study_note_access\hive\hive架构图.png)

- 解析sql，得到语法树，根据语法树找对应的mapreduce模版，然后把它组装起来
- 它有内部的优化机制：
  - 例如join操作: 以join的条件作为key，把2个表的数据发到reduce，然后在reduce端做聚合
  - 如果2个表join的时候，有一个表很小（有阈值，小于这个值），自动开启map端的join操作（把小表的数据加载到内存中）map端出结果
- 组装无非就是：传入一些参数，你的数据在哪里，你表数据的分隔符是什么，你的文件格式是什么（这些在建表的时候就指定了，它都知道，就可以跑起来了）


# 4. Hive使用

## 4.1 sort by 和 order by
- 使用`sort by` 取最大值，是有问题的，可能是分多个区，再用 `limit 1` 就会有问题；Sort By，它通常发生在每一个redcue里，只是保证了输出的部分有序
- 使用 `order by` 是全局有序的 ，再使用 `limit 1`
- Order By ，在strict 模式下（hive.mapred.mode=strict),order by 语句必须跟着limit语句，但是在nonstrict下就不是必须的，这样做的理由是必须有一个reduce对最终的结果进行排序，如果最后输出的行数过多，一个reduce需要花费很长的时间。
  - `hive> set hive.mapred.mode=strict;`   #<默认nonstrict>

## 4.2 保存select查询结果3种方式
- 将查询结果保存到一张新的hive表中
  ```
  create table t_tmp
  as
  select * from t_p;
  ```

- 将查询结果保存到一张已经存在的hive表中
  ```
  insert into  table t_tmp
  select * from t_p;
  ```

- 将查询结果保存到指定的文件目录（可以是本地，也可以是hdfs）
  ```
  insert overwrite local directory '/home/hadoop/test'
  select * from t_p;

  #hdfs上
  insert overwrite directory '/aaa/test'
  select * from t_p;
  ```

## 4.3 自定义UDF
- 先开发一个java类，继承UDF，并重载evaluate方法
  ```
  package cn.itcast.bigdata.udf
  import org.apache.hadoop.hive.ql.exec.UDF;
  import org.apache.hadoop.io.Text;

  public final class Lower extends UDF{
  	public Text evaluate(final Text s){
  		if(s==null){return null;}
  		return new Text(s.toString().toLowerCase());
  	}
  }
  ```
- 打成jar包上传到服务器
- 将jar包添加到hive的classpath
  - `hive>add JAR /home/hadoop/udf.jar;`
- 创建临时函数与开发好的java class关联
  - `Hive>create temporary function toprovince as 'cn.itcast.bigdata.udf.ToProvince';`
- 即可在hql中使用自定义的函数
  - `Select toprovince (name),age from t_test;`

## 4.4 UDF案例
### 4.4.1 json数据分字段入表
- 日志
  `{"movie":"1721","rate":"3","timeStamp":"965440048","uid":"5114"}`
- 开发UDF
  - MovieRateBean.java
    ```
    package cn.itcast.bigdata.udf;

    //{"movie":"1721","rate":"3","timeStamp":"965440048","uid":"5114"}
    public class MovieRateBean {

    	private String movie;
    	private String rate;
    	private String timeStamp;
    	private String uid;
    	public String getMovie() {
    		return movie;
    	}
    	public void setMovie(String movie) {
    		this.movie = movie;
    	}
    	public String getRate() {
    		return rate;
    	}
    	public void setRate(String rate) {
    		this.rate = rate;
    	}
    	public String getTimeStamp() {
    		return timeStamp;
    	}
    	public void setTimeStamp(String timeStamp) {
    		this.timeStamp = timeStamp;
    	}
    	public String getUid() {
    		return uid;
    	}
    	public void setUid(String uid) {
    		this.uid = uid;
    	}
    	@Override
    	public String toString() {
    		return movie + "\t" + rate + "\t" + timeStamp + "\t" + uid;
    	}
    }

    ```
  - JsonParser.java
    ```
    package cn.itcast.bigdata.udf;

    import org.apache.hadoop.hive.ql.exec.UDF;

    import parquet.org.codehaus.jackson.map.ObjectMapper;

    public class JsonParser extends UDF {

    	public String evaluate(String jsonLine) {

    		ObjectMapper objectMapper = new ObjectMapper();

    		try {
    			MovieRateBean bean = objectMapper.readValue(jsonLine, MovieRateBean.class);
    			return bean.toString();
    		} catch (Exception e) {

    		}
    		return "";
    	}

    }
    ```
- 处理数据到表里
  ```
  #先把原始数据加载到一个表里（字段是json字符串）
  create table rat_json(line string) row format delimited;
  load data local inpath '/home/hadoop/rating.json' into table rat_json;

  #创建放分字段的表
  drop table if exists t_rating;
  create table t_rating(movieid string,rate int,timestring string,uid string)
  row format delimited fields terminated by '\t';

  #利用自带的split函数处理解析后的json数据（自定义的UDF中解析json，并拼接成\t分隔的字符串）
  insert overwrite table t_rating
  select split(parsejson(line),'\t')[0]as movieid,split(parsejson(line),'\t')[1] as rate,split(parsejson(line),'\t')[2] as timestring,split(parsejson(line),'\t')[3] as uid from rat_json limit 10;
  ```

### 4.4.2 使用内置jason函数
- 使用内置函数替代上面的自定义UDF
```
select get_json_object(line,'$.movie') as moive,get_json_object(line,'$.rate') as rate  from rat_json limit 10;
```
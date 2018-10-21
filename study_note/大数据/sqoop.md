# 一、sqoop异常  
## 1. 字段对应不上
- 异常信息：  
```
2018-09-04 16:02:30,854 WARN [main] org.apache.hadoop.mapred.YarnChild: Exception running child : java.io.IOException: Can't export data, please check failed map task logs
	at org.apache.sqoop.mapreduce.TextExportMapper.map(TextExportMapper.java:122)
	at org.apache.sqoop.mapreduce.TextExportMapper.map(TextExportMapper.java:39)
	at org.apache.hadoop.mapreduce.Mapper.run(Mapper.java:145)
	at org.apache.sqoop.mapreduce.AutoProgressMapper.run(AutoProgressMapper.java:64)
	at org.apache.hadoop.mapred.MapTask.runNewMapper(MapTask.java:787)
	at org.apache.hadoop.mapred.MapTask.run(MapTask.java:341)
	at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:164)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1796)
	at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:158)
Caused by: java.lang.RuntimeException: Can't parse input data: 'fasdfa'
	at zz_test.__loadFromFields(zz_test.java:267)
	at zz_test.parse(zz_test.java:210)
	at org.apache.sqoop.mapreduce.TextExportMapper.map(TextExportMapper.java:89)
	... 10 more
Caused by: java.util.NoSuchElementException
	at java.util.ArrayList$Itr.next(ArrayList.java:860)
	at zz_test.__loadFromFields(zz_test.java:262)
	... 12 more

2018-09-04 16:02:30,858 INFO [main] org.apache.hadoop.mapred.Task: Runnning cleanup for the task
```
- 异常原因：  
  - 数据中有空值的，文件中字段个数和关系数据库表中的字段对不上


# 二、sqoop 使用
## 1. 添加-D参数打出异常数据
```
-D org.apache.sqoop.export.text.dump_data_on_error=true \
```
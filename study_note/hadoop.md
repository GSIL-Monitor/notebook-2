# **hadoop**

> [hadoop 如何向map和reduce脚本传递参数,加载文件和目录](https://www.cnblogs.com/zhengrunjian/p/4536572.html)  
>





hadoop异常：
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
处理:

http://cache.baiducontent.com/c?m=9f65cb4a8c8507ed19fa950d100b96204a05d93e788090412189d65f93130a1c187ba0fc7063565f8e993d7a01ac4b5eedf430732a527debdd9bd3168afb852859d36623706ac6064e8f42f39e5125b770c711b4f34ff0ba866dc4f18f93944353bc0d5e25dbb6ca4e45498a39ff456cbfe0ca13490850aded413ebe4e7758df641cea1ca5b5256e76d1a1960110953dd01053cde474eb6f41eb04a46e44731bfd0cfa1f51576ebb107cb9052a05e2fc5d903d793172f108f3ee9df0fc48fd9bab62cdaddcae5f826195d2ea8872557122ed25c9bcbcc23f7345738c9ac916b56fe8&p=976fc64ad49c11a05bed9437455786&newp=9e759a46d6c333fc57efd0214f0886231610db2151d4d7156b82c825d7331b001c3bbfb423251303d8c0796607a54a5aeaf233713d012ba3dda5c91d9fb4c57479fa&user=baidu&fm=sc&query=Container+is+running+beyond+physical+memory+limits%2E+Current+usage%3A+1%2E0+GB+of+1+GB+physical+memory+used&qid=e932452900015c27&p1=1

```
conf.set("mapreduce.map.memory.mb", "2048");
```
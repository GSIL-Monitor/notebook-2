# **zookeeper**

## 1. 配置文件

### 1.1 线上配置

```
tickTime=2000
initLimit=10
syncLimit=5
clientPort=2181
server.0=172.18.54.162:2888:3888
server.1=172.18.54.163:2888:3888
server.2=172.18.54.164:2888:3888
dataLogDir=/bbd/logs/zookeeper/logs
dataDir=/bbd/logs/zookeeper/data
autopurge.purgeInterval=6
autopurge.snapRetainCount=72

maxClientCnxns=1000
```

### 1.2 虚拟机配置
```
tickTime=2000
initLimit=10
syncLimit=5
clientPort=2181
server.0=master:2888:3888
server.1=work1:2888:3888
server.2=work2:2888:3888
dataLogDir=/zz/data/zookeeper/logs
dataDir=/zz/data/zookeeper/data
autopurge.purgeInterval=6
autopurge.snapRetainCount=72
maxClientCnxns=1000
```
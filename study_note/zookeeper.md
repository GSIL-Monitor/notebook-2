# 1. 安装
## 1.1 下载
```
wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz
```

## 1.2 初始化目录
```
tar -zxvf zookeeper-3.4.9.tar.gz
ln -s zookeeper-3.4.9/ zookeeper

mkdir -p /zz/data/zookeeper/logs /zz/data/zookeeper/data
#各个机器的myid不一样
echo "0" > /zz/data/zookeeper/data/myid
```

## 1.3 修改配置
- vim conf/zoo.cnf
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

## 1.4 启动
```/zz/app/zookeeper/bin/zkServer.sh start```

## 1.5 查看状态
```
bin/zkServer.sh status
```

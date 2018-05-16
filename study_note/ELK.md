# 1. elasticsearch

## 1.1 安装
- 官方文档
  [https://www.elastic.co/guide/en/x-pack/5.2/index.html](https://www.elastic.co/guide/en/x-pack/5.2/index.html)

- 官网地址
  [https://www.elastic.co/downloads](https://www.elastic.co/downloads)  
  [https://www.elastic.co/downloads/past-releases](https://www.elastic.co/downloads/past-releases)  

- 下载、解压、创建存储文件夹、创建用户、更改权限
  ```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.zip
  unzip elasticsearch-5.2.0.zip
  ln -s /zz/app/elasticsearch-5.2.0 /zz/app/elasticsearch

  adduser elasticsearch

  chown -R elasticsearch:elasticsearch /zz/app/elasticsearch
  chown -R elasticsearch:elasticsearch /zz/app/elasticsearch-5.2.0

  mkdir -p /zz/data/es/es_data /zz/data/es/es_backup
  chown -R elasticsearch:elasticsearch /zz/data/es
  ```

- 修改系统配
  - vim /etc/security/limits.conf
    ```
    #线程
    *	soft	nproc	1024000
    *	hard	nproc	1024000
    #文件
    *	hard	nofile	65536
    *	soft	nofile	65536
    #内存
    elasticsearch	soft	memlock	unlimited
    elasticsearch	hard	memlock	unlimited
    ```

  - vim /etc/security/limits.d/90-nproc.conf
    `* soft nproc 2048`

  - vim /etc/sysctl.conf
    `vm.max_map_count=655360`

  - 执行命令使生效
    `sysctl -p`

- 配置文件
  [master节点配置文件](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/elasticsearch/elasticsearch(master).yml)  
  [jvm配置](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/elasticsearch/jvm.options)  
  [work节点配置文件](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/elasticsearch/elasticsearch(work1).yml)  

- 启动
  `/zz/app/es/bin/elasticsearch -d`


## 1.2 命令
### 1.2.1 查看集群健康值
`curl -XGET 'http://master:9200/_cluster/health?pretty'`
> 每当我们要求群集健康时，我们都会得到绿色，黄色或红色。绿色表示一切都很好（集群完全正常），黄色表示所有数据都可用，但某些副本尚未分配（集群完全正常运行），而红色表示某些数据由于任何原因而无法使用。请注意，即使群集是红色的，它仍然部分功能（即它将继续从可用碎片中提供搜索请求），但由于缺少数据，因此您可能需要尽快修复

### 1.2.2 查看集群节点列表
  ```curl -XGET 'http://master:9200/_cat/nodes?v'
#现在我们来看看我们的指数
curl -XGET 'http://master:9200/_cat/indices?v'
  ```

### 1.2.3 创建索引
```
curl -XPUT 'http://master:9200/customer?pretty'
curl -XGET 'http://master:9200/_cat/indices?v'
```
> 返回结果：  
> health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size  
> green  open   twotest  sCHl66lTTeCIZtn1-yeLWA   5   1          1            0      8.6kb          4.3kb  
> green  open   customer 3HidYr5aRlG1ghWT-jVl-A   5   1          0            0       650b           390b  
>
> 第一个命令使用PUT动词创建名为“customer”的索引。我们简单地追加pretty到调用的结尾，告诉它打印JSON响应（如果有的话）  
> 第二个命令的结果告诉我们，我们现在有一个名为customer的索引，它有5个主分片和1个副本（默认值），它包含0个文档。  
>
> 您可能还会注意到，客户索引的黄色健康状况已被标记。从我们以前的讨论中回想一下，黄色意味着某些副本尚未被分配。这个索引发生的原因是因为Elasticsearch默认为此索引创建了一个副本。由于我们目前只有一个节点正在运行，所以一个副本不能被分配（为了高可用性），直到另一个节点加入集群的后来的时间点。一旦该副本被分配到第二个节点上，该索引的运行状况将变为绿色

### 1.2.4 插入、查询、删除
```
<REST动词> / <索引> / <类型> / <ID>
curl -XPOST http://master:9200/mytest/typetest1/ -d '{"name":"张三", "age":17}'
curl -XPOST http://master:9200/mytest/typetest2/ -d '{"name":"李四", "age":18}'
curl -XGET http://master:9200/mytest/typetest1/_search?pretty

curl http://master:9200/mytest/_search?pretty -d '{"query":{"bool":{"must":[{"match_all":{}}],"must_not":[],"should":[]}},"from":0,"size":10,"sort":[],"aggs":{}}'
```

### 1.2.5 crul命令批量插入数据
- vim test.json
  ```
  {"index":{"_index":"wushan_rh_log_destri","_type":"rh_log_destri","_id":"3"}}
  {"id":"3"}
  {"index":{"_index":"wushan_rh_log_destri","_type":"rh_log_destri","_id":"4"}}
  {"id":"4"}
  ```
- 插入json文件中的数据
  `curl -XPOST http://master:9200/_bulk --data-binary @test.json`

### 1.2.6 聚合函数

#### 1.2.6.1 参考地址
  [https://www.cnblogs.com/xing901022/p/4944043.html](https://www.cnblogs.com/xing901022/p/4944043.html)

#### 1.2.6.2 Sum
```
"aggs": {
	"intraday_return": {
		"sum": {
			"field": "change"
		}
	}
}
```

#### 1.2.6.3 Min 求最小值
```
{
	"aggs": {
		"min_price": {
			"min": {
				"field": "price"
			}
		}
	}
}
```

#### 1.2.6.4 Max 求最大值
```
{
	"aggs": {
		"max_price": {
			"max": {
				"field": "price"
			}
		}
	}
}
```

#### 1.2.6.5 avg 求平均值
```
{
	"aggs": {
		"avg_grade": {
			"avg": {
				"field": "grade"
			}
		}
	}
}
```

#### 1.2.6.6 求唯一值
即不重复的字段有多少
```
{
	"aggs": {
		"author_count": {
			"cardinality": {
				"field": "author"
			}
		}
	}
}
```

#### 1.2.6.7 求百分比
- 多值聚合,percentiles 求百分比
  ```
  {
  	"aggs": {
  		"load_time_outlier": {
  			"percentile_ranks": {
  				"field": "load_time",
  				"values": [15,
  				30]
  			}
  		}
  	}
  }
  ```
- 返回的结果包含多个值：
  ```
  {
  	...
  	"aggregations": {
  		"load_time_outlier": {
  			"values": {
  				"15": 92,
  				"30": 100
  			}
  		}
  	}
  }
  ```


#### 1.2.6.8 stats统计
```
{
	"aggs": {
		"grades_stats": {
			"stats": {
				"field": "grade"
			}
		}
	}
}
```
请求后会直接显示多种聚合结果：
```
{
	...
	"aggregations": {
		"grades_stats": {
			"count": 6,
			"min": 60,
			"max": 98,
			"avg": 78.5,
			"sum": 471
		}
	}
}
```

#### 1.2.6.9 extend stats扩展统计
```
{
    "aggs" : {
        "grades_stats" : { "extended_stats" : { "field" : "grade" } }
    }
}
```
在统计的基础上还增加了多种复杂的统计信息：
```
{
    ...
    "aggregations": {
        "grade_stats": {
           "count": 9,
           "min": 72,
           "max": 99,
           "avg": 86,
           "sum": 774,
           "sum_of_squares": 67028,
           "variance": 51.55555555555556,
           "std_deviation": 7.180219742846005,
           "std_deviation_bounds": {
            "upper": 100.36043948569201,
            "lower": 71.63956051430799
           }
        }
    }
}
```

#### 1.2.6.10 总结
上面并没有列举全面，比如2.0版本的ES，还支持多值的percentile Rank百分比排名，Geo Bounds地理位置信息，Scripted Metric脚本；单值的top hits等等。  
•	在性能上，ES也做了很多的优化：比如max和min，如果对于排序的字段，那么就直接跳过了计算的步骤，直接取出目标值即可。  
•	当然有些聚合也是需要特定的场合的，比如cardinality计算唯一值是通过哈希的方式，如果字段数据规模很大，那么会消耗很多的性能。  
•	另外桶之间是可以嵌套的，比如在range聚合下嵌套了一个max聚合，那么会在range得到的每个结果组上，再次进行max的统计。  
•	在聚合中支持脚本的使用，可以增加统计的灵活度。很多内容还需要在实践中使用，才能了解它的优势。



### 1.2.7 工作中的查询语句
#### 1.2.7.1 基本查询
```
{"query":{"bool":{"must":[{"match_all":{}}],"must_not":[],"should":[]}},"from":0,"size":10,"sort":[],"aggs":{}}
```

#### 1.2.7.2 按文件名分组查询
```
{
	"size": 1,
	"query": {
		"bool": {
			"must": [{
				"query_string": {
					"query": "file_name:*TX6.access*",
					"analyze_wildcard": true
				}
			},
			{
				"term": {
					"firm_name": "TX"
				}
			}],
			"must_not": []
		}
	},
	"_source": {
		"excludes": []
	},
	"aggs": {
		"2": {
			"terms": {
				"field": "file_name",
				"size": 500,
				"order": {
					"_count": "desc"
				}
			}
		}
	}
}
```

#### 1.2.7.1 



## 1.3 es备份、还原
### 1.3.1 用插件备份到hdfs上（repository-hdfs）
```
curl -XPUT 183.136.128.47:9200/_snapshot/my_hdfs_repository5/kshttplog-test -d '{"indices":"kshttplog-test"}'

curl -XPOST 183.136.128.47:9200/_snapshot/my_hdfs_repository5/kshttplog-test/_restore
```

安装repository-hdfs插件
bin/elasticsearch-plugin install repository-hdfs

```
hdfs dfs -mkdir /elasticsearch
hdfs dfs -chown -R elasticsearch:elasticsearch /elasticsearch
```


```
# hdfs
PUT /_snapshot/my_hdfsbackup
{
  "type": "hdfs",
  "settings": {
         "uri": "hdfs://master:9000",
         "path": "/elasticsearch/esbackup/",
         "load_defaults": "true",
         "compress": "true",
         "conf_location":"hdfs-site.xml"
  }
}

GET _snapshot/my_hdfsbackup
#下面当前所有的仓库信息s
GET /_snapshot
GET /_snapshot/_all

DELETE /_snapshot/my_hdfsbackup


PUT /_snapshot/my_hdfsbackup/snapshot_1?wait_for_completion=true
{
  "indices": "mytest,mytest1",
  "ignore_unavailable": true,
  "include_global_state": false
}

GET /_snapshot/my_hdfsbackup/_all

POST /_snapshot/my_hdfsbackup/snapshot_1/_restore
{
  "indices": "mytest,mytest1", 
  "ignore_unavailable": true,
  "include_global_state": false,
  "rename_pattern": "index_(.+)",
  "rename_replacement": "restored_index_$1"
}
```

vim plugin-security.policy

```
grant {
  // Hadoop UserGroupInformation, HdfsConstants, PipelineAck clinit
  permission java.lang.RuntimePermission "getClassLoader";

  // UserGroupInformation (UGI) Metrics clinit
  permission java.lang.RuntimePermission "accessDeclaredMembers";
  permission java.lang.reflect.ReflectPermission "suppressAccessChecks";

  // org.apache.hadoop.util.StringUtils clinit
  permission java.util.PropertyPermission "*", "read,write";

  // org.apache.hadoop.util.ShutdownHookManager clinit
  permission java.lang.RuntimePermission "shutdownHooks";

  // JAAS is used always, we use a fake subject, hurts nobody
  permission javax.security.auth.AuthPermission "getSubject";
  permission javax.security.auth.AuthPermission "doAs";
  permission javax.security.auth.AuthPermission "modifyPrivateCredentials";
  permission java.lang.RuntimePermission "accessDeclaredMembers";
  permission java.lang.RuntimePermission "getClassLoader";
  permission java.lang.RuntimePermission "shutdownHooks";
  permission java.lang.reflect.ReflectPermission "suppressAccessChecks";
  permission javax.security.auth.AuthPermission "doAs";
  permission javax.security.auth.AuthPermission "getSubject";
  permission javax.security.auth.AuthPermission "modifyPrivateCredentials";
  permission java.security.AllPermission;
  permission java.util.PropertyPermission "*", "read,write";
  permission javax.security.auth.PrivateCredentialPermission "org.apache.hadoop.security.Credentials * \"*\"", "read";
};

作者：king_wang
链接：https://www.jianshu.com/p/f14f0afed345
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



安装插件repository-hdfs
```
bin/elasticsearch-plugin install repository-hdfs
```
安装如下
```
-> Downloading repository-hdfs from elastic
[=================================================] 100%   
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@     WARNING: plugin requires additional permissions     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
* java.lang.RuntimePermission accessDeclaredMembers
* java.lang.RuntimePermission getClassLoader
* java.lang.RuntimePermission shutdownHooks
* java.lang.reflect.ReflectPermission suppressAccessChecks
* java.util.PropertyPermission * read,write
* javax.security.auth.AuthPermission doAs
* javax.security.auth.AuthPermission getSubject
* javax.security.auth.AuthPermission modifyPrivateCredentials
See http://docs.oracle.com/javase/8/docs/technotes/guides/security/permissions.html
for descriptions of what these permissions allow and the associated risks.

Continue with installation? [y/N]y
-> Installed repository-hdfs
```


创建仓库后备份索引异常
```
{
  "error": {
    "root_cause": [
      {
        "type": "repository_exception",
        "reason": "[my_hdfsbackup] could not read repository data from index blob"
      }
    ],
    "type": "repository_exception",
    "reason": "[my_hdfsbackup] could not read repository data from index blob",
    "caused_by": {
      "type": "i_o_exception",
      "reason": "com.google.protobuf.ServiceException: java.security.AccessControlException: access denied (\"javax.security.auth.PrivateCredentialPermission\" \"org.apache.hadoop.security.Credentials\" \"read\")",
      "caused_by": {
        "type": "service_exception",
        "reason": "java.security.AccessControlException: access denied (\"javax.security.auth.PrivateCredentialPermission\" \"org.apache.hadoop.security.Credentials\" \"read\")",
        "caused_by": {
          "type": "access_control_exception",
          "reason": "access denied (\"javax.security.auth.PrivateCredentialPermission\" \"org.apache.hadoop.security.Credentials\" \"read\")"
        }
      }
    }
  },
  "status": 500
}
```
处理
参考地址：https://www.jianshu.com/p/f14f0afed345


## 1.4 问题
### 1.4.1 分片无法分配
参考地址：[https://birdben.github.io/2016/12/22/Elasticsearch/Elasticsearch%E5%AD%A6%E4%B9%A0%EF%BC%88%E4%B8%80%EF%BC%89%E9%9B%86%E7%BE%A4red%E7%8A%B6%E6%80%81%E7%9A%84%E5%A4%84%E7%90%86/](https://birdben.github.io/2016/12/22/Elasticsearch/Elasticsearch%E5%AD%A6%E4%B9%A0%EF%BC%88%E4%B8%80%EF%BC%89%E9%9B%86%E7%BE%A4red%E7%8A%B6%E6%80%81%E7%9A%84%E5%A4%84%E7%90%86/)

### 1.4.2 启动异常
```
[2018-02-23T14:06:37,153][WARN ][r.suppressed             ] path: /_stats, params: {}
org.elasticsearch.cluster.block.ClusterBlockException: blocked by: [SERVICE_UNAVAILABLE/1/state not recovered / initialized];
        at org.elasticsearch.cluster.block.ClusterBlocks.globalBlockedException(ClusterBlocks.java:165) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.admin.indices.stats.TransportIndicesStatsAction.checkGlobalBlock(TransportIndicesStatsAction.java:70) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.admin.indices.stats.TransportIndicesStatsAction.checkGlobalBlock(TransportIndicesStatsAction.java:47) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction$AsyncAction.<init>(TransportBroadcastByNodeAction.java:256) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction.doExecute(TransportBroadcastByNodeAction.java:234) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction.doExecute(TransportBroadcastByNodeAction.java:79) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction$RequestFilterChain.proceed(TransportAction.java:173) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:145) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:87) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.node.NodeClient.executeLocally(NodeClient.java:75) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.node.NodeClient.doExecute(NodeClient.java:64) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.support.AbstractClient.execute(AbstractClient.java:403) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.support.AbstractClient$IndicesAdmin.execute(AbstractClient.java:1226) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.support.AbstractClient$IndicesAdmin.stats(AbstractClient.java:1547) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.rest.action.admin.indices.RestIndicesStatsAction.lambda$prepareRequest$17(RestIndicesStatsAction.java:148) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.rest.BaseRestHandler.handleRequest(BaseRestHandler.java:82) [elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.rest.RestController.dispatchRequest(RestController.java:162) [elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.http.HttpServer.dispatchRequest(HttpServer.java:115) [elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.http.netty4.Netty4HttpServerTransport.dispatchRequest(Netty4HttpServerTransport.java:515) [transport-netty4-5.2.0.jar:5.2.0]
        at org.elasticsearch.http.netty4.Netty4HttpRequestHandler.channelRead0(Netty4HttpRequestHandler.java:70) [transport-netty4-5.2.0.jar:5.2.0]
        at io.netty.channel.SimpleChannelInboundHandler.channelRead(SimpleChannelInboundHandler.java:105) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:363) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:349) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:341) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at org.elasticsearch.http.netty4.pipelining.HttpPipeliningHandler.channelRead(HttpPipeliningHandler.java:66) [transport-netty4-5.2.0.jar:5.2.0]
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:363) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:349) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:341) [netty-transport-4.1.7.Final.jar:4.1.7.Final]
        at org.elasticsearch.http.netty4.cors.Netty4CorsHandler.channelRead(Netty4CorsHandler.java:76) [transport-netty4-5.2.0.jar:5.2.0]
        at io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:363) [netty-transport-4.1.
```
- 出现上面的原因是：有分片没有完全分配，分配分配完成就好了

## 1.5 备份

### 1.5.1 参考地址
[http://blog.csdn.net/u014431852/article/details/52905821](http://blog.csdn.net/u014431852/article/details/52905821)
[中文官网文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current/backing-up-your-cluster.html)
[使用sshfs挂载远程服务器目录](https://www.jianshu.com/p/cdf5652a88d3)
[umount命令](http://man.linuxde.net/umount)

要备份你的集群，你可以使用 snapshot API。这个会拿到你集群里当前的状态和数据然后保存到一个共享仓库里。这个备份过程是"智能"的,即增量备份，所有后续的快照会保留的是已存快照和新数据之间的差异。随着你不时的对数据进行快照，备份也在增量的添加和删除。这意味着后续备份会相当快速
有多个仓库类型可以供你选择
- 共享文件系统，比如 NAS
- Amazon S3
- HDFS (Hadoop 分布式文件系统)
- Azure Cloud

### 1.5.2 安装sshfs
- 安装epel扩展源
  ```rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm```

- 在 Linux 系统上安装 SSHFS
  ```yum -y install sshfs```

### 1.5.3 创建共享目录
```
#选定一个节点的一个目录作为共享目录
mkdir -p /mnt/es_backup
chmod -R 777 /mnt/es_backup

#在每个节点的相同位置创建目录，并挂载共享目录
mkdir /zz/data/es/es_backup
chmod -R 777 /zz/data/es/es_backup
sshfs root@work2:/mnt/es_backup /zz/data/es/es_backup/ -o allow_other
```

- -o allow_other 解决了不同节点往共享仓库中写数据的权限问题

- 查看挂载情况
```
mount -l
#或者是
df -h
```

- 测试运行ES的用户是否有对共享目录的写权限
  ```sudo -u elasticsearch echo "a" >> /zz/data/es/es_backup/a```

### 1.5.4 修改es配置文件
在elasticsearch.yml中增加path.repo: /zz/data/es/es_backup，配置共享仓库的位置，重启节点

### 1.5.5 为集群创建共享仓库
```
#让我部署一个共享 文件系统仓库my_backup
PUT _snapshot/my_backup 
{
    "type": "fs", 
    "settings": {
        "location": "/zz/data/es/es_backup",
        "compress": true
    }
}
```
- my_backup : 给我们的仓库取一个名字，在本例它叫 my_backup 。
- fs: 我们指定仓库的类型应该是一个共享文件系统。
- /zz/data/es/es_backup：一个已挂载的设备作为目的地址。
  注意：共享文件系统路径必须确保集群所有节点都可以访问到。
  这步会在挂载点创建仓库和所需的元数据。还有一些其他的配置你可能想要配置的，这些取决于你的节点、网络的性能状况和仓库位置：
- max_snapshot_bytes_per_sec
  当快照数据进入仓库时，这个参数控制这个过程的限流情况。默认是每秒 20mb 。
- max_restore_bytes_per_sec
  当从仓库恢复数据时，这个参数控制什么时候恢复过程会被限流以保障你的网络不会被占满。默认是每秒 `20mb`。
- 假设我们有一个非常快的网络，而且对额外的流量也很 OK，那我们可以增加这些默认值
```
POST _snapshot/my_backup/ 
{
    "type": "fs",
    "settings": {
        "location": "/mount/backups/my_backup",
        "max_snapshot_bytes_per_sec" : "50mb", 
        "max_restore_bytes_per_sec" : "50mb"
    }
}
```
- 注意我们用的是 POST 而不是 PUT 。这会更新已有仓库的设置

### 1.5.6 备份数据
一个仓库可以包含多个快照。 每个快照跟一系列索引相关（比如所有索引，一部分索引，或者单个索引）。当创建快照的时候，你指定你感兴趣的索引然后给快照取一个唯一的名字。
```
PUT _snapshot/my_backup/snapshot_name
```
- 这个会备份所有打开的索引到 my_backup 仓库下一个命名为 snapshot_name 的快照里。这个调用会立刻返回，然后快照会在后台运行。
- 通常你会希望你的快照作为后台进程运行，不过有时候你会希望在你的脚本中一直等待到完成。这可以通过添加一个 wait_for_completion 标记实现
  `PUT _snapshot/my_backup/snapshot_1?wait_for_completion=true`
  - 这个会阻塞调用直到快照完成。注意大型快照会花很长时间才返回

### 1.5.7 备份指定索引的数据
默认行为是备份所有打开的索引。备份你的集群的时候指定备份哪些索引
```
PUT _snapshot/my_backup/snapshot_2
{
    "indices": "index_1,index_2"
}
```
- 这个快照命令现在只会备份 index_1 和 index_2 了。

### 1.5.8 列出快照相关的信息
- 一旦你开始在你的仓库里积攒起快照了，你可能就慢慢忘记里面各自的细节了 ——特别是快照按照时间划分命名的时候（比如， backup_2014_10_28 ）。
  要获得单个快照的信息，直接对仓库和快照名发起一个 GET 请求：
  `GET _snapshot/my_backup/snapshot_2`
  这个会返回一个小响应，包括快照相关的各种信息：
  ```
  {
     "snapshots": [
        {
           "snapshot": "snapshot_1",
           "indices": [
              ".marvel_2014_28_10",
              "index1",
              "index2"
           ],
           "state": "SUCCESS",
           "start_time": "2014-09-02T13:01:43.115Z",
           "start_time_in_millis": 1409662903115,
           "end_time": "2014-09-02T13:01:43.439Z",
           "end_time_in_millis": 1409662903439,
           "duration_in_millis": 324,
           "failures": [],
           "shards": {
              "total": 10,
              "failed": 0,
              "successful": 10
           }
        }
     ]
  }
  ```
- 要获取一个仓库中所有快照的完整列表，使用 _all 占位符替换掉具体的快照名称：
    `GET _snapshot/my_backup/_all`

### 1.5.9 监控快照进度
`GET _snapshot/my_backup/snapshot_3/_status`
- INITIALIZING
  分片在检查集群状态看看自己是否可以被快照。这个一般是非常快的。
- STARTED
  数据正在被传输到仓库。
- FINALIZING
  数据传输完成；分片现在在发送快照元数据。
- DONE
  快照完成！
- FAILED
  快照处理的时候碰到了错误，这个分片/索引/快照不可能完成了。检查你的日志获取更多信息。

### 1.5.10 删除快照
最后，我们需要一个命令来删除所有不再有用的旧快照 。这只要对仓库/快照名称发一个简单的 DELETE
`DELETE _snapshot/my_backup/snapshot_2`

### 1.5.11 取消一个快照编辑
最后，你可能想取消一个快照或恢复。 因为它们是长期运行的进程，执行操作的时候一个笔误或者过错就会花很长时间来解决——而且同时还会耗尽有价值的资源。
要取消一个快照，在他进行中的时候简单的删除快照就可以：
`DELETE _snapshot/my_backup/snapshot_3`
这个会中断快照进程。然后删除仓库里进行到一半的快照。

### 1.5.12 从快照恢复
一旦你备份过了数据，恢复它就简单了：只要在你希望恢复回集群的快照 ID 后面加上 _restore 即可：
`POST _snapshot/my_backup/snapshot_1/_restore`

默认行为是把这个快照里存有的所有索引都恢复。如果 snapshot_1 包括五个索引，这五个都会被恢复到我们集群里。 和 snapshot API 一样，我们也可以选择希望恢复具体哪个索引。

还有附加的选项用来重命名索引。这个选项允许你通过模式匹配索引名称，然后通过恢复进程提供一个新名称。如果你想在不替换现有数据的前提下，恢复老数据来验证内容，或者做其他处理，这个选项很有用。让我们从快照里恢复单个索引并提供一个替换的名称：
```
POST /_snapshot/my_backup/snapshot_1/_restore
{
    "indices": "index_1", 
    "rename_pattern": "index_(.+)", 
    "rename_replacement": "restored_index_$1" 
}
```
- indices: 只恢复 index_1 索引，忽略快照中存在的其余索引。
- rename_pattern: 查找所提供的模式能匹配上的正在恢复的索引。
- rename_replacement: 然后把它们重命名成替代的模式。
- 这个会恢复 index_1 到你及群里，但是重命名成了 restored_index_1 
- 和快照类似， restore 命令也会立刻返回，恢复进程会在后台进行。如果你更希望你的 HTTP 调用阻塞直到恢复完成，添加 wait_for_completion 标记
  `POST _snapshot/my_backup/snapshot_1/_restore?wait_for_completion=true`

### 1.5.13 监控恢复操作
从仓库恢复数据借鉴了 Elasticsearch 里已有的现行恢复机制。 在内部实现上，从仓库恢复分片和从另一个节点恢复是等价的。

如果你想监控恢复的进度，你可以使用 recovery API。这是一个通用目的的 API，用来展示你集群中移动着的分片状态。

这个 API 可以为你在恢复的指定索引单独调用
`GET restored_index_3/_recovery`
或者查看你集群里所有索引，可能包括跟你的恢复进程无关的其他分片移动
`GET /_recovery/`
- 输出会跟这个类似（注意，根据你集群的活跃度，输出可能会变得非常啰嗦！）：
```
{
  "restored_index_3" : {
    "shards" : [ {
      "id" : 0,
      "type" : "snapshot", 
      "stage" : "index",
      "primary" : true,
      "start_time" : "2014-02-24T12:15:59.716",
      "stop_time" : 0,
      "total_time_in_millis" : 175576,
      "source" : { 
        "repository" : "my_backup",
        "snapshot" : "snapshot_3",
        "index" : "restored_index_3"
      },
      "target" : {
        "id" : "ryqJ5lO5S4-lSFbGntkEkg",
        "hostname" : "my.fqdn",
        "ip" : "10.0.1.7",
        "name" : "my_es_node"
      },
      "index" : {
        "files" : {
          "total" : 73,
          "reused" : 0,
          "recovered" : 69,
          "percent" : "94.5%" 
        },
        "bytes" : {
          "total" : 79063092,
          "reused" : 0,
          "recovered" : 68891939,
          "percent" : "87.1%"
        },
        "total_time_in_millis" : 0
      },
      "translog" : {
        "recovered" : 0,
        "total_time_in_millis" : 0
      },
      "start" : {
        "check_index_time" : 0,
        "total_time_in_millis" : 0
      }
    } ]
  }
}
```
- type 字段告诉你恢复的本质；这个分片是在从一个快照恢复。
- source 哈希描述了作为恢复来源的特定快照和仓库。
- percent 字段让你对恢复的状态有个概念。这个特定分片目前已经恢复了 94% 的文件；它就快完成了。

- 输出会列出所有目前正在经历恢复的索引，然后列出这些索引里的所有分片。每个分片里会有启动/停止时间、持续时间、恢复百分比、传输字节数等统计值。


### 1.5.14 取消一个恢复
- 要取消一个恢复，你需要删除正在恢复的索引。 因为恢复进程其实就是分片恢复，发送一个 删除索引 API 修改集群状态，就可以停止恢复进程。比如
  `DELETE /restored_index_3`
- 如果 restored_index_3 正在恢复中，这个删除命令会停止恢复，同时删除所有已经恢复到集群里的数据


## 1.6 使用kopf插件备份
more -> snapshot

## 1.7 作为服务启动
### 1.7.1 启动文件
- vim elasticsearch-5 (修改elasticsearch和jdk的路径)

```
#!/bin/sh
#
# elasticsearch <summary>
#
# chkconfig:   2345 80 20
# description: Starts and stops a single elasticsearch instance on this system 
#
### BEGIN INIT INFO
# Provides: Elasticsearch
# Required-Start: $network $named
# Required-Stop: $network $named
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: This service manages the elasticsearch daemon
# Description: Elasticsearch is a very scalable, schema-free and high-performance search solution supporting multi-tenancy and near realtime search.
### END INIT INFO
#
# init.d / servicectl compatibility (openSUSE)
#
if [ -f /etc/rc.status ]; then
    . /etc/rc.status
    rc_reset
fi
#
# Source function library.
#
if [ -f /etc/rc.d/init.d/functions ]; then
    . /etc/rc.d/init.d/functions
fi
# Sets the default values for elasticsearch variables used in this script
ES_VERSION="5.2.0"
ES_USER="elasticsearch"
ES_GROUP="elasticsearch"
ES_HOME="/zz/app/elasticsearch-${ES_VERSION}"
MAX_OPEN_FILES=1024000
MAX_MAP_COUNT=262144
PID_DIR="${ES_HOME}"
# Source the default env file
ES_ENV_FILE="/etc/sysconfig/elasticsearch"
if [ -f "$ES_ENV_FILE" ]; then
    . "$ES_ENV_FILE"
fi
# CONF_FILE setting was removed
if [ ! -z "$CONF_FILE" ]; then
    echo "CONF_FILE setting is no longer supported. elasticsearch.yml must be placed in the config directory and cannot be renamed."
    exit 1
fi
exec="$ES_HOME/bin/elasticsearch"
prog="elasticsearch5"
pidfile="$PID_DIR/${prog}.pid"
lockfile=/var/lock/subsys/$prog
export JAVA_HOME=/zz/app/jdk
# backwards compatibility for old config sysconfig files, pre 0.90.1
if [ -n $USER ] && [ -z $ES_USER ] ; then 
   ES_USER=$USER
fi
checkJava() {
    if [ -x "$JAVA_HOME/bin/java" ]; then
        JAVA="$JAVA_HOME/bin/java"
    else
        JAVA=`which java`
    fi
    if [ ! -x "$JAVA" ]; then
        echo "Could not find any executable java binary. Please install java in your PATH or set JAVA_HOME"
        exit 1
    fi
}

start() {
    checkJava
    [ -x $exec ] || exit 5
    if [ -n "$MAX_LOCKED_MEMORY" -a -z "$ES_HEAP_SIZE" ]; then
        echo "MAX_LOCKED_MEMORY is set - ES_HEAP_SIZE must also be set"
        return 7
    fi
    if [ -n "$MAX_OPEN_FILES" ]; then
        ulimit -n $MAX_OPEN_FILES
    fi
    if [ -n "$MAX_LOCKED_MEMORY" ]; then
        ulimit -l $MAX_LOCKED_MEMORY
    fi
    if [ -n "$MAX_MAP_COUNT" -a -f /proc/sys/vm/max_map_count ]; then
        sysctl -q -w vm.max_map_count=$MAX_MAP_COUNT
    fi
    export ES_GC_LOG_FILE
    # Ensure that the PID_DIR exists (it is cleaned at OS startup time)
    if [ -n "$PID_DIR" ] && [ ! -e "$PID_DIR" ]; then
        mkdir -p "$PID_DIR" && chown "$ES_USER":"$ES_GROUP" "$PID_DIR"
    fi
    if [ -n "$pidfile" ] && [ ! -e "$pidfile" ]; then
        touch "$pidfile" && chown "$ES_USER":"$ES_GROUP" "$pidfile"
    fi
    cd $ES_HOME
    echo -n $"Starting $prog: "
     #if not running, start it up here, usually something like "daemon $exec"
    daemon --user $ES_USER --pidfile $pidfile $exec -p $pidfile -d
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}
stop() {
    echo -n $"Stopping $prog: "
    #curl -XPUT `hostname`:9200/_cluster/settings -d '{
    #    "transient" : {
    #    "cluster.routing.allocation.enable" : "none"
    #    }
    # }'
    # stop it here, often "killproc $prog"
    killproc -p $pidfile -d 86400 $prog
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}
restart() {
    stop
    start
}
reload() {
    restart
}
force_reload() {
    restart
}
rh_status() {
    # run checks to determine if the service is running or use generic status
    #if [ ! -f $pidfile ]; then
    #  echo "$prog not running"
    #else
    #  if ps auxw | grep $(cat $pidfile) | grep -v grep > /dev/null; then
    #    echo "running on pid $(cat $pidfile)"
    #  else
    #    echo 'not running (but PID file exists)'
    #  fi
    #fi
    status -p $pidfile $prog

}
rh_status_q() {
    rh_status >/dev/null 2>&1
}
case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
        restart
        ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload}"
        exit 2
esac
exit $?
```
### 1.7.2 添加到启动服务里
```
cp elasticsearch-5 /etc/init.d/
chkconfig --add /etc/init.d/elasticsearch-5
```
### 1.7.3 启动、停止
```
service elasticsearch-5 start
service elasticsearch-5 stop
```

## 1.8 nginx代理elasticsearch

## 1.9 概念
### 1.9.1 master、client、datanode
- master节点

主要功能是维护元数据，管理集群各个节点的状态，数据的导入和查询都不会走master节点，所以master节点的压力相对较小，因此master节点的内存分配也可以相对少些；但是master节点是最重要的，如果master节点挂了或者发生脑裂了，你的元数据就会发生混乱，那样你集群里的全部数据可能会发生丢失，所以一定要保证master节点的稳定性。

- data node

是负责数据的查询和导入的，它的压力会比较大，它需要分配多点的内存，选择服务器的时候最好选择配置较高的机器（大内存，双路CPU，SSD... 土豪~）；data node要是坏了，可能会丢失一小份数据。

- client node

是作为任务分发用的，它里面也会存元数据，但是它不会对元数据做任何修改。client node存在的好处是可以分担下data node的一部分压力；为什么client node能分担data node的一部分压力？因为es的查询是两层汇聚的结果，第一层是在data node上做查询结果汇聚，然后把结果发给client node，client node接收到data node发来的结果后再做第二次的汇聚，然后把最终的查询结果返回给用户；所以我们看到，client node帮忙把第二层的汇聚工作处理了，自然分担了data node的压力。
这里，我们可以举个例子，当你有个大数据查询的任务（比如上亿条查询任务量）丢给了es集群，要是没有client node，那么压力直接全丢给了data node，如果data node机器配置不足以接受这么大的查询，那么就很有可能挂掉，一旦挂掉，data node就要重新recover，重新reblance，这是一个异常恢复的过程，这个过程的结果就是导致es集群服务停止... 但是如果你有client node，任务会先丢给client node，client node要是处理不来，顶多就是client node停止了，不会影响到data node，es集群也不会走异常恢复。

对于es 集群为何要设计这三种角色的节点，也是从分层逻辑去考虑的，只有把相关功能和角色划分清楚了，每种node各尽其责，才能发挥出分布式集群的效果。

## 1.10 elasticsearch资源链接
- [elasticsearch官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/index.html)
- [elasticsearch权威指南中文版](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)
- ​

## 1.11 单台机器多实例
- 修改，一台机器的多个实例不一样
  ```
  node.name
  path.data
  ```
- 多master，
  discovery.zen.ping.unicast.hosts #master节点
  discovery.zen.minimum_master_nodes #发现至少master节点个数才能组成集群

## 1.12 es supervisor监控
/etc/supervisord.conf
```
[unix_http_server]
file=/bbd/logs/supervisor/supervisor.sock   ; (the path to the socket file)
;chmod=0700                 ; socket file mode (default 0700)
;chown=nobody:nogroup       ; socket file uid:gid owner
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))

[inet_http_server]         ; inet (TCP) server disabled by default
port=0.0.0.0:6001        ; (ip_address:port specifier, *:port for all iface)
;username=user              ; (default is no username (open server))
;password=123               ; (default is no password (open server))

[supervisord]
logfile=/bbd/logs/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10           ; (num of main logfile rotation backups;default 10)
loglevel=info                ; (log level;default info; others: debug,warn,trace)
pidfile=/bbd/logs/supervisor/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=false               ; (start in foreground if true;default false)
minfds=1024                  ; (min. avail startup file descriptors;default 1024)
minprocs=200                 ; (min. avail process descriptors;default 200)
;umask=022                   ; (process file creation umask;default 022)
;user=chrism                 ; (default is current user, required if root)
;identifier=supervisor       ; (supervisord identifier, default is 'supervisor')
;directory=/tmp              ; (default is not to cd during start)
;nocleanup=true              ; (don't clean up tempfiles at start;default false)
;childlogdir=/tmp            ; ('AUTO' child log dir, default $TEMP)
;environment=KEY="value"     ; (key value pairs to add to environment)
;strip_ansi=false            ; (strip ansi escape codes in logs; def. false)
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
[supervisorctl]
serverurl=unix:///bbd/logs/supervisor/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as http_username if set
;password=123                ; should be same as http_password if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available
; The below sample program section shows all possible program subsection values,
; create one or more 'real' program: sections to be able to control them under
; supervisor.


[include]
files = /etc/supervisor/conf.d/*.conf
```

/etc/supervisor/conf.d/monitor_es.conf
```
[program:monitores]
command=/opt/rh/wechat.py
stdout_logfile=/opt/rh/logs/monitores.log
autostart=true
autorestart=true
startsecs=5
priority=1
stopasgroup=true
killasgroup=true
```
wechat.py
```
#!/usr/bin/env python 
#coding: utf-8
import time
import urllib,urllib2,requests
import json
import sys

class WeChatMSG(object):
    def __init__(self,content):
        self.gettoken_url = 'https://qyapi.weixin.qq.com/cgi-bin/gettoken'
        self.gettoken_content = {
                            'corpid' : 'ww6f4afe29f8a62102',
                            'corpsecret' : 'J-ZTRxt3yOHUsQkk-dow3r6Uw28on1HxMZNOxt7iAag' ,
                            }
        self.main_content = {
                            "toparty":"3",
                            "agentid":"1000003",
                            "msgtype": "text",
                            "text":{
                            "content":content,
                                    }
                            }

    def get_access_token(self,string):
        token_result = json.loads(string.read())
        access_token=  token_result['access_token']
        return access_token.encode('utf-8')
    def geturl(self,url,data):
        data = self.encodeurl(data)
        response = urllib2.urlopen('%s?%s' % (url,data))
        return response.read().decode('utf-8')

    def posturl(self,url,data,isjson = True):
        if isjson:
            data = json.dumps(data)
        response = urllib2.urlopen(url,data)
        return response.read().decode('utf-8')
    def encodeurl(self,dict):
        data = ''
        for k,v in dict.items():
            data += '%s=%s%s' % (k,v,'&')
        return data
def send_msg(content):
    msgsender = WeChatMSG(content)
    access_token_response = msgsender.geturl(msgsender.gettoken_url, msgsender.gettoken_content)
    access_token =  json.loads(access_token_response)['access_token']
    sendmsg_url = 'https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token=%s' % access_token
    print msgsender.posturl(sendmsg_url,msgsender.main_content)

def get_es_status():
    es_url = "http://esclient.bbdcdn.net:9200/_cluster/health"
    try:
        response = requests.get(es_url)
        data = json.loads(response.content)['status']
    except :
        data = "connection close"
    return data
if __name__ == '__main__':
    try:
        error = 0
        ok = 0
        while True:
           status = get_es_status()
           if status == "red" :
               print time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) + "  " + status
               sys.stdout.flush()
               send_msg("elasticsearch cluster status is red")
               error = error + 1
               time.sleep(300)
           elif status == "green":
               ok = ok + 1
               print time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) + "  " + status
               print "ok is %d---error is %d" %(ok,error)
               sys.stdout.flush()
               if error >=1 and ok >= 1:
                   send_msg("elasticsearch cluster status is green")
                   error = 0
                   ok = 0
           time.sleep(60)
    except Exception:
        sys.exit(0)
```


# 2. elasticsearch-head
## 2.1 安装head插件
- 下载head插件
  ```
  wget https://github.com/mobz/elasticsearch-head/archive/master.zip
  unzip master.zip
  ln -s elasticsearch-head-master/ es_head
  ```
- 安装node
  ```
  #下载安装node
  wget https://nodejs.org/dist/v8.9.4/node-v8.9.4-linux-x64.tar.xz
  xz -d node-v8.9.4-linux-x64.tar.xz
  tar -xvf node-v8.9.4-linux-x64.tar
  ln -s node-v8.9.4-linux-x64/ node
  ```
- 配置node 的环境变量
  `vim /etc/profile`
  ```
  export NODE_HOME=/zz/app/node
  export PATH=$NODE_HOME/bin:$PATH
  ``
  `source /etc/profile`
  ```

- npm安装grunt
  ```
  npm install -g grunt-cli #执行完后会生成node_modules文件夹
  cd elasticsearch-head 
  npm install –g grunt–cli
  npm install -g grunt --registry=https://registry.npm.taobao.org
  npm install -g cnpm --registry=https://registry.npm.taobao.org
  npm install
  ```

- 安装依赖
  ```
  npm install grunt-contrib-clean --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-concat --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-watch --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-connect --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-copy --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-jasmine --registry=https://registry.npm.taobao.org --ignore-scripts
  ```

- 修改Elasticsearch配置文件, elasticsearch-5.1.1/config/elasticsearch.yml,加入以下内容：
  ```
  #是否支持跨域
  http.cors.enabled: true

  #*表示支持所有域名
  http.cors.allow-origin: "*"
  ```

- vim Gruntfile.js
  ```
  connect: {
  	server: {
  		options: {
  				hostname: 'master',
  				port: 9100,
  				base: '.',
  				keepalive: true
  		}
  	}
  }
  ```

- vim elasticsearch-head/_site/app.js  
  `http://master:9200`

- vim elasticsearch-head/package.json  
  `"license": "Apache-2.0",`

- 启动  
  `nohup elasticsearch-head/node_modules/grunt/bin/grunt server &`

- 访问
  > http://master:9100/

- 附：  
  wiki上的解释是 Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources ，即跨域访问。
  这个字段默认为false，在Elasticsearch安装集群之外的一台机上用Sense、Head等监控插件访问Elasticsearch是不允许的。这个字段最早可以追溯到1.4.x版本，而非5.x特有。  
  具体这个http.cors.x字段还有哪些用途和用法，见下表：  
  http.cors.enabled是否支持跨域，默认为false  
  http.cors.allow-origin当设置允许跨域，默认为*,表示支持所有域名，如果我们只是允许某些网站能访问，那么可以使用正则表达式。比如只允许本地地址。 /https?:\/\/localhost(:[0-9]+)?/  
  http.cors.max-age浏览器发送一个“预检”OPTIONS请求，以确定CORS设置。最大年龄定义多久的结果应该缓存。默认为1728000（20天）  
  http.cors.allow-methods允许跨域的请求方式，默认OPTIONS,HEAD,GET,POST,PUT,DELETE  
  http.cors.allow-headers跨域允许设置的头信息，默认为X-Requested-With,Content-Type,Content-Length  
  http.cors.allow-credentials是否返回设置的跨域Access-Control-Allow-Credentials头，如果设置为true,那么会返回给客户端。  

## 2.2 使用nginx代理
### 2.2.1 安装nginx，nginx配置中加入配置
```
location /plugin {
	root   html;
	index  index.html index.htm;
	access_log /zz/app/nginx/nginx/logs/plugin.access.log main;
	#lua_need_request_body on;
}
```

### 2.2.2 head
```
wget https://github.com/mobz/elasticsearch-head/archive/master.zip
unzip master.zip
ln -s /zz/app/elasticsearch-head-master/_site/ /zz/app/nginx/nginx/html/plugin/head
```

### 2.2.3 修改head配置
```
vim /zz/app/nginx/nginx/html/plugin/head/app.js
#下面这行使用自己的主机
this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://master:9200";
```

### 2.2.4 访问head插件
> master/plugin/head

## 2.3 安装时遇到的问题
- 在elasticsearch-head目录下node_modules/grunt下如果没有grunt二进制程序，需要执行：
  ```
  cd elasticsearch-head
  npm install grunt --save
  ```

- 出现以下提示，为Gruntfile.js引用的，缺少以下包
  > Local Npm module "grunt-contrib-clean" not found. Is it installed?  
  > Local Npm module "grunt-contrib-concat" not found. Is it installed?  
  > Local Npm module "grunt-contrib-watch" not found. Is it installed?  
  > Local Npm module "grunt-contrib-connect" not found. Is it installed?  
  > Local Npm module "grunt-contrib-copy" not found. Is it installed?  
  > Local Npm module "grunt-contrib-jasmine" not found. Is it installed?  
  > Warning: Task "connect:server" not found. Use --force to continue.  
  > Aborted due to warnings.  

  安装缺少的依赖
  ```
  npm install grunt-contrib-clean --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-concat --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-watch --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-connect --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-copy --registry=https://registry.npm.taobao.org  
  npm install grunt-contrib-jasmine --registry=https://registry.npm.taobao.org  
  ```

- 安装grunt-contrib-jasmine时报错
  > error This is probably not a problem with npm. There is likely additional logging output above.

  是因为在执行安装的过程中需要执行install.js，这里会下载Chromium,官网建议是进行跳过，我们可以执行 —ignore-scripts 忽略这个js执行。也可以通过设置环境变量set PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1阻止下载 Chromium （因为封网，直接下载会失败）  
  处理：
  `npm install grunt-contrib-jasmine --registry=https://registry.npm.taobao.org --ignore-scripts`


# 3. kopf
## 3.1 安装  
- github地址  
  [https://github.com/lmenezes/elasticsearch-kopf](https://github.com/lmenezes/elasticsearch-kopf)

- 下载解压
  ```
  wget https://github.com/lmenezes/elasticsearch-kopf/archive/master.zip
  unzip master.zip
  ```

- 使用nginx代理,类似于head  
  `ln -s /zz/app/elasticsearch-kopf-master/_site/ /zz/app/nginx/nginx/html/plugin/kopf`

## 3.2 进入初始化问题
### 3.2.1 弹框提示版本低
- 右上角不断弹版本过低
  ```
  cd /zz/app/nginx/nginx/html/plugin/kopf
  fgrep 'This version of kopf is not compatible with your ES version' ./*
  fgrep 'This version of kopf is not compatible with your ES version' ./*/*
  ```
- 上面会搜出 ./dist/kopf.js  这个文件，把相关弹出注释掉
  ```
  function(newValue, oldValue) {
  	var version = ElasticService.getVersion();
  	if (version && version.isValid()) {
  		var major = version.getMajor();
  		if (major != parseInt($scope.version.charAt(0))) {
  			//AlertService.warn(
  				//'This version of kopf is not compatible with your ES version',
  				//'Upgrading to newest supported version is recommended'
  			//);
  		}
  	}
  }
  ```
- 注意修改后清理下浏览器缓存

### 3.2.2 第一次进入提示连接错误
- 第一次进入提示 Error connecting to http://master:80
  ```
  cd /zz/app/nginx/nginx/html/plugin/kopf
  fgrep '9200' ./*
  fgrep '9200' ./*/*
  ```
- 上面搜后会出现 ./partials/nav_bar.html 和 ./dist/kopf.js
  - ./dist/kopf.js 中的host做相应的更改
    ```
    try {
    var host = 'http://master:9200'; // default
    if ($location.host() !== '') { // not opening from fs
    var location = $scope.readParameter('location');
    var url = $location.absUrl();
    if (isDefined(location)) {
      //host = location;
      host = 'http://master:9200';
    } else if (url.indexOf('/_plugin/kopf') > -1) {
      host = url.substring(0, url.indexOf('/_plugin/kopf'));
    } else {
      host = 'http://master:9200';
      //host = $location.protocol() + '://' + $location.host() +
      //    ':' + $location.port();
    }
    }
    ElasticService.connect(host);
    } catch (error) {
    AlertService.error(error.message, error.body);
    }
    ```
  - ./dist/kopf.js 和 ./partials/nav_bar.html的其他地方的'http://localhost:9200' 改为 'http://master:9200'


# 4. cerebro
## 4.1 安装
- kopf已停止维护，改用cerebro（kopf的替代品）

- github地址：

  - https://github.com/lmenezes/cerebro

- 下载、解压、启动

  ```
  wget https://github.com/lmenezes/cerebro/releases/download/v0.7.2/cerebro-0.7.2.zip
  unzip cerebro-0.7.2.zip
  cd cerebro-0.7.2.zip
  #启动脚本
  vim start.sh
  nohup ./bin/cerebro -Dhttp.port=1234 -Dhttp.address=master > /zz/app/cerebro-0.7.2/cerebro.log 2>&1 &
  ./start.sh
  ```

- 配置
  文件 ./conf/application.conf 中可以做相应的配置，例如：用户、访问es的连接

- 访问
  http://master:1234



# 5. kibana
## 5.1 安装
- 官方文档
  [https://www.elastic.co/guide/en/kibana/5.2/index.html](https://www.elastic.co/guide/en/kibana/5.2/index.html)
- 下载解压
  ```
  wget https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar -zxvf kibana-5.2.0-linux-x86_64.tar.gz
  ln -s kibana-5.2.0-linux-x86_64 kibana
  ```
- 修改配置  
  vim config/kibana.yml  
  [配置文件kibana.yml](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/kibana/kibana.yml)
- 启动
  `nohup ./bin/kibana &`
- 停止
  ```
  #查看端口
  ps -ef|grep kibana
  ps -ef|grep 5601
  fuser -n tcp 5601
  #杀掉进程
  kill -9  端口
  ```

## 5.2 使用nginx代理
- nginx加入下面配置
  ```
  location / {
  proxy_pass http://192.168.229.129:5601;
  proxy_http_version 1.1;
  proxy_set_header Connection "";
  proxy_connect_timeout 600s;
  proxy_read_timeout 600s;
  proxy_send_timeout 600s;
  access_log /zz/app/nginx/nginx/logs/kibana.access.log main;
  }
  location /plugins {
  proxy_pass http://192.168.229.129:5601/plugins;
  proxy_http_version 1.1;
  proxy_set_header Connection "";
  proxy_connect_timeout 600s;
  proxy_read_timeout 600s;
  proxy_send_timeout 600s;
  access_log /zz/app/nginx/nginx/logs/kibana.access.log main;
  }
  ```
- 访问
  http://master



# 6. x-pack
## 6.1 安装
- 下载和es相同版本的x-pack插件
  wget https://artifacts.elastic.co/downloads/packs/x-pack/x-pack-5.2.0.zip  
- 注意
  停es和kibana再安装，先安装es的再安装kibana的，启动es、启动kibana
- 在es中安装
  ```
  bin/elasticsearch-plugin install x-pack
  bin/elasticsearch-plugin install file:///path/to/file/x-pack-5.6.4.zip
  ```
- 在kibana中安装
  ```
  bin/kibana-plugin install x-pack
  bin/kibana-plugin install file:///path/to/file/x-pack-5.6.4.zip
  ```

## 6.3 如果不适用用户验证
在kibana和es配置文件中修改配置: `xpack.security.enabled: false`

## 6.4 卸载
```
bin/elasticsearch-plugin remove x-pack
bin/kibana-plugin remove x-pack
```

## 6.5 问题
### 6.5.1 安装x-packes启动报错
- master节点异常信息
  ```
  [2018-02-12T09:53:08,281][ERROR][o.e.x.m.c.i.IndicesStatsCollector] [master] collector [indices-stats-collector] failed to collect data
  org.elasticsearch.cluster.block.ClusterBlockException: blocked by: [SERVICE_UNAVAILABLE/1/state not recovered / initialized];
        at org.elasticsearch.cluster.block.ClusterBlocks.globalBlockedException(ClusterBlocks.java:165) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.admin.indices.stats.TransportIndicesStatsAction.checkGlobalBlock(TransportIndicesStatsAction.java:70) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.admin.indices.stats.TransportIndicesStatsAction.checkGlobalBlock(TransportIndicesStatsAction.java:47) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction$AsyncAction.<init>(TransportBroadcastByNodeAction.java:256) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction.doExecute(TransportBroadcastByNodeAction.java:234) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.broadcast.node.TransportBroadcastByNodeAction.doExecute(TransportBroadcastByNodeAction.java:79) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction$RequestFilterChain.proceed(TransportAction.java:173) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:145) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:87) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.node.NodeClient.executeLocally(NodeClient.java:75) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.node.NodeClient.doExecute(NodeClient.java:64) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.support.AbstractClient.execute(AbstractClient.java:403) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.FilterClient.doExecute(FilterClient.java:67) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.xpack.security.InternalClient.doExecute(InternalClient.java:83) ~[?:?]
        at org.elasticsearch.client.support.AbstractClient.execute(AbstractClient.java:403) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.client.support.AbstractClient$IndicesAdmin.execute(AbstractClient.java:1226) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.ActionRequestBuilder.execute(ActionRequestBuilder.java:80) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.ActionRequestBuilder.execute(ActionRequestBuilder.java:54) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.action.ActionRequestBuilder.get(ActionRequestBuilder.java:69) ~[elasticsearch-5.2.0.jar:5.2.0]
        at org.elasticsearch.xpack.monitoring.collector.indices.IndicesStatsCollector.doCollect(IndicesStatsCollector.java:66) ~[x-pack-5.2.0.jar:5.2.0]
        at org.elasticsearch.xpack.monitoring.collector.Collector.collect(Collector.java:83) [x-pack-5.2.0.jar:5.2.0]
        at org.elasticsearch.xpack.monitoring.AgentService$ExportingWorker.collect(AgentService.java:226) [x-pack-5.2.0.jar:5.2.0]
        at org.elasticsearch.xpack.monitoring.AgentService$ExportingWorker.run(AgentService.java:193) [x-pack-5.2.0.jar:5.2.0]
        at java.lang.Thread.run(Thread.java:745) [?:1.8.0_121]
  ```

- work节点异常信息
  ```
  [2018-02-12T10:02:25,198][INFO ][o.e.x.m.e.Exporters      ] [work1] skipping exporter [default_local] as it is not ready yet
  [2018-02-12T10:02:35,204][INFO ][o.e.x.m.e.Exporters      ] [work1] skipping exporter [default_local] as it is not ready yet
  [2018-02-12T10:02:45,212][INFO ][o.e.x.m.e.Exporters      ] [work1] skipping exporter [default_local] as it is not ready yet
  [2018-02-12T10:02:55,216][INFO ][o.e.x.m.e.Exporters      ] [work1] skipping exporter [default_local] as it is not ready yet
  ```
- 处理
  如果是正常安装的情况下，先忽略这些；启动master、work节点，启动kibana；es就由red变成了green

## 6.6 x-pack注册license

- x-pack安装后只有一个月的使用期限，注册后有一年的使用期限
  官网参考：[https://www.elastic.co/guide/en/x-pack/5.6/installing-license.html](https://www.elastic.co/guide/en/x-pack/5.6/installing-license.html)

- 申请一个免费license
  [https://license.elastic.co/registration](https://license.elastic.co/registration)

- 更新license
  ```
  curl -XPUT -u elastic 'http://<host>:<port>/_xpack/license' -H "Content-Type: application/json" -d @license.json
  #或者是
  curl -XPUT -u elastic 'http://<host>:<port>/_xpack/license?acknowledge=true' -H "Content-Type: application/json" -d @license.json
  ```

- 查看license
  ```
  curl -XGET -u elastic:changeme 'http://<host>:<port>/_xpack/license'
  ```

- 重启es集群生效


## 6.7 x-pack破解
### 6.7.1 参考地址
- 从5.0版本开始，Elastic将一些重要的插件整合成了X-Pack。免费的license只能使用一年，而且很多插件无法使用。如果想要试用，需要进行破解
  >[http://blog.csdn.net/mvpboss1004/article/details/65445023](http://blog.csdn.net/mvpboss1004/article/details/65445023)
  >[https://www.cnblogs.com/benwu/articles/6648471.html](https://www.cnblogs.com/benwu/articles/6648471.html)
  >
  >[http://fishboy.iteye.com/blog/2391750](http://fishboy.iteye.com/blog/2391750)

### 6.7.2 反编译luyten
- JD-GUI是无法反编译的，要使用luyten
- 下载luyten
  github地址:[https://github.com/deathmarine/Luyten/releases](https://github.com/deathmarine/Luyten/releases)
- 运行时异常(已经安装jdk配置环境变量了)：
  > this application requires a java runtime environment 1.7-1.8
  - 处理：
    方法一添加注册表:[https://www.cnblogs.com/myit/p/4575359.html](https://www.cnblogs.com/myit/p/4575359.html)
    方法二使用java -jar运行：[http://blog.csdn.net/k2514091675/article/details/76254729](http://blog.csdn.net/k2514091675/article/details/76254729)

### 6.7.3 反编译
- 找到es安装x-pack插件后的x-pack jar包，反编译类 LicenseVerifier
  包：elasticsearch-5.2.0/plugins/x-pack/x-pack-5.2.0.jar
  类：org.elasticsearch/license/LicenseVerifier.class

- 这个类是检查license完整性的类，我们使其始终返回true，就可以任意修改license并导入。将其改为：
  ```
  package org.elasticsearch.license;

  public class LicenseVerifier {
      public static boolean verifyLicense(final License license, final byte[] encryptedPublicKeyData) {
          return true;
      }
      
      public static boolean verifyLicense(final License license) {
          return true;
      }
  }
  ```

- 重新编译class文件。注意这里我们无需编译整个工程，将原来的x-pack-5.2.0.jar和依赖包加入CLASSPATH，即可完成单个文件的编译。实际上只用到了3个依赖包
  jar包下载maven仓库：[http://mvnrepository.com/artifact/org.elasticsearch/elasticsearch/5.2.0](http://mvnrepository.com/artifact/org.elasticsearch/elasticsearch/5.2.0)
  ```
  javac -cp "/usr/share/elasticsearch/lib/elasticsearch-5.2.0.jar:/usr/share/elasticsearch/lib/lucene-core-6.4.0.jar:/usr/share/elasticsearch/plugins/x-pack/x-pack-5.2.0.jar" LicenseVerifier.java
  ```

- 把x-pack-5.2.0.jar用压缩文件管理器打开，将里面的LicenseVerifier.class替换掉。再把破解了的jar包部署到各节点上，并重启es集群

- 修改申请的license文件，platinum表示白金版，可以使用所有功能。其他的如expiry_date_in_millis、max_nodes等根据自己需要修改即可。

- 把该license导入集群即可(- u指定用户，可以不用)：
  ```
  curl -XPUT -u elastic 'http://master:9200/_xpack/license?acknowledge=true' -H "Content-Type: application/json" -d @license.json
  ```

- 打开kibana中的monitring，可以看到x-pack的许可与期限

# 7. curator
https://www.elastic.co/guide/en/elasticsearch/client/curator/5.2/index.html







# 资源链接
https://www.cnblogs.com/bonelee/p/6617612.html
es的forcemerge——按照天分割：http://www.bubuko.com/infodetail-1995847.html

Elasticsearch 5.x 源码分析（5）segments merge 流程分析

在ElasticSearch中，集群(Cluster),节点(Node),分片(Shard),Indices(索引),replicas(备份)之间是什么关系？：https://www.zhihu.com/question/26446020?sort=created
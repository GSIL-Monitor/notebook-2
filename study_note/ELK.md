# **ELK**

## 1. elasticsearch

### 1.1 安装
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


### 1.2 命令
#### 1.2.1 查看集群健康值
`curl -XGET 'http://master:9200/_cluster/health?pretty'`
> 每当我们要求群集健康时，我们都会得到绿色，黄色或红色。绿色表示一切都很好（集群完全正常），黄色表示所有数据都可用，但某些副本尚未分配（集群完全正常运行），而红色表示某些数据由于任何原因而无法使用。请注意，即使群集是红色的，它仍然部分功能（即它将继续从可用碎片中提供搜索请求），但由于缺少数据，因此您可能需要尽快修复

#### 1.2.2 查看集群节点列表
  ```curl -XGET 'http://master:9200/_cat/nodes?v'
  #现在我们来看看我们的指数
  curl -XGET 'http://master:9200/_cat/indices?v'
  ```

#### 1.2.3 创建索引
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

#### 1.2.4 插入、查询、删除
```
<REST动词> / <索引> / <类型> / <ID>
curl -XPOST http://master:9200/mytest/typetest1/ -d '{"name":"张三", "age":17}'
curl -XPOST http://master:9200/mytest/typetest2/ -d '{"name":"李四", "age":18}'
curl -XGET http://master:9200/mytest/typetest1/_search?pretty

curl http://master:9200/mytest/_search?pretty -d '{"query":{"bool":{"must":[{"match_all":{}}],"must_not":[],"should":[]}},"from":0,"size":10,"sort":[],"aggs":{}}'
```

#### 1.2.5 crul命令批量插入数据
- vim test.json
  ```
  {"index":{"_index":"wushan_rh_log_destri","_type":"rh_log_destri","_id":"3"}}
  {"id":"3"}
  {"index":{"_index":"wushan_rh_log_destri","_type":"rh_log_destri","_id":"4"}}
  {"id":"4"}
  ```
- 插入json文件中的数据
  `curl -XPOST http://master:9200/_bulk --data-binary @test.json`

#### 1.2.6 聚合函数

##### 1.2.6.1 参考地址
  [https://www.cnblogs.com/xing901022/p/4944043.html](https://www.cnblogs.com/xing901022/p/4944043.html)

##### 1.2.6.2 Sum
```
"aggs": {
	"intraday_return": {
		"sum": {
			"field": "change"
		}
	}
}
```

##### 1.2.6.3 Min 求最小值
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

##### 1.2.6.4 Max 求最大值
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

##### 1.2.6.5 avg 求平均值
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

##### 1.2.6.6 求唯一值
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

##### 1.2.6.7 求百分比
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


##### 1.2.6.8 stats统计
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

##### 1.2.6.9 extend stats扩展统计
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

##### 1.2.6.10 总结
上面并没有列举全面，比如2.0版本的ES，还支持多值的percentile Rank百分比排名，Geo Bounds地理位置信息，Scripted Metric脚本；单值的top hits等等。
•	在性能上，ES也做了很多的优化：比如max和min，如果对于排序的字段，那么就直接跳过了计算的步骤，直接取出目标值即可。
•	当然有些聚合也是需要特定的场合的，比如cardinality计算唯一值是通过哈希的方式，如果字段数据规模很大，那么会消耗很多的性能。
•	另外桶之间是可以嵌套的，比如在range聚合下嵌套了一个max聚合，那么会在range得到的每个结果组上，再次进行max的统计。
•	在聚合中支持脚本的使用，可以增加统计的灵活度。
很多内容还需要在实践中使用，才能了解它的优势。



#### 1.2.7 工作中的查询语句
##### 1.2.7.1 按文件名分组查询
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

##### 1.2.7.1 



### 1.3 es备份、还原
#### 1.3.1 使用es的hdfs插件把数据备份到hdfs上
```
curl -XPUT 183.136.128.47:9200/_snapshot/my_hdfs_repository5/kshttplog-test -d '{"indices":"kshttplog-test"}'

curl -XPOST 183.136.128.47:9200/_snapshot/my_hdfs_repository5/kshttplog-test/_restore
```









## 2. elasticsearch-head
### 2.1 安装head插件
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

附：
wiki上的解释是 Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources ，即跨域访问。
这个字段默认为false，在Elasticsearch安装集群之外的一台机上用Sense、Head等监控插件访问Elasticsearch是不允许的。这个字段最早可以追溯到1.4.x版本，而非5.x特有。
具体这个http.cors.x字段还有哪些用途和用法，见下表：

http.cors.enabled	是否支持跨域，默认为false
http.cors.allow-origin	当设置允许跨域，默认为*,表示支持所有域名，如果我们只是允许某些网站能访问，那么可以使用正则表达式。比如只允许本地地址。 /https?:\/\/localhost(:[0-9]+)?/
http.cors.max-age	浏览器发送一个“预检”OPTIONS请求，以确定CORS设置。最大年龄定义多久的结果应该缓存。默认为1728000（20天）
http.cors.allow-methods	允许跨域的请求方式，默认OPTIONS,HEAD,GET,POST,PUT,DELETE
http.cors.allow-headers	跨域允许设置的头信息，默认为X-Requested-With,Content-Type,Content-Length
http.cors.allow-credentials	是否返回设置的跨域Access-Control-Allow-Credentials头，如果设置为true,那么会返回给客户端。


### 2.2 安装时遇到的问题
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





## 3. kibana

  ```

  ```
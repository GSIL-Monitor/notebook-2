# cdn monitor api

标签（空格分隔）： 未分类

---
## 全局错误码
|code|message|备注|
|:---|:---|:---|
|0|success||
|1001|unknow error||
|1002|params format error|请求参数错误|

如果HTTP 返回的状态非200 body可能会形如:
```
{
    "code": 1001,
    "message": "params format error"
}
```



1. 供inspector使用的接口
2. 供dashboard使用的接口

## 索引
|编号|接口名称|Method|接口URL|备注|
|:---|:---|:---|:---|:---|:---|
|[1.](#1-供inspector使用的接口)|供inspector使用的接口||||
|[1.1](#11-获取客户ip列表)|获取客户IP列表|GET|/inspector/v1/customer_ips||
|[1.2](#12-获取域名列表)|获取域名列表|GET|/inspector/v1/domains|从boss取|
|[1.3](#13-回写边缘节点信息)|回写边缘节点信息|PUT|/inspector/v1/domain/edge_nodes||
|[1.4](#14-回写域名质量信息)|回写域名服务质量信息|PUT|/inspector/v1/domain/quality||
|[1.5](#15-回写边缘节点质量信息)|回写边缘节点质量信息|PUT|/inspector/v1/node/quality||
|[2.](#2-供dashboard使用的接口)|供dashboard使用的接口||||
|[2.1](#21-获取客户ip列表)|获取客户IP列表|GET|/dashboard/v1/customer_ips||
|[2.2](#22-增加客户ip)|增加客户IP|POST|/dashboard/v1/customer_ips||
|[2.3](#23-查看客户ip)|查看客户IP|GET|/dashboard/v1/customer_ips/:id||
|[2.4](#24-删除客户ip)|删除客户IP|DELETE|/dashboard/v1/customer_ips/:id||
|[2.5](#25-获取域名列表)|获取域名列表|GET|/dashboard/v1/domains||
|[2.6](#26-获取某个域名的服务质量分省)|获取某个域名的服务质量(分省)|GET|/dashboard/v1/domains/:domain/quality||
|[2.7](#27-获取特定省份运营商域名的所有ip)|获取特定省份，运营商，域名的所有ip|GET|/dashboard/v1/domains/:domain/ip||
|[2.8](#28-获取某个域名特定省份运行商的服务质量可用性响应事件丢包率)|获取某个域名&特定省份&运行商的服务质量(可用性、响应事件、丢包率)|GET|/dashboard/v1/domains/:domain/history_quality||
|[2.9](#29-某个ip的服务质量可用性响应事件丢包率)|某个IP的服务质量(可用性、响应事件、丢包率)|GET|/dashboard/v1/ips/:ip/history_quality||
|[2.10](#210-域名质量统计信息)|域名质量统计信息|GET|/dashboard/v1/domains/:domain/statistics||
|[2.11](#211-获取不可用的节点)|获取不可用的节点|GET|/dashboard/v1/domains/:domain/bad_nodes||



## 1. 供inspector使用的接口
### 1.1 获取客户IP列表
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/inspector/v1/customer_ips

#### Params
无

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|id|int|主键||
|province|string|省份||
|isp|string|运营商|目前只有"电信"/"联通"/"移动"|
|ip|string|IP|||

```
[
	{
		"id": 1,
		"province": "新疆",
		"isp": "电信",
		"ip": "61.128.96.12"
	} ...
]
```

### 1.2 获取域名列表
[返回顶部](#索引)
参看[接口2.5](#25-获取域名列表)

### 1.3 回写边缘节点信息
[返回顶部](#索引)
#### Method
PUT

#### Request
##### URL
/inspector/v1/domain/edge_nodes


#### Body
```
{
	"domain": "x1.com",
	"views": [{
		"province": "北京",
		"isp": "移动",
		"ip_list": ["1.1.1.1", "1.1.1.2" ...]
	}, {
		"province": "北京",
		"isp": "联通",
		"ip_list": ["1.1.1.1", "1.1.1.2" ...]
	}, {
		"province": "北京",
		"isp": "电信",
		"ip_list": ["1.1.1.1", "1.1.1.2" ...]
	}]
}
```

#### Response
```
{
	"result": "success"
}
```

### 1.4 回写域名质量信息
[返回顶部](#索引)
#### Method
PUT

#### Request
##### URL
/inspector/v1/domain/quality

##### params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|packet_loss|int|丢包率|取分子部分|
|response_time|int|响应时间|毫秒|
|availability|int|可用性(百分数的分子部分)|%|

##### Body
```
{
	"domain": "x1.com",
	"views": [{
		"province": "北京",
		"isp": "移动",
		"packet_loss": 11,
		"response_time": 22,
		"availability": 33
	}, {
		"province": "北京",
		"isp": "联通",
		"packet_loss": 11,
		"response_time": 22,
		"availability": 33
	}, {
		"province": "北京",
		"isp": "电信",
		"packet_loss": 11,
		"response_time": 22,
		"availability": 33
	}]
}
```

#### Response
成功返回200
```
{
    "result": "success"
}
```
失败返回400(参数错误)
```
{
    "code": 1001
    "message": "parameter format error"
}
```
其他错误501


### 1.5 回写边缘节点质量信息
[返回顶部](#索引)
#### Method
PUT

#### Request
##### URL
/inspector/v1/node/quality

##### params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|packet_loss|int|丢包率|取分子部分|
|response_time|int|响应时间|毫秒|
|availability|int|可用性(百分数的分子部分)|%|

##### Body
```
[{
 "ip": "192.168.1.1",
 "packet_loss":5,
 "response_time": 50,
 "availability": 80
}]
```

#### Response
成功返回200
```
{
    "result": "success"
}
```

## 2. 供dashboard使用的接口
### 2.1 获取客户IP列表
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/customer_ips?start=0&length=10&searchword=192&province=北京&isp=联通

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|start|int|offset|可为null|
|length|int|limit|可为null|
|searchword|搜索ip|可为null|
|province|string|按省份过滤|可为null|
|isp|string|按isp过滤|可为null|

#### Response
```
{
	"total_count": 100,
	"ip_list": [
		{
			"ip": "1.1.1.1",
			"province": "北京",
			"isp": "联通",
			"created_at": "2017-11-21 08:00:00",
			"updated_at": "2017-11-21 08:00:00"
		},
		{
			"ip": "2.2.2.2",
			"province": "北京",
			"isp": "联通",
			"created_at": "2017-11-21 08:00:00",
			"updated_at": "2017-11-21 08:00:00"
		}
		...
	]
}
```

### 2.2 增加客户IP
[返回顶部](#索引)
#### Method
POST

#### Request
##### URL
/dashboard/v1/customer_ips

#### Params
无

#### Body
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|province|string|省份|必填|
|isp|string|运行商|必填|
|ip|string|ip|必填|

```
{
	"province": "北京",
	"isp": "联通",
	"ip": "1.1.1.1"
}
```


#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|id|int|id||
|province|string|省份||
|isp|string|运行商||
|ip|string|ip||

```
{
	"id": 101,
	"province": "北京",
	"isp": "联通",
	"ip": "1.1.1.1"
}
```


### 2.3 查看客户IP
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/customer_ips/:id

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|id|int|id|必填|


#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|id|int|id||
|province|string|省份||
|isp|string|运行商||
|ip|string|ip||

```
{
	"id": 101,
	"province": "北京",
	"isp": "联通",
	"ip": "1.1.1.1",
	"created_at": "2017-11-06T17:15:46+08:00",
	"updated_at": "2017-11-06T17:15:46+08:00"
}
```


### 2.4 删除客户IP
[返回顶部](#索引)
#### Method
DELETE

#### Request
##### URL
/dashboard/v1/customer_ips/:id

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|id|int|id|必填|

#### Response
无


### 2.5 获取域名列表
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains

#### Params
无

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|total_count|int|总数||
|domain_list|list|域名列表||

```
{
	"total_count": 1016,
	"domain_list": [
		"x.com",
		"y.com"
	]
}
```


### 2.6 获取某个域名的服务质量(分省)
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains/:domain/quality?isp=移动

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名|必填|
|isp|string|运营商|必填|

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名||
|data|dict|返回数据||

```
{
	"domain": "a.com",
	"data": [
		{
			"id": 1,
			"domain": "a.com",
			"province": "北京",
			"isp": "移动",
			"packet_loss": 10,
			"response_time": 10,
			"availability": 90
		},
		{
			"id": 2,
			"domain": "a.com",
			"province": "辽宁",
			"isp": "移动",
			"packet_loss": 11,
			"response_time": 11,
			"availability": 89
		}
	]
}
```

### 2.7 获取特定省份，运营商，域名的所有ip
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains/:domain/ip?province=广东&isp=移动

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名|必填|
|province|string|省份|必填|
|isp|string|运营商|必填|

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名||
|province|string|省份||
|isp|string|运营商||
|vendor|string|厂商||
|data|dict|返回数据||

```
{
	"total_count": 2,
	"domain": "a.com",
	"province": "北京",
	"isp": "移动",
	"data": [
		{
			"id": 1,
			"domain": "a.com",
			"province": "北京",
			"isp": "移动",
			"vendor": "自建",
			"ip": "1.1.1.1",
			"created_at": "2017-11-27T11:02:29+08:00",
			"updated_at": "2017-11-27T11:02:29+08:00"
		},
		{
			"id": 2,
			"domain": "a.com",
			"province": "北京",
			"isp": "移动",
			"vendor": "自建",
			"ip": "2.2.2.2",
			"created_at": "2017-11-27T11:03:07+08:00",
			"updated_at": "2017-11-27T11:03:07+08:00"
		}
	]
}
```


### 2.8 获取某个域名&特定省份&运行商的服务质量(可用性、响应事件、丢包率)
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains/:domain/history_quality?province=辽宁&isp=移动&start_date=xxx&end_date=xxx

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名|必填|
|province|string|省份|必填|
|isp|string|运营商|必填|
|start_date|int|时间戳|必填|
|end_date|int|时间戳|选填|

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名|必填|
|province|string|省份|必填|
|isp|string|服务商|必填|
|packet_loss|list|丢包率||
|response_time|list|响应时间||
|availability|list|可用率||

```
{
	"domain": "x.com",
	"province": "辽宁",
	"isp": "移动",
	"packet_loss": [{"timestamp": 1511242881, "value": 11}, ...],
	"response_time": [{"timestamp": 1511242881, "value": 11}, ...],
	"availability": [{"timestamp": 1511242881, "value": 11}, ...],
}
```

### 2.9 某个IP的服务质量(可用性、响应事件、丢包率)
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/ips/:ip/history_quality?start_date=xxx&end_date=xxx

#### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|ip|string|ip|必填|
|start_date|int|时间戳|必填|
|end_date|int|时间戳|选填|

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|ip|string|ip||
|packet_loss|list|丢包率||
|response_time|list|响应时间||
|availability|list|可用率||

```
{
	"ip": "1.1.1.1",
	"packet_loss": [{"timestamp": 1511242881, "value": 11}, ...],
	"response_time": [{"timestamp": 1511242881, "value": 11}, ...],
	"availability": [{"timestamp": 1511242881, "value": 11}, ...],
}
```

### 2.10 域名质量统计信息
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains/:domain/statistics

##### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名||

#### Response
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|average_availability|int|平均可用率||
|node_count|int|总服务节点数量||
|bad_count|int|不可用节点数量||

200
```
{
     "average_availability": 95,
     "node_count": 89,
     "bad_count": 5
}
```

### 2.11 获取不可用的节点
[返回顶部](#索引)
#### Method
GET

#### Request
##### URL
/dashboard/v1/domains/:domain/bad_nodes

##### Params
|字段|类型|说明|备注|
|:---|:---|:---|:---|
|domain|string|域名||


#### Response
200
```
[{
    "province":"新疆",
    "isp":"移动",
	"ip": "192.168.1.1",
	"packet_loss":5,
	"response_time": 50,
	"availability": 80
},{
    "province":"新疆",
    "isp":"移动",
	"ip": "192.168.1.1",
	"packet_loss":5,
	"response_time": 50,
	"availability": 80
}]
```
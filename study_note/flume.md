```
a1.sources = r1
a1.channels = c1
a1.sinks = k1

a1.sources.r1.type = http
a1.sources.r1.port = 1234
a1.sources.r1.channels = c1
a1.sources.r1.bind = 183.131.54.166
a1.sources.r1.handler = org.apache.flume.sink.solr.morphline.BlobHandler
#a1.sources.r1.handler = org.apache.flume.source.http.BLOBHandler
#a1.sources.r1.handler = org.apache.flume.source.http.JSONHandler
#a1.sources.r1.handler = com.sinobbd.source.http.JSONHandler
a1.sources.r1.handler.nickname = random props

a1.channels = c1
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 10000
a1.channels.c1.byteCapacityBufferPercentage = 20
a1.channels.c1.byteCapacity = 800000

a1.channels = c1
a1.sinks.k1.type = file_roll
a1.sinks.k1.channel = c1
a1.sinks.k1.sink.directory = /bbd/ssd01/app/apache-flume-1.8.0-bin/temp
```

1. 使用flume http服务端，修改url中的路径
2. java模拟快手http post 发送压缩格式的数据
3. flume使用JSONHandler，发送数据格式  和  限制； jar位置：flume-ng-core-1.8.0.jar
```
<dependency>
    <groupId>org.apache.tika</groupId>
    <artifactId>tika-core</artifactId>
    <version>1.5</version>
</dependency>
```
4. BLOBHandler：怎么发送数据未知， jar位置：flume-ng-core-1.8.0.jar
5. BlobHandler： 发送数据直接全部原样放进去；jar位置：flume-ng-morphline-solr-sink-1.8.0.jar


net.sf.json.JSONArray在哪个jar包: https://zhidao.baidu.com/question/1579905841188157540.html
ezmorph-1.0.6.jar


InputStream转ByteArrayOutputStream：http://blog.csdn.net/it_magician/article/details/9240727

使double保留两位小数的多方法 java保留两位小数：http://blog.csdn.net/a2459956664/article/details/55668495


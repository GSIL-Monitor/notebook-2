# 1. flume http服务
- 使用flume做http服务端，采集数据到es（http source、kafka channel、elasticsearch sink）

- 配置文件如下
  ```
  ksh.sources = ksHttpPost
  ksh.channels = kafkaChannel
  ksh.sinks = essink1

  # For each one of the sources, the type is defined
  ksh.sources.ksHttpPost.type = http
  ksh.sources.ksHttpPost.port = 1234
  ksh.sources.ksHttpPost.bind = x.x.x.x
  ksh.sources.ksHttpPost.handler = org.apache.flume.source.http.JSONHandler
  #ksh.sources.ksHttpPost.handler = org.apache.flume.sink.solr.morphline.BlobHandler
  ksh.sources.ksHttpPost.channels = kafkaChannel
  #ksh.sources.ksHttpPost.handler.nickname = random props

  # Each channel's type is defined.
  ksh.channels.kafkaChannel.type = org.apache.flume.channel.kafka.KafkaChannel
  ksh.channels.kafkaChannel.kafka.topic = kshttplog
  ksh.channels.kafkaChannel.kafka.consumer.group.id = kshttpgroup-flume
  ksh.channels.kafkaChannel.kafka.producer.acks = 1
  ksh.channels.kafkaChannel.kafka.consumer.auto.offset.reset = latest
  ksh.channels.kafkaChannel.parseAsFlumeEvent = false
  ksh.channels.kafkaChannel.kafka.bootstrap.servers = x.x.x.x:9092

  ksh.sinks.essink1.channel = kafkaChannel
  ksh.sinks.essink1.type = org.apache.flume.sink.elasticsearch.ElasticSearchSinkKshttp
  ksh.sinks.essink1.hostNames = x.x.x.x:9300
  ksh.sinks.essink1.indexName = kshttplogpron-%{_rm_logdate}
  ksh.sinks.essink1.indexType = fluentd
  ksh.sinks.essink1.indexNameBuilder=org.apache.flume.sink.elasticsearch.SimpleIndexNameBuilder
  ksh.sinks.essink1.clusterName = bbdes
  ksh.sinks.essink1.batchSize = 1000
  ksh.sinks.essink1.ttl = 30d
  ksh.sinks.essink1.serializer = org.apache.flume.sink.elasticsearch.ElasticSearchDynamicSerializer
  ```

- 修改点：
1. 使用flume http服务端，修改url中的路径
  修改类 org.apache.flume.source.http.HTTPSource 中的 start 方法，205行，修改如下：添加 kshttplog
  `root.addServlet(new ServletHolder(new FlumeHTTPServlet()), "/kshttplog");`
  如此url中就可以使用curl向地址 `http://x.x.x.x:1234/kshttplog` 发送数据，如果不修改地址则是`http://x.x.x.x:1234`
2. java模拟快手http post 发送压缩格式的数据
  ```
  import java.io.*;
  import java.nio.charset.Charset;
  import java.util.zip.GZIPInputStream;  
  import java.util.zip.GZIPOutputStream;
  import org.apache.http.HttpResponse;
  import org.apache.http.HttpStatus;
  import org.apache.http.client.HttpClient;
  import org.apache.http.client.methods.HttpPost;
  import org.apache.http.entity.StringEntity;
  import org.apache.http.impl.client.DefaultHttpClient;
  import org.apache.http.util.EntityUtils;
    
  public class GZipUtils {  
      public static String compress(String str) throws IOException {
          if (str == null || str.length() == 0) {
              return str;  
          }
          ByteArrayOutputStream out = new ByteArrayOutputStream();  
          GZIPOutputStream gzip = new GZIPOutputStream(out);  
  //        gzip.write(str.getBytes());
          gzip.write(str.getBytes());
          gzip.close();
          return out.toString("ISO-8859-1"); // UTF-8 ISO-8859-1
  //        return out.toString("utf-8"); // UTF-8 ISO-8859-1
  //        return out.toString();
      }
      // 解压缩  
      public static String uncompress(String str) throws IOException {
          if (str == null || str.length() == 0) {
              return str;
          }
          ByteArrayOutputStream out = new ByteArrayOutputStream();
          ByteArrayInputStream in = new ByteArrayInputStream(str.getBytes());
          GZIPInputStream gunzip = new GZIPInputStream(in);
          byte[] buffer = new byte[256];
          int n;
          while ((n = gunzip.read(buffer)) >= 0) {
              out.write(buffer, 0, n);
          }
          // toString()使用平台默认编码，也可以显式的指定如toString("GBK")
          return out.toString();
      }

      public static byte[] uncompress(byte[] bytes) {
          if (bytes == null || bytes.length == 0) {
              return null;
          }
          ByteArrayOutputStream out = new ByteArrayOutputStream();
          ByteArrayInputStream in = new ByteArrayInputStream(bytes);
          try {
              GZIPInputStream ungzip = new GZIPInputStream(in);
              byte[] buffer = new byte[1024];
              int n;
              while ((n = ungzip.read(buffer)) >= 0) {
                  out.write(buffer, 0, n);
              }
          } catch (IOException e) {

          }
          return out.toByteArray();
      }

      public static void main(String[] args) throws IOException {
      	  
          String str = "[{\"date\":1520908140001,\"area\":\"xizang\",\"fail\":0.2,\"slow\":0,\"isp\":\"chinaunicom\",\"success_count\":1,\"slow_count\":0,\"id\":\"f2303bd087706de0c93363dae0fcca96\",\"fail_count\":0},{\"date\":1520908380002,\"area\":\"xizang\",\"fail\":0,\"slow\":0,\"isp\":\"chinaunicom\",\"success_count\":2,\"slow_count\":0,\"id\":\"d9d25554fa3cf41f265bbd8c51543d09\",\"fail_count\":0},{\"date\":1520908860003,\"area\":\"xizang\",\"fail\":0,\"slow\":0,\"isp\":\"chinaunicom\",\"success_count\":2,\"slow_count\":0,\"id\":\"423148a87fc46de1a9a979c2c4df74c1\",\"fail_count\":0}]";
            
          HttpClient httpClient = new DefaultHttpClient();  
          HttpPost method = new HttpPost("http://183.131.54.166:1235/kshttplog");
  //        method.addHeader("Content-type","application/json; charset=ISO-8859-1");
          method.addHeader("Content-type","application/json; charset=UTF-8");
          method.setHeader("Accept", "application/json");
          method.setHeader("Content-Encoding", "gzip");
        
          method.setEntity(new StringEntity(GZipUtils.compress(str)));
  //        method.setEntity(new StringEntity(str, Charset.forName("UTF-8")));
          String s1 = new String(uncompress(GZipUtils.compress(str).getBytes(Charset.forName("ISO-8859-1"))));
  //        String s1 = new String(uncompress(GZipUtils.compress(str).getBytes()));
  //        String s2 = uncompress(GZipUtils.compress(str));
          System.out.println("----------------->" + s1);
  //        System.out.println("----------------->" + s2);

          HttpResponse response = httpClient.execute(method);  
          int statusCode = response.getStatusLine().getStatusCode();
          if (statusCode != HttpStatus.SC_OK) {  
              System.out.println("error...");
          }  
          // Read the response body  
          String body = EntityUtils.toString(response.getEntity());  
          System.out.println("------------"+body+"+++++++");
      }
  }
  ```
3. flume使用JSONHandler，发送数据格式  和  限制； jar位置：flume-ng-core-1.8.0.jar
- 原始的发送数据格式：`[{"headers":{"":""},"body":"the first event"}]`，保存的是body里的信息，而且body里不能为json，需要是字符串
- 更改后发送数据格式：`[{\"a\":\"123\"},{\"a\":\"123\"}]`, 数据按行放入kafka
  - 非压缩格式的数据
    ```
    String str, wholeStr = "";
    while((str = reader.readLine()) != null){
    wholeStr += str;
    }
    JsonParser parser = new JsonParser();
    JsonArray Jarray = parser.parse(wholeStr).getAsJsonArray();
    List<Event> newEvents = new ArrayList<Event>(Jarray.size());
    for(JsonElement obj : Jarray ){
    	String row=obj.toString();
        //System.out.println(" one row ======="+ row);
       newEvents.add(EventBuilder.withBody(row.getBytes(),null));
    }
    return newEvents;
    ```

  - 压缩格式的数据处理
    ```
    InputStream in = request.getInputStream();
    ByteArrayOutputStream blob = new ByteArrayOutputStream();
    byte[] buffer = new byte[1024];
    int len;
    while ((len = in.read(buffer)) > -1 ) {
      blob.write(buffer, 0, len);
    }
    blob.flush();
    String wholeStr = uncompress(blob.toString("ISO-8859-1"));
    //LOG.info("----------------------->" + wholeStr);
    JsonParser parser = new JsonParser();
    JsonArray Jarray = parser.parse(wholeStr).getAsJsonArray();
    List<Event> newEvents = new ArrayList<Event>(Jarray.size());
    for(JsonElement obj : Jarray ){
    	String row=obj.toString();
    //System.out.println(" one row ======="+ row);
       newEvents.add(EventBuilder.withBody(row.getBytes(),null));
    }

    return newEvents;
    ```

  - 解压函数
     ```
     public static String uncompress(String str) throws IOException {
       /*if (bytes == null || bytes.length == 0) {
         return null;
       }*/
       java.io.ByteArrayOutputStream out = new java.io.ByteArrayOutputStream();
       ByteArrayInputStream in = new ByteArrayInputStream(str.getBytes(Charset.forName("ISO-8859-1")));
       try {
         GZIPInputStream ungzip = new GZIPInputStream(in);
         byte[] buffer = new byte[1024];
         int n;
         while ((n = ungzip.read(buffer)) >= 0) {
           out.write(buffer, 0, n);
         }
       } catch (IOException e) {
         LOG.error("解压缩异常......." + e.toString());
       }
       
       return new String(out.toByteArray());
     }
     ```
    - pom.xml
      ```
      <dependency>
      <groupId>org.apache.tika</groupId>
      <artifactId>tika-core</artifactId>
      <version>1.5</version>
      </dependency>
      <dependency>
      <groupId>net.sf.json-lib</groupId>
      <artifactId>json-lib</artifactId>
      <version>2.4</version>
      <classifier>jdk15</classifier>
      </dependency>
      ```

4. BLOBHandler：怎么发送数据未知， jar位置：flume-ng-core-1.8.0.jar

5. BlobHandler： 发送数据直接全部原样放进去；jar位置：flume-ng-morphline-solr-sink-1.8.0.jar
  - 修改前数据: 
    ```
    [{"a":"123"},{"a":"123"}]
    ```
  - 修改后数据：
    ```
    {"a":"123"}
    {"a":"123"}
    ```
  - 代码修改，处理压缩数据，解压缩方法和上面一样
    ```
    #ByteArrayOutputStream blob = null;
    List<Event> eventList = new ArrayList<Event>();
    String str = uncompress(array, blob.toString("ISO-8859-1"));
    //LOGGER.info("------------> str:" + str);
    net.sf.json.JSONArray json = net.sf.json.JSONArray.fromObject(str);
    if(json.size()>0){
      for(int i=0;i<json.size();i++){
        JSONObject job = json.getJSONObject(i);
        SimpleEvent myEvent = new SimpleEvent();
        myEvent.setBody(job.toString().getBytes());
        eventList.add(myEvent);
      }
    }
      return eventList;
    ```

6. 相关问题处理
- json插件pom文件要加上jdk版本：jdk15；参考 “net.sf.json.JSONArray在哪个jar包” ：https://zhidao.baidu.com/question/1579905841188157540.html
- 加入的相关包
  flume-ng-core-1.8.0.jar
  flume-ng-morphline-solr-sink-1.8.0.jar

  json-lib-2.4-jdk15.jar
  ezmorph-1.0.6.jar
  commons-beanutils-core-1.8.0.jar

- InputStream转ByteArrayOutputStream：http://blog.csdn.net/it_magician/article/details/9240727
- 使double保留两位小数的多方法 java保留两位小数：http://blog.csdn.net/a2459956664/article/details/55668495



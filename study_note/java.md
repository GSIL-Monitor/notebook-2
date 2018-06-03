# **java**

## 1、java基础
### 1.1、java cp命令
[Java -cp 命令行引用多个jar包的简单写法（Windows、Linux）](http://blog.csdn.net/hongweigg/article/details/53464639)
1. Windows下用法
  在Windows上，可以使用
  ```
  java your-jar-lib-folder/* your-main-class
  ```
  `your-jar-lib-folder` 为存放一堆jar文件的路径。
  `your-main-class` 为要运行的具有main方法的主类。
  JDK 1.7及以上版本支持该写法。

2. Linux下用法
  在Linux上，这样写似乎并不支持，通配符转换后的文件路径用空格分隔，导致JAVA虚拟机只能认到前面一个。解决办法，使用SHELL脚本拼接：
  ```
  export  CLASSPATH=`find$your-jar-lib-folder  -name  *.jar | xargs | sed  "s/ /:/g"`
  ```
  这里 `$your-jar-jar-folder` 为包含 jar 的路径变量，可以用 `export` 设置，也可以写绝对路径，执行完后，就是把一大堆jar包用":"拼接在一起了。然后使用该类路径变量：
  ```
  java -cp $CLASSPATH your-main-class param1 param2...
  ```
  param1，param2为其他要传入的运行参数。

3. 通过指定Java扩展包路径变量（Windows、Linux均可使用）
  在java参数中加入虚拟机参数：
  ```
  java -Djava.ext.dirs=your-jar-lib-folder your-main-class
  ```
  该方法对整个虚拟机都会有影响，如果Java加载的包需要相互隔离，则不适宜。


### 1.2、时间
#### 1.2.1、根据时间获取小时    
    private static DateTimeFormatter formatter = DateTimeFormat.forPattern("dd/MMM/yyyy:HH:mm:ss Z").withLocale(Locale.US);
    private static DateTimeFormatter formatterH = DateTimeFormat.forPattern("HH");
    
    /**
     * 根据指定的时间 获取 小时
     * @param local_time
     * @return
     */
    public static String timeToHour(String local_time){
    	try {
    		return formatter.parseDateTime(local_time).toString(formatterH);
    	} catch (Exception e) {
    		return "error";
    	}
    }

### 1.3、字符串转list
```List<String> gameids = java.util.Arrays.asList(gameString.split(","));```

## 2、java异常
### 2.1、IDEA编译异常
异常信息：Error:java: Compilation failed: internal java compiler error
处理：ctrl+alt+s --》 修改java compiler

### 2.2、java -cp执行异常

异常：Exception in thread "main" java.lang.UnsupportedClassVersionError: logpackage/mvfile/MvHdfsFile : Unsupported major.minor version 52.0







# java资源链接
《深入理解 Java 内存模型》读书笔记：https://mp.weixin.qq.com/s?__biz=MzIxMTE0ODU5NQ==&mid=2650237071&idx=1&sn=354a9735b0000a215c77c942d3b2a566&chksm=8f5a02d3b82d8bc526af7fd2a89ba805a7377562b9f74295ac98b0e118aac796b16186958a4d&mpshare=1&scene=23&srcid=0302xviToj8leUuYQcmCVn4H#rd


# **java**

## 1、java基础
### 1.1、java cp命令
[Java -cp 命令行引用多个jar包的简单写法（Windows、Linux）](http://blog.csdn.net/hongweigg/article/details/53464639)


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


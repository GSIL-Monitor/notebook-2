# **Linux**

# **索引**  
[1、linux](#1、linux)  
[2、shell](#2、shell)  
&emsp;[2.1、时间](#2.1、时间)  
&emsp;[2.2、字符串截取](#2.2、字符串截取)  
&emsp;[2.3、执行的脚本获取当前文件名](2.3、执行的脚本获取当前文件名)
[3、服务安装](#3、服务安装)  
&emsp; [3.1、httpd](#3.1、httpd)  
&emsp; [3.2、](#3.2)    
[4、网址](#4、网址)



## 1、linux [返回索引](#索引)




## 2、shell [返回索引](#索引)
### 2.1、时间
    date -d '2017-12-14 16:52:10 +0800' +%s #指定时间转timeunix

### 2.2、字符串截取
    var="http://www.fengbohello.xin3e.com/blog/shell-truncating-string"
    echo ${#var} #字符长度
    
    echo ${var#*/}	#从左开始第一个/开始往后截取
    echo ${var##*/} #从左最后一个/开始往后截取
    
    echo ${var%/*}	#从右开始第一个/开始往前截取
    echo ${var%%/*}	#从右开始最后一个/开始往前截取
    
    echo ${var:start:len}	#从左开始从start开始截取len位
    echo ${var:start}		#从左开始从start开始截到最后
    
    echo ${var:0-start:len}	#从右开始第start位往右截取len位
    echo ${var:0-start}		#从右开始第start位往右截取到最后

### 2.3、执行的脚本获取当前文件名
获取文件名：`$(basename $0)`



## 3、服务安装<span id="3"> [返回索引](#索引)
### 3.1、httpd<span id="3.1">
#### 3.1.1、安装
    yum install -y httpd
    chkconfig --list|grep httpd #查看是否开机自启
    chkconfig httpd on #开机自启
    cd /etc/httpd/conf.d #进入目录删除欢迎页面
    mv welcome.conf welcome.conf.bak
#### 3.1.2、判断是否成功：  
> 浏览器中访问:http://192.168.229.129/

### 2、

## 4、网址
[sort排序](http://blog.csdn.net/liu_sheng_1991/article/details/53230604)  
[字符串截取](https://www.cnblogs.com/fengbohello/p/5954895.html)  
[数组操作](https://www.cnblogs.com/Joke-Shi/p/5705856.html)  
[向shell 函数传递数组 和 从函数返回数组](http://blog.csdn.net/guizaijianchic/article/details/78012179)  
[linux shell 解压缩](http://blog.csdn.net/mingde_he/article/details/5750857)
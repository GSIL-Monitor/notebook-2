# **Linux**

## **索引**  
[1、linux](#1linux)  
&emsp;[1.1、文件过多导致不能写入](#11文件过多导致不能写入)
&emsp;[1.2 SELinux](#12SELinux)
&emsp;&emsp;[1.2.1 查看SELinux状态](#查看SELinux状态)
&emsp;&emsp;[1.2.2 关闭SELinux](#关闭SELinux)

[2、shell](#2shell)  
&emsp;[2.1、时间](#21时间)  
&emsp;[2.2、字符串截取](#22字符串截取)  
&emsp;[2.3、执行的脚本获取当前文件名](23执行的脚本获取当前文件名)
[3、服务安装](#3服务安装)  
&emsp; [3.1、httpd](#31httpd)  
&emsp; [3.2、](#32)    
[4、网址](#4网址)



## 1、linux
### 1.1、文件过多导致不能写入
> [root@c3n-zj-nb1-183-136-128-47 delete_jenkins_logs]# df -i /
> Filesystem      Inodes  IUsed   IFree IUse% Mounted on
> /dev/sdm2      1966080 410549 1555531   21% /

### 1.2 SELinux
#### 1.2.1 查看SELinux状态
- `/usr/sbin/sestatus -v #如果SELinux status参数为enabled即为开启状态`
- getenforce#也可以用这个命令检查
#### 1.2.2 关闭SELinux
- 临时关闭（不用重启机器）
  `setenforce 0 #设置SELinux 成为permissive模式，setenforce 1 设置SELinux 成为enforcing模式`
- 修改配置文件，需要重启机器
  ```
  vim /etc/selinux/config
  SELINUX=disabled
  ```


# 2、shell
## 2.1、时间
    date -d '2017-12-14 16:52:10 +0800' +%s #指定时间转timeunix

## 2.2、字符串截取
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

## 2.3、执行的脚本获取当前文件名
获取文件名：`$(basename $0)`

## 2.4、shell中IFS用法
> IFS的值是空格，制表符，回车
```
echo "a b" > test
for i in `cat test`;do echo $i; done
```
结果：
> a
> b
```
OLD_IFS="$IFS"
IFS=$'\n' 
for i in `cat test`;do echo $i; done
IFS=$OLD_IFS
```
> 结果：
> a b

## 2.5、按最后修改时间删除文件夹
### 2.5.1、删除7天前的文件夹 
```
find /var/lib/jenkins/jobs/*/builds/* -mtime +7 -type d | xargs rm -rf
```

## 2.6 把字符串当执行命令直接执行
- vim test
```
echo "abc"
```
- 执行
```
a=`cat test`
echo $a
$a
#$a 直接这样执行是不行的，使用eval
eval ${a}
```
- 上面执行结果
> echo "abc"
> "abc"
> abc


## 2.7 加密
```
ak='92b83849e89143d01646df9f92b41c8306501f'
uri='/kuaishou/slow_speed/_send'
timestame=`date +%s`
#timestame=1517803989
str=$ak$uri$timestame

BASE=`echo -n "${str}"|md5sum|cut -f 1 -d " "`
str2=$timestame:$BASE
echo -n "$str2"|base64
```

# 3、服务安装<span id="3"> [返回索引](#索引)
## 3.1、httpd<span id="3.1">
### 3.1.1、安装
    yum install -y httpd
    chkconfig --list|grep httpd #查看是否开机自启
    chkconfig httpd on #开机自启
    cd /etc/httpd/conf.d #进入目录删除欢迎页面
    mv welcome.conf welcome.conf.bak
### 3.1.2、判断是否成功：  
> 浏览器中访问:http://192.168.229.129/

## 2、

# 4、网址
[sort排序](http://blog.csdn.net/liu_sheng_1991/article/details/53230604)  
[字符串截取](https://www.cnblogs.com/fengbohello/p/5954895.html)  
[数组操作](https://www.cnblogs.com/Joke-Shi/p/5705856.html)  
[向shell 函数传递数组 和 从函数返回数组](http://blog.csdn.net/guizaijianchic/article/details/78012179)  
[linux shell 解压缩](http://blog.csdn.net/mingde_he/article/details/5750857)
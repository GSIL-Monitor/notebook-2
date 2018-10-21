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



# 1、linux
## 1.1、文件过多导致不能写入
> [root@c3n-zj-nb1-183-136-128-47 delete_jenkins_logs]# df -i /
> Filesystem      Inodes  IUsed   IFree IUse% Mounted on
> /dev/sdm2      1966080 410549 1555531   21% /

## 1.2 SELinux
### 1.2.1 查看SELinux状态
- `/usr/sbin/sestatus -v #如果SELinux status参数为enabled即为开启状态`
- getenforce#也可以用这个命令检查
### 1.2.2 关闭SELinux
- 临时关闭（不用重启机器）
  `setenforce 0 #设置SELinux 成为permissive模式，setenforce 1 设置SELinux 成为enforcing模式`
- 修改配置文件，需要重启机器
  ```
  vim /etc/selinux/config
  SELINUX=disabled
  ```
## 1.3 普通用户添加root权限

### 1.3.1 参考地址：
- 友情提醒：虽然方法三看上去简单方便，但一般不推荐使用，推荐使用方法二

  > [http://blog.sina.com.cn/s/blog_6fc583e70100n6rm.html](http://blog.sina.com.cn/s/blog_6fc583e70100n6rm.html)

### 1.3.2 方法一
- 修改 /etc/sudoers 文件，找到下面一行，把前面的注释（#）去掉
```
## Allows people in group wheel to run all commands
%wheel    ALL=(ALL)    ALL
```
- 然后修改用户，使其属于root组（wheel），命令如下：
  ```#usermod -g root tommy```
- 修改完毕，现在可以用tommy帐号登录，然后用命令 su - ，即可获得root权限进行操作。

### 1.3.3 方法二
- 修改 /etc/sudoers 文件，找到下面一行，在root下面添加一行，如下所示
```
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
tommy   ALL=(ALL)     ALL
```
- 修改完毕，现在可以用tommy帐号登录，然后用命令 su - ，即可获得root权限进行操作。

### 1.3.3 方法三
- 修改 /etc/passwd 文件，找到如下行，把用户ID修改为 0 ，如下所示
```
tommy:x:500:500:tommy:/home/tommy:/bin/bash

#修改后如下
tommy:x:0:500:tommy:/home/tommy:/bin/bash
```
- 保存，用tommy账户登录后，直接获取的就是root帐号的权限。

## 1.4 查看某软件是否已安装
```
rpm -qa | grep ruby
dpkg-l|grepruby
yum list installed | grep ruby
```

## 1.5 Linux 下使用Trickle限制下载/上传带宽
http://blog.csdn.net/jb19900111/article/details/17756195

## 1.6 关闭开机自启
```
chkconfig servicename list #查看
chkconfig servicename off #关闭开机自启
```

# 2、shell
## 2.1、时间
  ```
  #当前时间
  date '+%Y-%m-%d %H:%M:%S' #时间
  date +%s #时间戳
  
  #在指定的时间基础上加一分钟：
  date -d "2017-09-18 14:41 1 minute" +"%Y-%m-%d %H:%M:%S"
  
  #指定时间转timeunix
  date -d '2017-12-14 16:52:10 +0800' +%s	
  
  #时间戳转时间：
  date -d @1438617600 "+%Y-%m-%d %H:%M:%S"
  ```

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

## 2.8 shell锁，保证只有一个实例
- Body部分为你的程序
```
# start
SELF_NAME=`basename $0`
PID_FILE=/var/run/$SELF_NAME.pid
# running lock 
if ([ -f $PID_FILE ] && kill -0 `cat $PID_FILE` 2>/dev/null); then
       # if grep -q $SELF_NAME /proc/`cat $PID_FILE 2>/dev/null`/cmdline; then
                echo "$SELF_NAME already is running... "
                exit 9
       # fi
fi
echo $$ >$PID_FILE
trap "rm -f $PID_FILE; exit 1" 2

# body
/bbd/sata09/bigdata/sinobbd/task/band/jdk1.8.0_111/bin/java -jar  /bbd/sata09/bigdata/sinobbd/task/band5mto1h_fcdn/Bandwidth5mTo1h.jar 1>>/bbd/sata09/bigdata/sinobbd/task/band5mto1h_fcdn/logs/task`date -d today +%Y%m%d`.log  2>>/bbd/sata09/bigdata/sinobbd/task/band5mto1h_fcdn/logs/error.log &
echo start running...
sleep 10

# end
rm -f $PID_FILE; exit 0
```
## 2.9 shell运算
### 2.9.1 小数运算
- expr只能用于整数之间的运算
  ```
  printf "%f" `echo 0.5432+2.432|bc`
  echo "scale=2;0.97 / 0.48" | bc
  ```
### 2.9.2 保留2位小数
```
awk 'BEGIN{printf "%.2f\n",'$size_count'/1024}'
```

## 2.10 awk
### 2.10.1 取值
```
$(NF-1) #倒数第二个
awk –F ' ' '{print $NF}' #最后一个
```

## 2.11 文件置空
### 2.11.1 彻底置空
- 彻底置空，也就是ls文件的大小为0，文件里面什么都没有
  ```
  : > filename
  true > filename
  cat /dev/null > filename
  > filename
  ```
### 2.11.1 普通置空
- 置空文件，但是文件中有空行，ls文件的大小，显示还有大小
  ```
  echo "" > filename
  echo > filename
  ```

## 2.12 json处理
- 参考地址
> [http://blog.csdn.net/u011641885/article/details/45559031](http://blog.csdn.net/u011641885/article/details/45559031)
> [http://blog.csdn.net/offbye/article/details/38379195](http://blog.csdn.net/offbye/article/details/38379195)



## 2.13 字符串转数组
```
function str_to_arr(){
	str=$1 #字符
	sep=$2 #分隔符
	
	OLD_IFS="$IFS"
	IFS="$sep"
	arr=($str)
	IFS="$OLD_IFS"

	echo "${arr[@]}"
}

result=( str_to_arr "ERROR,100,1" ",")
echo ${result[0]}
```

## 2.14 文件重命名
Linux Shell 批量重命名的方法总览: http://blog.csdn.net/kwame211/article/details/76019823
有些系统的rename好像不支持正则: https://bbs.csdn.net/topics/392089145?page=1
修复Bulk Rename不能使用正则表达式的Bug : http://blog.chinaunix.net/uid-20579666-id-1920361.html
rename 命令： http://man.linuxde.net/rename
rename在centos6.7中正则好像无法使用

`sed -i 's/aa/bb/g' file`

```
for i in `ls ./`
do
	if [[ $(echo $i | grep "t") != "" ]]
	then
			newName=`echo $i|sed s/t/boy/g`
			echo $newName
			mv $i $newName
	fi
done
```

## 2.15 shell算术运算
```shell
((i=$j+$k))    等价于 i=`expr $j + $k`
((i=$j-$k))     等价于   i=`expr $j -$k`
((i=$j*$k))     等价于   i=`expr $j \*$k`
((i=$j/$k))     等价于   i=`expr $j /$k`
```
> +：对两个变量做加法。
> -：对两个变量做减法。
> *：对两个变量做乘法。
> /：对两个变量做除法。
> **：对两个变量做幂运算。
> %：取模运算，第一个变量除以第二个变量求余数。
> +=：加等于，在自身基础上加第二个变量。
> -=：减等于，在第一个变量的基础上减去第二个变量。
> *=：乘等于，在第一个变量的基础上乘以第二个变量。
> /=：除等于，在第一个变量的基础上除以第二个变量。
> %=：取模赋值，第一个变量对第二个变量取模运算，再赋值给第一个变量。


# 3、服务安装
## 3.1、httpd
### 3.1.1、安装
    yum install -y httpd
    chkconfig --list|grep httpd #查看是否开机自启
    chkconfig httpd on #开机自启
    cd /etc/httpd/conf.d #进入目录删除欢迎页面
    mv welcome.conf welcome.conf.bak
### 3.1.2、判断是否成功：  
- 浏览器中访问:http://192.168.229.129/




# 资源

[sort排序](http://blog.csdn.net/liu_sheng_1991/article/details/53230604)  
[字符串截取](https://www.cnblogs.com/fengbohello/p/5954895.html)  
[数组操作](https://www.cnblogs.com/Joke-Shi/p/5705856.html)  
[向shell 函数传递数组 和 从函数返回数组](http://blog.csdn.net/guizaijianchic/article/details/78012179)  
[linux shell 解压缩](http://blog.csdn.net/mingde_he/article/details/5750857)
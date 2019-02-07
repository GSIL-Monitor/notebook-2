# 一、vmware linux 配置大数据环境

## 1. 基本修改
### 1.1 图形界面

  ```shell
  vim /etc/inittab
  # 3为黑窗口，5为图形界面
  id:3:initdefault:
  
  #立即生效（临时）
  init 3   #关闭图形界面
  init 5   #打开图形界面
  ```

### 1.2 修改主机名

  ```shell
  # 修改
  vim /etc/sysconfig/network
  
  #查看本机名字
  hostname
  ```

### 1.3 开机等待时间

```shell
vim /boot/grub/menu.lst
```

> 开机等待时间默认为5秒，将之设置为0秒后，保存后重新启动服务器，就会迅速跳过开机选项进入系统，不过建议设置为1秒，这样忘记密码时，可以及时按下重新设置密码的选项

### 1.4 系统时间

- 修改系统时间

```shell
date -s 06/18/14（2014年6月18日）
date -s 14:20:50（14点20分50秒）
date 0618141614.30（2014年6月18日14点16分30秒（MMDDhhmmYYYY.ss））
```

- 修改硬件时间

```shell
# 查看
hwclock  --show
# 或者
clock  --show

# 修改
hwclock --set --date="06/18/14 14:55" （月/日/年时:分:秒）
# 或者
clock --set --date="06/18/14 14:55" （月/日/年时:分:秒）
```

- 同步时间

```shell
# 硬件时钟同步系统时钟(硬件为准),（hc代表硬件时间，sys代表系统时间）
hwclock --hctosys
#或者
clock --hctosys

# 用系统时钟同步硬件时钟
hwclock --systohc
#或者
clock --systohc
```

### 1.5 防火墙

- 查看状态

```shell
service iptables status
/etc/init.d/iptables status
```

- 关闭

```shell
# 重启后生效，on:开启
chkconfig iptables off

# 即时生效，重启后失效, start:开启
service iptables stop
```

- 验证

```shell
chkconfig --list | grep iptables
```

### 1.6 安装 lrzsz

```
yum install -y lrzsz
```

## 2. 大数据环境配置

### 2.1 基本配置

- 克隆2个副本

- 修改克隆后的副本的主机名

  ```shell
  vim /etc/sysconfig/network
  
  # 上面命令需要重启机器，先hostname修改
  hostname work1
  hostname work2
  ```

- 修改 `host` 文件

  ```shell
  # vim /etc/host
  192.168.154.130 master
  192.168.154.132 work1
  192.168.154.133 work2
  ```

- 配置ssh

  ```shell
  # 生成key
  ssh-keygen
  
  # 从A复制到B上, 记得执行这步骤之前修改主机名
  ssh-copy-id work1
  
  # 验证
  ssh work1
  
  # 使用hadoop用户配置ssh使三台互通
  su - hadoop
  # 批量执行
  ssh-copy-id master
  ssh-copy-id work1
  ssh-copy-id work2
  ```

### 2.2 基本开发工具

#### 2.2.1 java、scala

- 上传、解压、软连接

  ```shell
  tar -zxvf jdk-8u181-linux-x64.tar.gz
  tar -zxvf scala-2.11.12.tgz
  
  ln -s jdk1.8.0_181 jdk1.8
  ln -s scala-2.11.12 scala2.11
  ```

- scp.sh

  ```shell
  #!/bin/bash
  if [ $# != 2 ];then
    exit 1
  fi
  scp -r $1 work1:$2
  scp -r $1 work2:$2
  ```

- vim /etc/profile

```shell
# jdk
export JAVA_HOME=/zz/app/jdk1.8
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# scala
export SCALA_HOME=/zz/app/scala2.11

export PATH=$JAVA_HOME/bin:$SCALA_HOME/bin:$PATH
```

### 2.3 搭建集群

hadoop、kafka、spark、flink、hbase、hive、mysql



# 二、linux

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


# 三、服务安装
## 3.1、httpd
### 3.1.1、安装
    yum install -y httpd
    chkconfig --list|grep httpd #查看是否开机自启
    chkconfig httpd on #开机自启
    cd /etc/httpd/conf.d #进入目录删除欢迎页面
    mv welcome.conf welcome.conf.bak
### 3.1.2、判断是否成功：  
- 浏览器中访问:http://192.168.229.129/





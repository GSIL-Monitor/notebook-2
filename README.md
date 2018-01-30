# notebook
# **mysql**

<span id="索引">**索引**<span>  
[1、绿色版mysql安装](#1)  
&emsp; [1.1、下载下载绿色版安装包](#1.1)   
&emsp; [1.2、创建组合用户](#1.2)  
&emsp; [1.3、安装](#1.3)  
&emsp;&emsp; [1.3.1 解压、创建软连接](#1.3.1)  
&emsp;&emsp; [1.3.2 配置](#1.3.2)  
&emsp;&emsp; [1.3.3 更改目录权限](#1.3.3)  
&emsp;&emsp; [1.3.4 安装依赖包、安装mysql](#1.3.4)  
&emsp;&emsp; [1.3.5 命令的地方显示当前目录](#1.3.5)  
&emsp;&emsp; [1.3.6 删除配置文件](#1.3.6)  
&emsp;&emsp; [1.3.7 查看进程、启动mysql](#1.3.7)  
&emsp;&emsp; [1.3.8更改密码、删除空账号](#1.3.8)  
&emsp;&emsp; [1.3.9 开机自启动](#1.3.9)  


## <span id="1">1、绿色版mysql安装
### <span id="1.1">1.1、下载下载绿色版安装包  
> [https://downloads.mysql.com/archives/community/](https://downloads.mysql.com/archives/community/)  
> system选择"linux - generic",然后下载对应的tar.gz包

### <span id="1.2">1.2、创建组合用户：
用户家目录：/usr/local/mysql
  
    groupadd -g 101 dba
    useradd -u 514 -g dba -G root -d /usr/local/mysql mysqladmin

### <span id="1.3">1.3、安装  
#### <span id="1.3.1">1.3.1 解压、创建软连接
    tar -zxvf mysql-5.6.39-linux-glibc2.12-x86_64.tar.gz -C /zz/app
    ln -s /zz/app/mysql-5.6.39-linux-glibc2.12-x86_64/ /zz/app/mysql
    ln -s /zz/app/mysql-5.6.39-linux-glibc2.12-x86_64/ /usr/local/mysql

#### <span id="1.3.2">1.3.2 配置
创建目录：

    mkdir /usr/local/mysql/logs
    mkdir /usr/local/mysql/arch
    mkdir /usr/local/mysql/backup
	mkdir /var/run/mysqld
	
配置：vim /etc/my.cnf

    [client]
    port= 3306
    socket  = /tmp/mysql.sock
    [mysqld]
    port= 3306
    datadir = /usr/local/mysql/data
    basedir = /usr/local/mysql
    log-error 	= /usr/local/mysql/logs
    pid-file	= /var/run/mysqld/mysqld.pid
    user		= mysqladmin
    socket  = /tmp/mysql.sock

#### <span id="1.3.3">1.3.3 更改目录权限
    chown mysqladmin:dba /etc/my.cnf
    chmod 640 /etc/my.cnf
    chown -R mysqladmin:dba /usr/local/mysql
    chown -R 755 /usr/local/mysql
	chown -R mysqladmin:dba /zz/app/mysql/
	chown -R mysqladmin:dba /var/run/mysqld

#### <span id="1.3.4">1.3.4 安装依赖包、安装mysql
	#root执行
    yum -y install libaio
	#mysqladmin执行
    scripts/mysql_install_db --user=mysqladmin --basedir=/zz/app/mysql --datadir=/zz/app/mysql/data 

#### <span id="1.3.5">1.3.5 命令的地方显示当前目录
/usr/local/mysql/为mysql的家目录

    cp /etc/skel/.* /usr/local/mysql/
    cd /usr/local/mysql/
    vim .bashrc
    MYSQL_BASE=/zz/app/mysql
    export MYSQL_BASE
    PATH=${MYSQL_BASE}/bin:$PATH
    export PATH
    unsest USERNAME
    set umask to 022
    umask 022
    PS1=`uname -n`":"'$USER'":"'$PWD'":>"; export PS1

更改前：
> -bash-4.1$

更改后：
> master:mysqladmin:/usr/local/mysql:>


#### <span id="1.3.6">1.3.6 删除配置文件
    rm -f /zz/app/mysql/my.cnf #删不删都行

#### <span id="1.3.7">1.3.7 查看进程、启动mysql
    ps -ef|grep mysql
	# mysqladmin执行
    mysqld_safe &
    netstat -nlp|grep 3306 #查看端口

#### <span id="1.3.8">1.3.8更改密码、删除空账号
    #mysql安装之后，会创建root(密码是空,要修改)和一个空账号(密码是空，要删除)
	#mysqladmin用户执行
    mysql -uroot -p #提示输入密码，直接回车回车
    show databases;
    use mysql;
    select user,password,host from user;
    update user set password=password('root') where user='root';
    select user,password,host from user;
    delete from user where user='';
    select user,password,host from user;
    flush privileges;


#### <span id="1.3.9">1.3.9 开机自启动
	#root用户执行
    cp support-files/mysql.server /etc/rc.d/init.d/mysql
    chmod +x /etc/rc.d/init.d/mysql
    chkconfig --add mysql
    chkconfig --level 345 mysql on 
    vim /etc/rc.local
    #加入下面命令
    su - mysqladmin -c "/etc/init.d/mysql start --federated"

#### 1.3.10 java连接驱动包下载啊
地址：https://dev.mysql.com/downloads/connector/  
选择：Connector/J

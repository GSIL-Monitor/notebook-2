# 1.linux绿色版mysql安装
## 1.1 下载下载绿色版安装包  
- 下载地址：[https://downloads.mysql.com/archives/community/](https://downloads.mysql.com/archives/community/) 
- system选择"linux - generic",然后下载对应的tar.gz包

## 1.2 解压、创建软连接
```
tar -zxvf mysql-5.6.39-linux-glibc2.12-x86_64.tar.gz -C /zz/app
ln -s /zz/app/mysql-5.6.39-linux-glibc2.12-x86_64/ /zz/app/mysql
ln -s /zz/app/mysql-5.6.39-linux-glibc2.12-x86_64/ /usr/local/mysql

#创建组合用户，用户家目录：/usr/local/mysql
groupadd -g 101 dba
useradd -u 514 -g dba -G root -d /usr/local/mysql mysqladmin
```
## 1.3 配置
- 创建目录：
  ```
   mkdir /usr/local/mysql/logs
   mkdir /usr/local/mysql/arch
   mkdir /usr/local/mysql/backup
   mkdir /var/run/mysqld
  ```
- 配置：vim /etc/my.cnf
  ```
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
  ```
## 1.4 更改目录权限
```
chown mysqladmin:dba /etc/my.cnf
chmod 640 /etc/my.cnf
chown -R mysqladmin:dba /usr/local/mysql
chown -R 755 /usr/local/mysql
chown -R mysqladmin:dba /zz/app/mysql/
chown -R mysqladmin:dba /var/run/mysqld
```
## 1.5 安装依赖包、安装mysql
	#root执行
	yum -y install libaio
	
	#mysqladmin执行
	scripts/mysql_install_db --user=mysqladmin --basedir=/zz/app/mysql --datadir=/zz/app/mysql/data

## 1.6 命令的地方显示当前目录
/usr/local/mysql/为mysql的家目录

    cp /etc/skel/.* /usr/local/mysql/
    cd /usr/local/mysql/
    
    #配置环境变量
    # vim .bashrc
    
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


## 1.8 删除配置文件
    rm -f /zz/app/mysql/my.cnf #删不删都行

## 1.9 查看进程、启动mysql
    ps -ef|grep mysql
    
    # mysqladmin执行
    mysqld_safe &
    netstat -nlp|grep 3306 #查看端口

## 1.10 更改密码、删除空账号
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

## 1.11 开机自启动
## 1.11 开机自启动
	#root用户执行
	cp support-files/mysql.server /etc/rc.d/init.d/mysql
	chmod +x /etc/rc.d/init.d/mysql
	chkconfig --add mysql
	chkconfig --level 345 mysql on 
	vim /etc/rc.local
	#加入下面命令
	su - mysqladmin -c "/etc/init.d/mysql start --federated"

## 1.12 允许远程连接myslq
```
#使用mysqladmin执行
su - mysqladmin
mysql -uroot -p

grant all on *.* TO 'root'@'%' IDENTIFIED BY 'root';
flush privileges;
```

# 2. window 绿色版mysql安装
## 2.1 下载绿色版mysql
- 下载地址：[https://downloads.mysql.com/archives/community/](https://downloads.mysql.com/archives/community/) 
- 下载参考：http://blog.csdn.net/anndy_/article/details/51093326
## 2.2 安装
- 安装参考：http://www.zuidaima.com/blog/3685035336600576.htm
- 解压，例如路径：D:\bigdata\tools\mysql-5.7.20-winx64
- 设置环境变量
  ```
  MYSQL_HOME
  D:\bigdata\tools\mysql-5.7.20-winx64

  path
  %MYSQL_HOME%\bin
  ```
- 在mysql目录下创建文件my.ini
  ```
  [client]
  port=3306
  default-character-set=utf8

  [mysql]
  # 设置mysql客户端默认字符集
  default-character-set=utf8

  [mysqld]
  #设置3306端口
  port = 3306

  # 设置mysql的安装目录
  basedir=D:\\bigdata\\tools\\mysql-5.7.20-winx64

  # 设置mysql数据库的数据的存放目录
  datadir=D:\\bigdata\\tools\\mysql-5.7.20-winx64\\data

  # 允许最大连接数
  max_connections=200

  # 服务端使用的字符集默认为8比特编码的latin1字符集
  character-set-server=utf8

  # 创建新表时将使用的默认存储引擎
  default-storage-engine=INNODB

  sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

  [WinMySQLAdmin]
  D:\bigdata\\tools\\mysql-5.7.20-winx64\\bin\\mysqld.exe
  ```
- 在mysql的实际安装目录下创建空的data文件夹
- 以管理员身份运行`cmd`进入`mysql-5.7.20\bin`目录下
  - 初始化数据库:执行`mysqld --initialize-insecure --user=mysql`进行初始化数据库操作
  - 安装mysql服务
    ```
    mysqld install MySQL --defaults-file="%MYSQL_HOME%\my.ini"
    ```
- mysql服务的启动与停止:
  - 启动: `net start mysql`
  - 停止: `net stop mysql`

- `mysql -uroot -p` 无密码进入
- 改密码：`SET PASSWORD = PASSWORD('密码');`
- 允许远程登录
  ```
  #使用mysqladmin执行
  mysql -uroot -p

  grant all on *.* TO 'root'@'%' IDENTIFIED BY 'root';
  flush privileges;
  ```
- 卸载mysql服务执行: `mysqld -remove` 或`sc delete mysql`即可卸载/删除服务

## 2.3 可能出现的问题
- 问题：`msvcp120.dll` 丢失
  处理：安装`vcredist_x86.exe`或者`vcredist_x64.exe`，根据自己的系统而定
- mysql数据库无法初始化
  - 参考：https://zhidao.baidu.com/question/395388025038751765.html
  ```
  mysqld: Can't create/write to file 'Digdata     ools\mysql-5.7.20-winx64\data\is_writable' (Errcode: 2 - No such file or directory)
  2018-03-18T15:52:28.119305Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
  2018-03-18T15:52:28.119305Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
  2018-03-18T15:52:28.119305Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
  2018-03-18T15:52:28.120302Z 0 [ERROR] Can't find error-message file 'D:\bigdata\tools\mysql-5.7.20-winx64\binigdata
  ools\mysql-5.7.20-winx64\share\errmsg.sys'. Check error-message file location and 'lc-messages-dir' configuration directive.
  2018-03-18T15:52:28.123295Z 0 [ERROR] --initialize specified but the data directory exists and is not writable. Aborting.
  2018-03-18T15:52:28.123801Z 0 [ERROR] Aborting
  ```
  处理：配置文件中的路径有问题，路径中应该是 `\\` 而不是 `\`

# 3. java连接驱动包下载
地址：https://dev.mysql.com/downloads/connector/  
选择：Connector/J


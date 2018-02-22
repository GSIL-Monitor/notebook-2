# 1. nginx安装
## 1.1 参考地址
- 参考地址
  nginx中文官网：[http://www.nginx.cn/doc/](http://www.nginx.cn/doc/)
  Nginx的安装目录详解: [http://blog.csdn.net/m0_37776094/article/details/78705718](http://blog.csdn.net/m0_37776094/article/details/78705718)

## 1.2 安装依赖
- 安装make
  `yum -y install gcc automake autoconf libtool make`
- 安装g++:
  `yum -y install gcc gcc-c++`

- 安装PCRE库
  ```
  cd /zz/app/nginx/src
  wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.41.tar.gz
  tar -zxvf pcre-8.41.tar.gz
  cd pcre-8.41/
  ./configure
  make
  make install
  ```

- 安装zlib库
  ```
  wget http://zlib.net/zlib-1.2.11.tar.gz
  tar -zxvf zlib-1.2.11.tar.gz
  cd zlib-1.2.11
  ./configure
  make
  make install
  ```

- 安装ssl（某些vps默认没装ssl)
  ```
  cd /usr/local/src
  wget https://www.openssl.org/source/openssl-1.0.2n.tar.gz
  tar -zxvf openssl-1.0.2n.tar.gz
  ```
## 1.3 安装nginx
- 安装nginx
  Nginx 一般有两个版本，分别是稳定版和开发版, 下面是把 Nginx 安装到 /usr/local/nginx   
  ```
  cd /zz/app/nginx/src
  wget http://nginx.org/download/nginx-1.9.9.tar.gz
  tar -zxvf nginx-1.9.9.tar.gz
  cd nginx-1.9.9

  ./configure \
  --prefix=/zz/app/nginx/nginx \
  --sbin-path=/zz/app/nginx/nginx/nginx \
  --conf-path=/zz/app/nginx/nginx/conf/nginx.conf \
  --http-log-path=/zz/app/nginx/nginx/logs/access.log \
  --error-log-path=/zz/app/nginx/nginx/logs/error.log \
  --pid-path=/zz/app/nginx/nginx/nginx.pid \
  --lock-path=/run/lock/subsys/nginx \
  --with-http_ssl_module \
  --with-pcre=/zz/app/nginx/pcre-8.41 \
  --with-zlib=/zz/app/nginx/zlib-1.2.11 \
  --with-openssl=/zz/app/nginx/openssl-1.0.2n

  make
  #make 有问题了，可以 到你的源码目录内，先make clean  然后./config ....
  make install
  ```

- 产看版本
  `/zz/app/nginx/nginx/nginx -v`

## 1.4 创建nginx用户
  ```
  /usr/sbin/groupadd nginx
  /usr/sbin/useradd nginx
  ```

## 1.5 修改配置文件
  [nginx.conf](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/nginx/nginx.conf)  
  [es-pugin.conf](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/nginx/es-plugin.conf)  
## 1.6 nginx命令
  ```
nginx -t #检查配置文件正确性
nginx -s reload # 重新载入配置文件
nginx -s reopen# 重启 Nginx
nginx -s stop# 停止 Nginx
nginx -v# 查看nginx版本
netstat -antlp | grep 80#nginx占用80端口，检查是否启动
ps -ef | grep nginx	#命令ps查找nginx的主进程号，检查是否启动(假设主进程号为3514)
kill -QUIT 3514	#从容停止
kill -TERM 3514	#快速停止
kill -9 3514	# 强制停止，只关闭一个主进程号，其余进程号仍在运行
kill -9 3514 3515 3525# 强制关闭nginx所有进程号
kill -HUP 3514	#平滑重启
  ```

## 1.7 启动
确保系统的 80 端口没被其他程序占用，运行`/zz/app/nginx/nginx/nginx` 命令来启动 Nginx  
`netstat -ano|grep 80`
如果查不到结果后执行，有结果则忽略此步骤（ubuntu下必须用sudo启动，不然只能在前台运行）
`sudo /zz/app/nginx/nginx/nginx/nginx`

## 1.8 验证
打开浏览器访问此机器的 IP，如果浏览器出现 Welcome to nginx! 则表示 Nginx 已经安装并运行成功。  
到这里nginx就安装完成了，如果只是处理静态html就不用继续安装了  
如果你需要处理php脚本的话，还需要安装php-fpm  

## 1.9 nginx开机自启
- 参考网址  
  [http://blog.csdn.net/u013870094/article/details/52463026](http://blog.csdn.net/u013870094/article/details/52463026)
- 添加启动脚本  
  `vim /etc/init.d/nginx`  
  > 脚本地址：[nginx](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/nginx/nginx)
  > 参考官网：[http://wiki.nginx.org/RedHatNginxInitScript](http://wiki.nginx.org/RedHatNginxInitScript)

- 修改以下2个配置：  
  ```
  nginx="/usr/sbin/nginx" 修改成nginx执行程序的路径。 
  NGINX_CONF_FILE="/etc/nginx/nginx.conf" 修改成配置文件的路径
  ```

- 保存脚本文件后设置文件的执行权限  
  `chmod a+x /etc/init.d/nginx`  

- 然后，就可以通过该脚本对nginx服务进行管理了  
  ```
  /etc/init.d/nginx start
  /etc/init.d/nginx stop
  ```

- 使用chkconfig进行管理  
  `chkconfig --add /etc/init.d/nginx`  

- 加完这个之后，就可以使用service对nginx进行启动，重启等操作了  
  ```
  service nginx start
  service nginx stop
  ```

- 设置终端模式开机启动  
  `chkconfig nginx on`  

# 2. 异常
## 2.1 make出错
- 异常
  ```
  make: *** No rule to make target `build', needed by `default'.  Stop.
  ./configure: error: SSL modules require the OpenSSL library.
  You can either do not enable the modules, or install the OpenSSL library
  into the system, or build the OpenSSL library statically from the source
  with nginx by using --with-openssl=<path> option
  ```
- 处理：
  `yum -y install openssl openssl-devel`

## 2.2 make时出错
- 问题
  ```
  cc1: warnings being treated as errors
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_init’:
  src/event/ngx_event_openssl.c:112: error: ‘OPENSSL_config’ is deprecated (declared at /zz/app/nginx/openssl-1.1.0g/.openssl/include/openssl/conf.h:92)
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_rsa512_key_callback’:
  src/event/ngx_event_openssl.c:753: error: ‘RSA_generate_key’ is deprecated (declared at /zz/app/nginx/openssl-1.1.0g/.openssl/include/openssl/rsa.h:193)
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_dhparam’:
  src/event/ngx_event_openssl.c:943: error: dereferencing pointer to incomplete type
  src/event/ngx_event_openssl.c:944: error: dereferencing pointer to incomplete type
  src/event/ngx_event_openssl.c:946: error: dereferencing pointer to incomplete type
  src/event/ngx_event_openssl.c:946: error: dereferencing pointer to incomplete type
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_connection_error’:
  src/event/ngx_event_openssl.c:1917: error: ‘SSL_R_NO_CIPHERS_PASSED’ undeclared (first use in this function)
  src/event/ngx_event_openssl.c:1917: error: (Each undeclared identifier is reported only once
  src/event/ngx_event_openssl.c:1917: error: for each function it appears in.)
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_session_cache’:
  src/event/ngx_event_openssl.c:2111: error: passing argument 2 of ‘SSL_CTX_sess_set_get_cb’ from incompatible pointer type
  /zz/app/nginx/openssl-1.1.0g/.openssl/include/openssl/ssl.h:639: note: expected ‘struct SSL_SESSION * (*)(struct ssl_st *, const unsigned char *, int,  int *)’ but argument is of type ‘struct SSL_SESSION * (*)(struct SSL *, u_char *, int,  int *)’
  src/event/ngx_event_openssl.c: In function ‘ngx_ssl_session_id_context’:
  src/event/ngx_event_openssl.c:2133: error: storage size of ‘md’ isn’t known
  src/event/ngx_event_openssl.c:2199: error: implicit declaration of function ‘EVP_MD_CTX_cleanup’
  src/event/ngx_event_openssl.c:2133: error: unused variable ‘md’
  make[1]: *** [objs/src/event/ngx_event_openssl.o] Error 1
  make[1]: Leaving directory `/zz/app/nginx/nginx-1.9.9'
  make: *** [build] Error 2
  ```
- 处理
  更改openssl-1.1.0g 为1.0.2，版本太高




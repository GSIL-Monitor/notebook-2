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
  cd /zz/app/nginx/src
  wget http://zlib.net/zlib-1.2.11.tar.gz
  tar -zxvf zlib-1.2.11.tar.gz
  cd zlib-1.2.11
  ./configure
  make
  make install
  ```

- 安装ssl（某些vps默认没装ssl)
  ```
  cd /zz/app/nginx/src/
  wget https://www.openssl.org/source/openssl-1.0.2n.tar.gz
  tar -zxvf openssl-1.0.2n.tar.gz
  ```
## 1.3 安装nginx
- 安装nginx
  Nginx 一般有两个版本，分别是稳定版和开发版, 下面是把 Nginx 安装到 /zz/app/nginx/nginx   
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
  --with-pcre=/zz/app/nginx/src/pcre-8.41 \
  --with-zlib=/zz/app/nginx/src/zlib-1.2.11 \
  --with-openssl=/zz/app/nginx/src/openssl-1.0.2n

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
  ```
  user nginx nginx;
  worker_processes  12;
  	
  events {
  	
  	#单个后台worker process进程的最大并发链接数
  	worker_connections   65536;
  
  	#epoll是多路复用IO(I/O Multiplexing)中的一种方式,
  	#仅用于linux2.6以上内核,可以大大提高nginx的性能
  	use epoll;
  	
  	#默认是on。设置为on后，多个worker按串行方式来处理连接，也就是一个连接只有一个worker被唤醒，其他的处于休眠状态。
  	#设置为off后，多个worker按并行方式来处理连接，也就是一个连接会唤醒所有的worker，知道连接分配完毕，没有取得连接的继续休眠。
  	#当你的服务器连接数不多时，开启这个参数会让负载有一定程度的降低。但是当服务器的吞吐量很大时，为了效率，请关闭这个参数。
  	multi_accept on;
  }
  
  http {
  	include       mime.types;
  	default_type  application/octet-stream;
  	
  	keepalive_timeout 65;
  	keepalive_requests 100000;
  	sendfile         on;
  	client_body_buffer_size    128k;
  	client_max_body_size       100m;
  	client_header_buffer_size    1k;
  	large_client_header_buffers  4 4k;
  	output_buffers   1 32k;
  	postpone_output  1460;
  	client_header_timeout  3m;
  	client_body_timeout    3m;
  	send_timeout           3m;
  	gzip on;
  	gzip_min_length  1000;
  	gzip_buffers     4 4k;
  
  	include /zz/app/nginx/nginx/conf/*.conf;
  }
  ```
  [es-pugin.conf](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/nginx/es-plugin.conf) 
  ```
#设定日志格式
log_format  main  '$remote_addr | [$time_iso8601] | "$request" | ' '$status | $body_bytes_sent | "$http_referer"  | ' '"$http_user_agent" | "$http_x_forwarded_for" |  $request_body';

server {
	listen 80;
	server_name master;
	location / {
		proxy_pass http://192.168.229.129:5601;
		proxy_http_version 1.1;
		proxy_set_header Connection "";
		proxy_connect_timeout 600s;
		proxy_read_timeout 600s;
		proxy_send_timeout 600s;
		access_log /zz/app/nginx/nginx/logs/kibana.access.log main;
	}
	location /plugins {
		proxy_pass http://192.168.229.129:5601/plugins;
		proxy_http_version 1.1;
		proxy_set_header Connection "";
		proxy_connect_timeout 600s;
		proxy_read_timeout 600s;
		proxy_send_timeout 600s;
		access_log /zz/app/nginx/nginx/logs/kibana.access.log main;
	}

	location /plugin {
		root   html;
		index  index.html index.htm;
		access_log /zz/app/nginx/nginx/logs/plugin.access.log main;
		#lua_need_request_body on;
	}

	location /download {
		root   /zz/soft/;
		index  index.html index.htm;
		access_log /zz/app/nginx/nginx/logs/download.access.log combined;
	}

	error_page   500 502 503 504  /50x.html;

	location = /50x.html {
		root   html;
	}
}
  ```
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

## 2.3 用户不可用
- 异常信息
  ```
  [root@work1 nginx]# ./nginx -t         
  nginx: [emerg] "user" directive is not allowed here in /zz/app/nginx/nginx/conf/nginx.conf:1
  nginx: configuration file /zz/app/nginx/nginx/conf/nginx.conf test failed
  ```
- 处理
  `include /zz/app/nginx/nginx/conf/*.conf;` 改为 `include /zz/app/nginx/nginx/conf/myconf/*.conf;`
  配置文件包含多了，自定义的配置文件单独放到一个文件夹myconf里

# 3. 高可用keepalived
## 3.1 安装keepalived
- 下载源码，编译
  ```
  cd /zz/app/nginx/src
  wget http://www.keepalived.org/software/keepalived-1.4.2.tar.gz
  cd keepalived-1.4.2/

  #开始安装
  ./configure --prefix=/usr/local/keepalived
  make
  make install
  ```

- 制作自启脚本
  `vim /etc/init.d/keepalived`
  注意 exec 、 prog 、config
  参考地址：http://blog.csdn.net/libaineu2004/article/details/79551455

  ```
  #!/bin/sh   
  #   
  # keepalived   High Availability monitor built upon LVS and VRRP   
  #   
  # chkconfig:   - 86 14   
  # description: Robust keepalive facility to the Linux Virtual Server project \   
  #              with multilayer TCP/IP stack checks.   
       
  ### BEGIN INIT INFO   
  # Provides: keepalived   
  # Required-Start: $local_fs $network $named $syslog   
  # Required-Stop: $local_fs $network $named $syslog   
  # Should-Start: smtpdaemon httpd   
  # Should-Stop: smtpdaemon httpd   
  # Default-Start:    
  # Default-Stop: 0 1 2 3 4 5 6   
  # Short-Description: High Availability monitor built upon LVS and VRRP   
  # Description:       Robust keepalive facility to the Linux Virtual Server   
  #                    project with multilayer TCP/IP stack checks.   
  ### END INIT INFO   
       
  # Source function library.   
  . /etc/rc.d/init.d/functions   
    
  exec="/usr/sbin/keepalived"
  prog="keepalived"  
  config="/etc/keepalived/keepalived.conf"  
       
  [ -e /usr/local/etc/sysconfig/$prog ] &&  /usr/local/etc/sysconfig/$prog   
    
  lockfile=/var/lock/subsys/keepalived   
    
  start() {   
      [ -x $exec ] || exit 5   
      [ -e $config ] || exit 6   
      echo -n $"Starting $prog: "   
      daemon $exec $KEEPALIVED_OPTIONS  
      retval=$?   
      echo   
      [ $retval -eq 0 ] && touch $lockfile   
      return $retval   
  }   
       
  stop() {   
      echo -n $"Stopping $prog: "   
      killproc $prog   
      retval=$?   
      echo   
      [ $retval -eq 0 ] && rm -f $lockfile   
      return $retval   
  }   
       
  restart() {   
      stop   
      start   
  }   
       
  reload() {   
      echo -n $"Reloading $prog: "   
      killproc $prog -1   
      retval=$?   
      echo   
      return $retval   
  }   
       
  force_reload() {   
      restart   
  }   
       
  rh_status() {   
      status $prog   
  }   
       
  rh_status_q() {   
      rh_status &>/dev/null   
  }   
       
       
  case "$1" in   
      start)   
          rh_status_q && exit 0   
          $1   
          ;;   
      stop)   
          rh_status_q || exit 0   
          $1   
          ;;   
      restart)   
          $1   
          ;;   
      reload)   
          rh_status_q || exit 7   
          $1   
          ;;   
      force-reload)   
          force_reload   
          ;;   
      status)   
          rh_status   
          ;;   
      condrestart|try-restart)   
          rh_status_q || exit 0   
          restart   
          ;;   
      *)   
          echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload}"   
          exit 2   
  esac   
  exit $?
  ```

- 复制配置、添加执行权限

  ```
  cp /usr/local/keepalived/sbin/keepalived /usr/sbin/
  cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
  mkdir -p /etc/keepalived
  cp /usr/local/keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf

  chmod a+x /etc/init.d/keepalived
  chmod a+x /etc/sysconfig/keepalived
  chmod a+x /usr/sbin/keepalived
  ```

- 开机自启

  ```
  chkconfig --add keepalived
  chkconfig keepalived on
  ```

- 修改配置文件（master）
  vim /etc/keepalived/keepalived.conf
  ```
  global_defs { #全局配置
  	notification_email {
  		zhangzhengstrive@163.com	#定义通知邮箱
  	}
  	notification_email_from zhangzhengstrive@163.com # 从哪个邮箱发送
  	smtp_server 127.0.0.1
  	smtp_connect_timeout 30
  	router_id  master1		# 标识本节点的字条串,通常为hostname
  }

  vrrp_script chk_health {
  	script "/zz/shell/keepalived/check_nginx.sh" #定义的检查脚本
  	interval 1    			#每隔1秒执行上述的脚本，去检查用户的程序ngnix
  	weight -2 				#每挂一次，惩罚自己一次-2分
  }

  vrrp_instance VI_1 {
  	state MASTER			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 1
  	priority 100			#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 1111
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.188			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }
  ```

- 修改配置文件（backup节点）
  vim /etc/keepalived/keepalived.conf
  ```
  global_defs { #全局配置
  	notification_email {
  		zhangzhengstrive@163.com	#定义通知邮箱
  	}
  	notification_email_from zhangzhengstrive@163.com # 从哪个邮箱发送
  	smtp_server 127.0.0.1
  	smtp_connect_timeout 30
  	router_id  master1		# 标识本节点的字条串,通常为hostname
  }

  vrrp_script chk_health {
  	script "/zz/shell/keepalived/check_nginx.sh" #定义的检查脚本
  	interval 1    			#每隔1秒执行上述的脚本，去检查用户的程序ngnix
  	weight -2 				#每挂一次，惩罚自己一次-2分
  }

  vrrp_instance VI_1 {
  	state BACKUP			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 1
  	priority 99			#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送	间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 1111
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.188			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }
  ```

- 监控nginx脚本
  mkdir -p /zz/shell/keepalived
  vim /zz/shell/keepalived/check_nginx.sh 
  ```
  killall -0 nginx	#发一个检测信号，不是真的kill
  if [[ $? -eq 0 ]];then
  	/etc/init.d/keepalived stop
  fi
  ```

- 添加keepalived_script用户(暂时不添加，添加后出现新下面的第三个异常，可能是权限问题，又删除了用户和组)
  1.3后添加了用于验证：http://old.gebiji.com/2016/12/21/ka-warning-default-user/

  ```
  groupadd keepalived_script
  useradd keepalived_script -g keepalived_script
  ```

- 启动
  `/etc/init.d/keepalived start` 或者 `service keepalived start`
  启动后，可以通过以下命令来查看是否安装正确，启动后有3个keepalive进程表示正确
  `ps -ef|grep keep|grep -v grep`

`ip add|grep`
ifconfig只能查看物理网卡，ip addr或者ip a才能看见虚拟ip，172.16.6.161是真实ip，172.16.6.200是虚拟ip

- 停止
  `/etc/init.d/keepalived stop` 或者 `service keepalived stop`

- keepalived日志目录默认：/var/log/messages
  参考：https://www.cnblogs.com/zzzhfo/p/6070575.html


## 3.2 keepalived的双主架构
- master节点配置
  ```
  global_defs { #全局配置
  	notification_email {
  		zhangzhengstrive@163.com	#定义通知邮箱
  	}
  	notification_email_from zhangzhengstrive@163.com # 从哪个邮箱发送
  	smtp_server 127.0.0.1
  	smtp_connect_timeout 30
  	router_id  master1		# 标识本节点的字条串,通常为hostname
  }

  vrrp_script chk_health {
  	script "/zz/shell/keepalived/check_nginx.sh" #定义的检查脚本
  	interval 1    			#每隔1秒执行上述的脚本，去检查用户的程序ngnix
  	weight -2 				#每挂一次，惩罚自己一次-2分
  }

  #VIP1
  vrrp_instance VI_1 {
  	state MASTER			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 1
  	priority 100			#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 1111
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.188			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }

  #VIP2，和VIP1中的 virtual_router_id 、 auth_pass 、 priority 、 virtual_ipaddress 不一样
  vrrp_instance VI_2 {
  	state BACKUP			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 2		#路由id
  	priority 90				#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 2222
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.189			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }
  ```
- backup节点配置
  ```
  global_defs { #全局配置
  	notification_email {
  		zhangzhengstrive@163.com	#定义通知邮箱
  	}
  	notification_email_from zhangzhengstrive@163.com # 从哪个邮箱发送
  	smtp_server 127.0.0.1
  	smtp_connect_timeout 30
  	router_id  master1		# 标识本节点的字条串,通常为hostname
  }

  vrrp_script chk_health {
  	script "/zz/shell/keepalived/check_nginx.sh" #定义的检查脚本
  	interval 1    			#每隔1秒执行上述的脚本，去检查用户的程序ngnix
  	weight -2 				#每挂一次，惩罚自己一次-2分
  }

  #VIP1
  vrrp_instance VI_1 {
  	state BACKUP			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 1
  	priority 99			#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送	间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 1111
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.188			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }

  #VIP2，和VIP1中的 virtual_router_id 、 auth_pass 、 priority 不一样
  vrrp_instance VI_2 {
  	state MASTER			#指定A节点为主节点 备用节点上设置为BACKUP即可
  	interface eth0			#绑定虚拟IP的网络接口
  	virtual_router_id 2		#路由id
  	priority 91				#主节点的优先级（1-254之间），备用节点必须比主节点优先级低
  	advert_int 2			#组播信息发送间隔，两个节点设置必须一样
  	nopreempt #不抢占
  	authentication {		#认证
  		auth_type PASS
  		auth_pass 2222
  	}
  	
  	track_script {
  		chk_health #执行这个脚本
  	}
  	
  	virtual_ipaddress {
  		192.168.229.189			#指定虚拟IP, 两个节点设置必须一样
  	}
  	
  	#notify_master "/usr/local/keepalived/sbin/notify.sh master" #backup挂了，自己要变成master
  	#notify_backup "/usr/local/keepalived/sbin/notify.sh backup" #自己要变成backup
  	#notify_fault "/usr/local/keepalived/sbin/notify.sh fault"
  }
  ```
- 用户访问
  可以通过访问 192.168.229.188 和 192.168.229.189，访问nginx

- 2个ip用户怎么访问？
  域名映射到2个ip，域名dns负载均衡的方式，之后访问这个域名

## 3.3 异常
- 异常：编译keepalived时候出现'aclocal-1.15' is missing on your system
  处理：`autoreconf -f -i.` ，然后再重新执行./configure --prefix=/usr/local/keepalived

- 异常：
  configure: error: 
  !!! OpenSSL is not properly installed on your system. !!!
  !!! Can not include OpenSSL headers files.            !!!
  处理：`yum -y install openssl-devel`

- 异常
  ```
  Mar 18 17:07:05 work1 Keepalived_healthcheckers[47167]: Opening file '/etc/keepalived/keepalived.conf'.
  Mar 18 17:07:05 work1 Keepalived_vrrp[47168]: VRRP_Instance(VI_1) Entering BACKUP STATE
  Mar 18 17:07:06 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  Mar 18 17:07:06 work1 Keepalived_vrrp[47168]: VRRP_Script(chk_health) timed_out
  Mar 18 17:07:08 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  Mar 18 17:07:10 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  Mar 18 17:07:12 work1 Keepalived_vrrp[47168]: VRRP_Instance(VI_1) Transition to MASTER STATE
  Mar 18 17:07:12 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  Mar 18 17:07:14 work1 Keepalived_vrrp[47168]: VRRP_Instance(VI_1) Entering MASTER STATE
  Mar 18 17:07:14 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  Mar 18 17:07:16 work1 Keepalived_vrrp[47168]: Track script chk_health is being timed out, expect idle - skipping run
  ```
  处理：可能是权限的问题
  ```
  groupdel keepalived_script
  userdel keepalived_script
  ```
# 1. rsyslog安装：
yum -y install rsyslog rsyslog-kafka rsyslog-mmjsonparse json-c rsyslog-elasticsearch

# 2. 配置
## 2.1 rsyslog采集文件入kafka
- 入kafka官方文档：
  Configuration --》 Modules  --》 Output Modules --》 omkafka: write to Apache Kafka
  http://www.rsyslog.com/doc/v8-stable/configuration/modules/omkafka.html
- 配置（版本：8.25）
  ```
  # rsyslog v5 configuration file

  # For more information see /usr/share/doc/rsyslog-*/rsyslog_conf.html
  # If you experience problems, see http://www.rsyslog.com/doc/troubleshoot.html

  #### MODULES ####
  $MaxMessageSize 20k

  $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
  $ModLoad imklog   # provides kernel logging support (previously done by rklogd)
  #$ModLoad immark  # provides --MARK-- message capability
  module(load="omkafka")
  module(load="omelasticsearch")
  module(load="mmjsonparse")

  # Provides UDP syslog reception
  #$ModLoad imudp
  #$UDPServerRun 514

  # Provides TCP syslog reception
  #$ModLoad imtcp
  #$InputTCPServerRun 514


  #### GLOBAL DIRECTIVES ####

  # Use default timestamp format
  $ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat

  # File syncing capability is disabled by default. This feature is usually not required,
  # not useful and an extreme performance hit
  #$ActionFileEnableSync on

  # Include all config files in /etc/rsyslog.d/
  $IncludeConfig /etc/rsyslog.d/*.conf


  #### RULES ####

  # Log all kernel messages to the console.
  # Logging much else clutters up the screen.
  #kern.*                                                 /dev/console
  kern.*                                                 /var/log/messages
  *.info;mail.none;authpriv.none;cron.none;local6.none;local5.none                /var/log/messages

  # Log anything (except mail) of level info or higher.
  # Don't log private authentication messages!

  # The authpriv file has restricted access.
  authpriv.*                                              /var/log/secure

  # Log all the mail messages in one place.
  mail.*                                                  -/var/log/maillog


  # Log cron stuff
  cron.*                                                  /var/log/cron


  # Save news errors of level crit and higher in a special file.
  uucp,news.crit                                          /var/log/spooler

  # Save boot messages also to boot.log
  local7.*                                                /var/log/boot.log


  # ### begin forwarding rule ###
  # The statement between the begin ... end define a SINGLE forwarding
  # rule. They belong together, do NOT split them. If you create multiple
  # forwarding rules, duplicate the whole block!
  # Remote Logging (we use TCP for reliable delivery)
  #
  # An on-disk queue is created for this action. If the remote host is
  # down, messages are spooled to disk and sent when it is up again.
  #$WorkDirectory /var/lib/rsyslog # where to place spool files
  #$ActionQueueFileName fwdRule1 # unique name prefix for spool files
  #$ActionQueueMaxDiskSpace 1g   # 1gb space limit (use as much as possible)
  #$ActionQueueSaveOnShutdown on # save messages to disk on shutdown
  #$ActionQueueType LinkedList   # run asynchronously
  #$ActionResumeRetryCount -1    # infinite retries if host is down
  # remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
  #*.* @@remote-host:514
  # ### end of the forwarding rule ###
  $template strtpl,"%TIMESTAMP:::date-pgsql% %msg%\n"
  local6.info                /var/log/history;strtpl
  #nginx accesslog
  $WorkDirectory /var/spool/rsyslog
  module(load="imfile" mode="inotify")
  input(type="imfile"
        file="/bbd/logs/20*_user_access.log"
        Tag="nginx-access:"
        Facility="local5"
        Severity="err"
        PersistStateInterval="200")
  $template FormatNginx,"%msg%\n"
  local5.err action(type="omkafka" 
  		topic="nginx-access" 
  		broker=["x.x.x.x:9092","x.x.x.x:9092","x.x.x.x:9092","x.x.x.x:9092","x.x.x.x:9092"] 
  		partitions.number="60" errorFile="/bbd/logs/rsyslog/kafkaerr.log" 
  		template="FormatNginx" 
  		confParam=["compression.codec=snappy","socket.timeout.ms=5000","socket.keepalive.enable=true"] 
  		queue.spoolDirectory="/bbd/logs/rsyslog" #指定队列文件的路径
  		queue.type="Disk"				#队列的方式  [FixedArray/LinkedList/Direct/Disk]
  		queue.checkpointinterval="500"	#检查点
  		queue.size="16777216"			#队列的最大大小（应该是条数）
  		queue.filename="toKafka" 		#队列文件的文件名，只是文件名，不能通过此参数指定路径（指定路径：queue.spoolDirectory）
  		queue.highwatermark="10066330" 	#当队列消息数量查过此值脱机到磁盘，此值要在size的60%上
  		queue.lowwatermark="5033165" 	#number default 2000
  		queue.maxdiskspace="20g" 		#所有队列在磁盘上使用的最大大小
  		queue.maxfilesize="1g" 			#size_nbr default 1m
  		queue.dequeuebatchsize="500" 	#number default 128
  		queue.saveonshutdown="on" 		#on/off
  		queue.workerThreads="4" 		#number of worker threads, default 1, recommended 1
  		queue.discardseverity="8" 		#number *numerical* severity! default 8 (nothing discarded)
  		queue.discardmark="16000000" 	#number default 9750
  		queue.timeoutenqueue="5000"     #默认是2000毫秒，0表示立即丢弃，（我的理解是当队列满的时候，超过这个时间未被处理会被丢弃）
  		action.resumeRetryCount="5"     #integer [default 0, -1 means eternal]，重试，-1会一直重试
  		action.resumeInterval="5"       #
  		action.reportSuspension="on"    #
  		action.reportSuspensionContinuation="on")
  #local5.err action(type="omkafka" topic="nginx-access" broker=["kafka1.bbdcdn.net:9092","kafka2.bbdcdn.net:9092","kafka3.bbdcdn.net:9092","kafka2.bbdcdn.net:9092","kafka3.bbdcdn.net:9092","kafka1.bbdcdn.net:9092","kafka3.bbdcdn.net:9092","kafka2.bbdcdn.net:9092","kafka1.bbdcdn.net:9092"] partitions.number="9" errorFile="/bbd/logs/rsyslog/kafkaerr.log" template="FormatNginx" confParam=["compression.codec=snappy","socket.timeout.ms=5000","socket.keepalive.enable=true"] queue.spoolDirectory="/bbd/logs/rsyslog" queue.type="LinkedList" queue.size="16777216" queue.filename="toKafka" queue.highwatermark="10066330" queue.lowwatermark="5033165" queue.maxdiskspace="20g" queue.maxfilesize="1g" queue.dequeuebatchsize="500" queue.saveonshutdown="on" queue.workerThreads="4" queue.discardseverity="8" queue.discardmark="16000000" queue.timeoutenqueue="5000")
  if $programname == 'nginx-access' then stop

  ##### Purge&preload log #####
  template(name="timegeneratedrfc3339" type="string"
          string="%timegenerated:::date-rfc3339%")
  set $!@timestamp = exec_template("timegeneratedrfc3339");
  *.* :mmjsonparse:
  #template(name="json-to-elk" type="string" string="%$!all-json-plain%" )
  #'{ "datetime": "2016-10-15 16:55:30", "task_id" : "5638", "log_level": "error", "task_type": "inject", "hostname": "c3n-he-sjz1-121-28-195-162", "description": "error log" }'
  template(name="format-json" type="list") {  # Elasticsearch documents will contain
      constant(value="{\"datetime\":\"")
      property(name="$!datetime")
      constant(value="\",\"task_id\":\"")
      property(name="$!task_id")
      constant(value="\",\"log_level\":\"")
      property(name="$!log_level")
      constant(value="\",\"task_type\":\"")
      property(name="$!task_type")
      constant(value="\",\"hostname\":\"")
      property(name="$!hostname")
      constant(value="\",\"description\":\"")
      property(name="$!description")
      constant(value="\"}")
  }
  #$template index,"devops-%timereported:1:10:date-rfc3339%"
  $template index,"devops-%timereported:1:10:date-year%-%timereported:1:10:date-month%"

  local0.err action(type="omelasticsearch"
         server="x.x.x.x"
         serverport="9200"
         template="format-json"
         searchIndex="index"
         searchType="devops"
         bulkmode="on"
         dynSearchIndex="on"
         queue.type="linkedlist"
         queue.size="5000"
         errorFile="/bbd/logs/rsyslog/eserror.log"
         queue.dequeuebatchsize="300"
         action.resumeretrycount="-1")
  ```




# 3. rsyslog异常
## 3.1 版本过高异常
rsyslog版本过高异常
![](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/rsyslog/rsyslog%E7%89%88%E6%9C%AC%E8%BF%87%E9%AB%98%E5%BC%82%E5%B8%B8.png?raw=true)
处理：改为较低版本的rsyslog
# 1 安装
## 1.1 下载war包
- 下载地址：[https://jenkins.io/doc/book/installing/](https://jenkins.io/doc/book/installing/)

## 1.2 用java启动
```
nohup java -Dcom.sun.akuma.Daemon=daemonized -Djava.awt.headless=true -DJENKINS_HOME=/var/lib/jenkins -jar /usr/lib/jenkins/jenkins.war --logfile=/var/log/jenkins/jenkins.log --webroot=/var/cache/jenkins/war --daemon --httpPort=9080 --debug=5 --handlerCountMax=100 --handlerCountMaxIdle=20 &
```

# 2 使用ssh执行任务
## 2.1 安装插件
系统管理 -> 管理插件 -> 可选插件 -> (安装：Publish Over SSH, 安装：SSH plugin)
## 2.2 配置
- `cp ~/.ssh/id_rsa /zz/app/jenkins/`
- 系统管理 -> 系统管理 -> Publish over SSH #添加Path to key(/zz/app/jenkins/id_rsa) 和 Key(id_rsa的内容)
- Credentials -> System -> Global credentials (unrestricted) -> Add Credentials #添加执行ssh的用户信息
  参考：[jenkins ssh remote hosts如何设置?](https://www.zhihu.com/question/63573975)
- 系统管理 -> 系统管理 -> SSH remote hosts #开始添加服务器，Credentials选择刚添加的Credentials

## 2.3 定时删除任务执行日志
```
find /zz/app/jenkins/jobs/*/builds/* -mtime +7 -type d | xargs rm -rf
```
有的文件可能太多，可以循环删除
```
OLD_IFS="$IFS"
IFS=$'\n' 
for i in `ls /zz/app/jenkins/jobs/`;do find /zz/app/jenkins/jobs/$i/builds/* -mtime +7 -type d | xargs rm -rf; done
IFS=$OLD_IFS
```
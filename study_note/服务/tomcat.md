tomcat：  
环境变量  
tomcat环境配置：
```
CATALINA_BASE
E:\java\apache-tomcat-7.0.64
TOMECATE_HOME
E:\java\apache-tomcat-7.0.64
path
%TOMECATE_HOME %\lib;%TOMECATE_HOME %\bin
```

用户配置（tomcat-users.xml）：
```xml
<role rolename="manager-gui"/>
<user username="admin" password="admin" roles="manager-gui"/>
```

部署项目配置（server.xml）：
```xml
<Context path="/javawebtest1" docBase="D:\study\company\ZGRS\code\test\src\main\webapp" debug="0" privileged="true" reloadable="true"></Context>
```

启动：D:\bigdata\tools_free\tomcat\apache-tomcat-7.0.88\bin\startup.bat  
停止：D:\bigdata\tools_free\tomcat\apache-tomcat-7.0.88\bin\shutdown.bat  

Tomcat下web项目部署方式：
[https://blog.csdn.net/jq_ak47/article/details/70667608](https://blog.csdn.net/jq_ak47/article/details/70667608)

idea tomcat插件 - Smart Tomcat：
[http://plugins.jetbrains.com/plugin/9492-smart-tomcat](http://plugins.jetbrains.com/plugin/9492-smart-tomcat)




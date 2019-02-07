# 一、下载安装：

## 1. 地址

- java官网下载地址
  http://www.oracle.com/technetwork/java/javase/archive-139210.html

- scala官网下载地址(scala不分32和64位)
  http://www.scala-lang.org/download/all.html

- scalaIDE（eclipse）官网下载地址：
  http://scala-ide.org/download/sdk.html

- IntelliJ IDEA官网下载地址：
  http://www.jetbrains.com/idea/

- 指定版本的下载

  - 2.11.12

    | Archive                                                      | System                  | Size    |
    | ------------------------------------------------------------ | ----------------------- | ------- |
    | [scala-2.11.12.tgz](https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.tgz) | Mac OS X, Unix, Cygwin  | 27.77M  |
    | [scala-2.11.12.msi](https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.msi) | Windows (msi installer) | 109.82M |
    | [scala-2.11.12.zip](https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.zip) | Windows                 | 27.82M  |
    | [scala-2.11.12.deb](https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.deb) | Debian                  | 76.44M  |
    | [scala-2.11.12.rpm](https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.rpm) | RPM package             | 108.60M |
    | [scala-docs-2.11.12.txz](https://downloads.lightbend.com/scala/2.11.12/scala-docs-2.11.12.txz) | API docs                | 46.17M  |
    | [scala-docs-2.11.12.zip](https://downloads.lightbend.com/scala/2.11.12/scala-docs-2.11.12.zip) | API docs                | 84.26M  |
    | [scala-sources-2.11.12.tar.gz](https://github.com/scala/scala/archive/v2.11.12.tar.gz) | Sources                 |         |

  - 2.12.8

    | Archive                                                      | System                  | Size    |
    | ------------------------------------------------------------ | ----------------------- | ------- |
    | [scala-2.12.8.tgz](https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.tgz) | Mac OS X, Unix, Cygwin  | 19.52M  |
    | [scala-2.12.8.msi](https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.msi) | Windows (msi installer) | 123.96M |
    | [scala-2.12.8.zip](https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.zip) | Windows                 | 19.56M  |
    | [scala-2.12.8.deb](https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.deb) | Debian                  | 144.40M |
    | [scala-2.12.8.rpm](https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.rpm) | RPM package             | 124.27M |
    | [scala-docs-2.12.8.txz](https://downloads.lightbend.com/scala/2.12.8/scala-docs-2.12.8.txz) | API docs                | 53.21M  |
    | [scala-docs-2.12.8.zip](https://downloads.lightbend.com/scala/2.12.8/scala-docs-2.12.8.zip) | API docs                | 107.53M |
    | [scala-sources-2.12.8.tar.gz](https://github.com/scala/scala/archive/v2.12.8.tar.gz) | Sources                 |         |

## 2. 环境变量

- window中

  ```
  SCALA_HOME
  E:\scala\scala-2.11.7
  
  path
  %SCALA_HOME%\bin;%SCALA_HOME%\jre\bin;
  
  ClassPath
  %SCALA_HOME%\bin;%SCALA_HOME%\lib\dt.jar;%SCALA_HOME%\lib\tools.jar;
  ```

  > Dos窗口输入：scala
  >
  > 出现：Welcome to Scala version 2.11.7 (Java HotSpot(TM) Client VM, Java 1.7.0_80).Type in expressions to have them evaluated.Type :help for more information

- linux中

  ```
  # 解压：
  tar -zxvf /work/software/scala-2.11.7.tgz -C /opt/software/
  
  # 生成软连接：
  ln -s /opt/software/scala-2.11.7 /usr/local/software/scala
  
  # 配置环境变量
  # vi  /etc/profile在文件后添加：
  export SCALA_HOME=/usr/local/software/scala
  export PATH=$JAVA_HOME/bin:$SCALA_HOME/bin:$PATH
  ```

# 二、temp

  



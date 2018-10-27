# 一. jar包压缩解压:

## 1. jar命令

> 用法: jar {ctxui}[vfmn0PMe][jar-file] [manifest-file][entry-point] [-C dir] files ...  
> 选项:  
> ​    -c  创建新档案  
> ​    -t  列出档案目录  
> ​    -x  从档案中提取指定的 (或所有) 文件  
> ​    -u  更新现有档案  
> ​    -v  在标准输出中生成详细输出  
> ​    -f  指定档案文件名  
> ​    -m  包含指定清单文件中的清单信息  
> ​    -n  创建新档案后执行 Pack200 规范化  
> ​    -e  为捆绑到可执行 jar 文件的独立应用程序  
> ​        指定应用程序入口点  
> ​    -0  仅存储; 不使用任何 ZIP 压缩  
> ​    -P  保留文件名中的前导 '/' (绝对路径) 和 ".." (父目录) 组件  
> ​    -M  不创建条目的清单文件  
> ​    -i  为指定的 jar 文件生成索引信息  
> ​    -C  更改为指定的目录并包含以下文件  
> 如果任何文件为目录, 则对其进行递归处理。  
> 清单文件名, 档案文件名和入口点名称的指定顺序  
> 与 'm', 'f' 和 'e' 标记的指定顺序相同。  
>
> 示例 1: 将两个类文件归档到一个名为 classes.jar 的档案中:  
> ​       jar cvf classes.jar Foo.class Bar.class  
> 示例 2: 使用现有的清单文件 'mymanifest' 并  
> ​           将 foo/ 目录中的所有文件归档到 'classes.jar' 中:  
> ​       jar cvfm classes.jar mymanifest -C foo/ .  



## 2. jar命令详解

> jar 命令详解
>
> 使用不带任何的 jar 命令我们可以看到 jar 命令的用法如下：
>
> jar {ctxu}[vfm0M] [jar-文件] [manifest-文件] [-C 目录] 文件名 ...
>
> 其中 {ctxu} 是 jar 命令的子命令，每次 jar 命令只能包含 ctxu 中的一个，它们分别表示：
>
> -c　创建新的 JAR 文件包
>
> -t　列出 JAR 文件包的内容列表
>
> -x　展开 JAR 文件包的指定文件或者所有文件
>
> -u　更新已存在的 JAR 文件包 (添加文件到 JAR 文件包中)
>
> 特别注意，在参数的下达中， c/x/t/u 仅能存在一个！不可同时存在！
>
> 因为不可能同时压缩与解压缩。
>
> -z ：是否同时具有 gzip 的属性？亦即是否需要用 gzip 压缩？
>
> -j ：是否同时具有 bzip2 的属性？亦即是否需要用 bzip2 压缩？
>
> -v ：压缩的过程中显示文件！这个常用，但不建议用在背景执行过程！
>
> -f　指定 JAR 文件名，通常这个参数是必须的
>
> 请留意，在 f 之后要立即接档名喔！不要再加参数！
>
> 　　　例如使用『 tar -zcvfP tfile sfile』就是错误的写法，要写成
>
> 　　　『 tar -zcvPf tfile sfile』才对喔！
>
> -p ：使用原文件的原来属性（属性不会依据使用者而变）
>
> -P ：可以使用绝对路径来压缩！
>
> -N ：比后面接的日期(yyyy/mm/dd)还要新的才会被打包进新建的文件中！
>
> –exclude FILE：在压缩的过程中，不要将 FILE 打包！
>
> -m　指定需要包含的 MANIFEST 清单文件
>
> -0　只存储，不压缩，这样产生的 JAR 文件包会比不用该参数产生的体积大，但速度更快
>
> -M　不产生所有项的清单（MANIFEST〕文件，此参数会忽略 -m 参数
>
> [jar-文件] 即需要生成、查看、更新或者解开的 JAR 文件包，它是 -f 参数的附属参数
>
> [manifest-文件] 即 MANIFEST 清单文件，它是 -m 参数的附属参数
>
> [-C 目录] 表示转到指定目录下去执行这个 jar 命令的操作。它相当于先使用 cd 命令转该目录下再执行不带 -C 参数的 jar 命令，它只能在创建和更新 JAR 文件包的时候可用。　　
>
> 文件名 ... 指定一个文件/目录列表，这些文件/目录就是要添加到 JAR 文件包中的文件/目录。如果指定了目录，那么 jar 命令打包的时候会自动把该目录中的所有文件和子目录打入包中。



## 3. 压缩

```shell
# 把2个test文件归档到test.jar中
jar -cvf test.jar test1.txt test2.txt
```

## 4. 解压

```shell
jar -xvf test.jar
```

## 5. 添加文件到jar中

```shell
jar -uvf $jar_file $file
```



# 二. shell&properties文件

- [参考地址](https://blog.csdn.net/qq_36684665/article/details/81134179)

- 配置文件config.properties

  ```properties
  #以下是配置文件内容，配置文件文件名为config.properties
  username=jack
  password=33281
  ```

- shell脚本

  ```shell
  #!/bin/bash
  source ./config.properties
  echo ${username}
  ```

# 三. shell获取绝对路径

- [参考地址](https://www.cnblogs.com/xuxm2007/p/7554543.html)

- shell

  ```shell
  SHELL_FOLDER=$(cd "$(dirname "$0")";pwd)
  SHELL_FOLDER=$(dirname $(readlink -f "$0"))
  ```

# 四. java读写properties文件

- 读取a.properties属性列表，与生成属性文件b.properties

  ```
  import java.io.BufferedInputStream;
  import java.io.FileInputStream;
  import java.io.FileOutputStream;
  import java.io.InputStream; 
  import java.util.Iterator;
  import java.util.Properties; 
  
  public class PropertyTest {
      public static void main(String[] args) { 
          Properties prop = new Properties();     
          try{
              //读取属性文件a.properties
              InputStream in = new BufferedInputStream (new FileInputStream("a.properties"));
              prop.load(in);     ///加载属性列表
              Iterator<String> it=prop.stringPropertyNames().iterator();
              while(it.hasNext()){
                  String key=it.next();
                  System.out.println(key+":"+prop.getProperty(key));
              }
              in.close();
              
              ///保存属性到b.properties文件
              FileOutputStream oFile = new FileOutputStream("b.properties", true);//true表示追加打开
              prop.setProperty("phone", "10086");
              prop.store(oFile, "The New properties file");
              oFile.close();
          }
          catch(Exception e){
              System.out.println(e);
          }
      } 
  }
  ```





# 1. 学院

## 1.1 尚学堂
1. 账号
  - 官网账号密码：18911831867 / 19910605abc
  - 百战程序员：18911831867
    邮箱：zhangzhengstrive@163.com
    19910605abc
2. 官网：http://www.bjsxt.com/
3. 尚学堂百战程序员视屏地址：http://www.sxt.cn/#4

## 1.2 小象学院
1. 官网：http://www.chinahadoop.cn/



# 2. 资源地址
## 2.1 kylin
1. kylin公开课视屏（小象学院）：https://v.qq.com/x/page/j0153igsfyk.html
2. 小象学院kylin视屏：http://www.chinahadoop.cn/search?q=kylin
3. ​

## 2.2 面试资源
1. 同学大数据面试集锦：http://www.daoke360.com/front/articleinfo/26.html
2. 若则数据面试题（github）：https://github.com/ruozedata/BigData/blob/master/interview/%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md



零基础学习人工智能+大数据教程（2018年最新版）
课程学习地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/229
课程出自学途无忧网：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com

本课程划分为两大部分，第一部分侧重于基于Python的数据分析：从最基础的Python基础语法开始讲起、Python科学计算及数据分析工具包+Python数据可视化工具包的学习，系统了介绍了数据收集、清洗、处理、展示的数据分析与挖掘的全过程

第二部分为本课程的中高级部分：从最核心的机器学习的十大经典算法原理以及Python语言的实现开始。到机器学习、深度学习、大数据+机器学习，自然语言处理，相关机器学习/深度学习框架，项目实战等 。过程中涉及了大量的机器学习过程中常用的技巧、经验和方法，并配合经典机器学习项目实战训练，达到了解人工智能，机器学习、大数据生态圈的关系，并能够利用所学结合实际业务完成数据分析、人工智能的项目，并最终轻松 迈进人工智能的领域，实现高薪就业转型！

5大阶段，20+ 课程，16+ 经典案例，4大商业项目实战，5层级能力训练模型（代码级、应用级、工程级、算法级、专家级），打造史上最实用的听得懂、学得会的最接地气 的人工智能（机器学习）高端培训课程 



免费课程目录

```
git的使用
1-git介绍和使用
2-git中的方法
3-git版本切换、分支和合并
4-git中的push和pull方法


第2节：HDFS增强
hdfs客户端操作
	用流api进行文件下载、目录操作、查看文件夹以及文件信息、客户端流式操作的代码实现、上传文件（写数据）流程、下载文件（读数据）流程。
NAMENODE工作机制--元数据管理
	三种存储机制、checkpoint过程、利用原理来分析解决生产中可能遇到的问题和现象
	日志采集汇聚案例：需求----实现流程、shell脚本实现、回顾hdfs的基本工作机制、运行测试
hdfs的概念跟特性回顾讲解
	hdfs的命令行操作常用命令、hdfs的命令行操作常用命令、hdfs实际开发中使用的场景、java操作hdfs环境搭建以及环境测试、hdfs客户端权限身份伪造的问题、windows平台下开发hadoop需要注意的细节、hdfs客户端操作--用流api进行文件上传；
1-1、HDFS前言
2-2、HDFS的概念和特性
3-3、HDFS的shell(命令行客户端)操作
4-4、HDFS的工作机制
5-5、NAMENODE的工作机制
6-6、java的api操作`（上）
7-7、java的api操作`（下）
8-8、案例1：开发shell采集脚本





MapReduce是一种编程模型，用于大规模数据集（大于1TB）的并行运算。概念"Map（映射）"和"Reduce（归约）"，是它们的主要思想。
MapReduce极大地方便了编程人员在不会分布式并行编程的情况下，将自己的程序运行在分布式系统上。
  maptask并行度机制
    切片的原理、切片大小参数设置、debug跟踪、优化经验之谈、reduceTask并行度决定
    mapreduce编程案例--倒排索引--需求分析、步骤代码
    mapreduce编程案例--共同好友--需求思路、实现步骤

  流量汇总排序在一个mr-job中完成、分省份输出案例----自定义partitioner
    partitioner组件细节--reduce Task个数跟partition分区个数关系
    Combiner组件的用法及注意事项

  mapreduce工作机制详解
     maptask端的机制、Reducetask端的机制、mapreduce工作机制详解----shuffle的含义

  maprecduce运算框架中用户可自定义的组件总结
第4节：MAPREDUCE增强
1-1、Mapreduce排序
2-2、自定义partitioner
3-3、Mapreduce的combiner
4-4、mapreduce工作机制详解（上）
5-5、mapreduce工作机制详解（下）
6-7、maptask并行度设置
7-8、倒排索引
8-9、共同好友


top
htop

reboot
shutdown -h 20:30

ifconfig：io本地会环，

```




图片

https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/rsyslog/rsyslog%E7%89%88%E6%9C%AC%E8%BF%87%E9%AB%98%E5%BC%82%E5%B8%B8.png?raw=true



kakfa 参数：  
&emsp;读取与记录group的offset相关的配置信息：  

> offset.metadata.max.bytes，默认值4096.用于配置offset的请求的最大请求的消息大小。  
> offsets.load.buffer.size，默认值5MB，用于在读取offset信息到内存cache时，用于读取缓冲区的大小。  
> offsets.retention.minutes，默认值24小时，针对一个offset的消费记录的最长保留时间。  
> offsets.retention.check.interval.ms，默认值600秒，用于定期检查offset过期数据的检查周期。  
> offsets.topic.num.partitions，默认值50,offset记录的topic的partition个数。  
> offsets.topic.replication.factor，默认3,用于配置offset记录的topic的partition的副本个数。  
> offsets.commit.timeout.ms，默认值5秒，用于配置提交offset的最长等待时间。  
> offsets.commit.required.acks，默认值-1,用于配置提交offset的请求的ack的值。  
> group.min.session.timeout.ms，默认值6秒，  
> group.max.session.timeout.ms，默认值30秒，用于配置session的超时时间。  


学习资源：  
&emsp;上海Spark meetup精彩演讲视频：  

- [Spark在金融领域的算法实践-平安银行](http://t.cn/RQohWlc)  
- [Data Layout in Spark - Databricks](http://t.cn/RQoh3nS)  
- [Spark中的压缩编解码最佳实践 - Intel](http://t.cn/RQohsbj)  

本周热门大会直播预告
>
>  1月26日  
>  1.数据库大咖秀http://t.cn/RQTAV3R  
>  2.华为大数据认证启航行动——泰克.北京站 http://t.cn/RQkSVNv  
>  1月27日  
>  1.阿里云移动测试杭州站http://t.cn/RQ0CfG1  
>  2.数人云深圳http://t.cn/RQ0CpQb  
>  3.AI在反欺诈中的应用实践http://t.cn/RQ0Colq  
>  4.华为大数据认证启航行动之誉天?广州站 http://t.cn/RQkB8Kk  
>  5.遇见未来 ? 2018区块链全球论坛http://t.cn/RQkr9MF  
>  6.LiveVideoStack Meet：移动音视频开发进阶暨新书分享会http://t.cn/RQkrYGn  
>  7.HUAWEI HiAI公开课 邀你一起解锁未来http://t.cn/RQkrnWT  
>  1月28日  
>  智能合约的设计原理及开发3-3 http://t.cn/RQFmrOC 



#前端
> 前端素材表：[http://www.17sucai.com/?invite=888623](http://www.17sucai.com/?invite=888623)





## 1

https://github.com/debasishg/scala-redis

## 2

![img](file:///C:\Users\zhang\AppData\Local\Temp\[5UQ[BL(6~BS2JV6W}N6[%S.png)https://redis.io/clients#scala

## 3

每天进步一点点
每日一读

TensorFlow教程：1基本概念
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.aboutyun.com/forum.php?mod=viewthread&tid=23896
1.机器学习和深度学习的基础包含哪些内容？
2.TensorFlow包含哪些内容？
3.如何使用TensorBoard？



资源：
区块链革命书籍
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.aboutyun.com/forum.php?mod=viewthread&tid=23897

更多
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.aboutyun.com/forum.php?mod=viewthread&tid=23898

每日一读汇总
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.aboutyun.com/forum.php?mod=viewthread&tid=23739



## 4

@全体成员 
年前最后三个课程，为明年而打算:
【优惠截止到今晚24点】

一. 大数据面试题集锦第二季:
1.10次课程，从头到尾，讲解【答案】!
2.从JAVA到大数据，系统复习面试要点，夯实基础 !
3.无论你要不要找工作，必须听一听 !

原价1000，【月底前】报名优惠为599。
（凡机构学员有叠加优惠，详情咨询课程顾问-星星）

二. 高级班二期课表:
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.ruozedata.com/static/courses/course-02.html
（本月31号开班，月底前报名立减2000）

三. 年后3月初，生产纯项目线下班第八期:
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.ruozedata.com/static/courses/course-01.html
（年后要提升及跳槽的自己看看课表，只招10个，名额有限）

详情访问链接或者咨询我![img](file:///C:\Users\zhang\AppData\Local\Temp\7[R844}%0OFGK]5`{V[WK)N.png)



## 5

《高分辨率区域气候模式在生态、水文、风资源等领域高级应用培训通知》4天课程 
正式文件链接：![img](file:///C:\Users\zhang\AppData\Local\Temp\[5UQ[BL(6~BS2JV6W}N6[%S.png)https://pan.baidu.com/s/1dy4joE

时间：2018年3月15 日 -3月19日   地点：南 京
培训目标：
1、熟悉区域模式的构成及物理过程；            
2、能自主完成区域模式获取、安装及更新；
3、能自主进行区域模式的调试及运行； 
4、能利用BASH、CDO、NCO、GRADS、NCL等后处理工具进行结果处理及分析。
5、掌握高分辨率区域气候模式在生态、水文、风资源等领域应用技术方法
咨询请联系：张伟010-53853500   手机18201545027  qq：515325120



# 6

2018最新录制课程ElasticSearch实战
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/224 

浅入深出ElasticSearch构建高性能搜索架构
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/161 

基于MEAN全栈架构的多用户博客系统（Angular2+Node+MongoDB）
学习地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/223

从零开始学可视化数据分析师就业课程(9个课程，26章，共计324课时！Tableau、Echarts、Power BI、excel)
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/182

Tableau商业智能与可视化应用实战
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/179

大数据可视化－Echarts从入门到上手实战
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/180

Power BI数据分析快速上手及案例实战
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/194

Excel数据处理与分析实战
课程学习地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/191

2017零基础大数据就业课程（零到大神一“部”之遥） 
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/181

全面深度剖析Spark2--知识点，源码，调优，JVM，图计算，项目
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/220

基于Docker实现PaaS平台
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/166

【直播】大数据系统架构分析师高端培训课程
![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/200

Spark从入门到上手实战
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/my/course/186

基于Python Spark的大数据分析
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/173

Spark全面精讲（基于Spark2版本+含Spark调优+超多案例）
课程观看地址：![img](file:///C:\Users\zhang\AppData\Local\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)http://www.xuetuwuyou.com/course/149



Hadoop从入门到上手企业开发视频下载[70集]：https://www.iteblog.com/archives/1273.html







hadoop系列最全视频教程  定时删除赶紧下载 
 链接: http://pan.baidu.com/s/1bnGV8dT 密码: 7wna
链接：http://pan.baidu.com/s/1bndG3kr 密码：x6d2

# 7. 万门大学 人工智能

万门大学：https://www.wanmen.org/
```
人工智能专业学习课程
领取步骤：1.点击优惠券链接兑换课程  2.点击课程链接上课
第一阶段:数学
《高等数学特训班》
优惠券链接:https://st.h5.xiaoe-tech.com/st/9AQlwNiBD
课程链接:https://st.h5.xiaoe-tech.com/st/9lQXf9gXL

《线性代数两日特训班》
优惠券链接:https://st.h5.xiaoe-tech.com/st/6jF7M4Jra
课程链接:https://st.h5.xiaoe-tech.com/st/9JuhENZq5      

《考研概率论》
优惠券链接：https://st.h5.xiaoe-tech.com/st/8Y6XjN09F 
课程链接：https://st.h5.xiaoe-tech.com/st/1uWUJA1ZP

第二阶段:编程:
《python编程入门》
优惠券链接：https://st.h5.xiaoe-tech.com/st/2zb5qczoi
课程链接：https://st.h5.xiaoe-tech.com/st/6VBafa7mM

第三阶段:机器学习
《机器学习Machine Learning》
优惠券链接：https://st.h5.xiaoe-tech.com/st/0IEsjrVnS
课程链接https://st.h5.xiaoe-tech.com/st/14pRWJEM9

《机器学习理解与实战》
优惠券链接：https://st.h5.xiaoe-tech.com/st/7ATW2L9ke
课程链接:https://st.h5.xiaoe-tech.com/st/93i61IA7p

第四阶段:数据挖掘实战
《大数据与贝叶斯课程》
课程链接：https://st.h5.xiaoe-tech.com/st/8dLINpGZE

《数据挖掘课程》
课程链接：https://st.h5.xiaoe-tech.com/st/2sv9GdDrM

第五阶段:深度学习
《深度学习和神经网络》
优惠券链接：https://st.h5.xiaoe-tech.com/st/41pjtxsiP
课程链接：https://st.h5.xiaoe-tech.com/st/3wHOBWtwO

《10G AI资料包》链接:https://pan.baidu.com/s/1YtNft-KjcMD-atbLiXI-EQ  密码:pui5
```
# 8. DT大数据

30个商业案例（AI）：
​	http://mp.weixin.qq.com/s?__biz=MzIwMDI0NzYxMQ==&mid=2649672117&idx=1&sn=f0798528f66c36997a98645700c46302&chksm=8e9a79e1b9edf0f708cea90b48264b0f70682485d948b8bc977a001ddc2aa3a21298beb668f7&mpshare=1&scene=1&srcid=0419q8ujcy3x4SMjnSrhf6Pn#rd

# 9. 若则大数据
- github：https://github.com/ruozedata/BigData/blob/master/blog/BigDataBlogOverview.md

# 10 spark
## 10.1 spark2.2快速入门到精通
（大数据学习资料分享群-232840209）
《spark2.2从入门到精通》有更新了
​	下载链接:https://pan.baidu.com/s/1sm2Jdmt 密码:rdea
​	更新日期：2018-07-15

# 11. QQ中的资源
## 11.1 大杂烩
	QQ中的资源：（大数据学习资料分享群-232840209）
	首席互联网架构师高级研修班课程(高端)
	链接: https://pan.baidu.com/s/1uW_z2emTlZS9Qx1g-k0jmw 密码: caaw
	
	北风网 AI
	链接: https://pan.baidu.com/s/1U14FQirGgrbHhD02DWAomQ 密码: 1d63
	
	蚂蚁
	链接：https://pan.baidu.com/s/1WfvsmQkMG8qSmWhIPCpr7A 密码：21wy
	
	沽泡学院
	链接: https://pan.baidu.com/s/1UMEbTvhlnOnvVzzzZgTI4Q 密码: p9q5
	
	如果视频失效，请联系QQ重新进行发布。更多视频,更多行业内最新资讯。
	联系微信:17549206263    QQ:1121219793
	
	大数据跳槽班课程
	链接：https://pan.baidu.com/s/1zy40-wT8RE1jc2Fn1hfnLA 密码：i64w
	
	Spring源码解读与设计详析
	链接: https://pan.baidu.com/s/1a5nFu_kVZcM-BRnotvnkhQ 密码: p4vj
	
	产品经理从基础到全能
	链接: https://pan.baidu.com/s/1VCTY8ZpWJqhVblMLYapjog 密码: qvbt
	
	深入理解jvm虚拟机
	链接: https://pan.baidu.com/s/1WZb-K-o39i1GvsVwYKLvDg 密码: ednw
	
	CM+CDH集群管理课程
	链接: https://pan.baidu.com/s/1uqY5VpdbRGFBESgw_afJuA 密码: 7p5a
	
	龙果课程
	链接: https://pan.baidu.com/s/1h8j0gPzI38vI7BKnbceKKg 密码: qpw2
	
	如果视频失效，请联系QQ重新进行发布。更多视频,更多行业内最新资讯。
	联系微信:17549206263    QQ:1121219793
	
	十大互联网电商项目
	链接: https://pan.baidu.com/s/148BfkxIlB5weT61o0GuHfQ 密码: kr3c
	
	Oracle从入门到DBA之路
	链接：https://pan.baidu.com/s/1YGHPJwCGqepGambm0Q_sIw 密码：k8s2
	
	netty源码分析
	链接：https://pan.baidu.com/s/1oGBrcATGkk-HUXHQuZpxZQ 密码：x53v
	
	Hadoo实战课程 Hadoop云计算综合实战项目视频教程
	链接：https://pan.baidu.com/s/1ExNkaSNe9EKk8GW20QT1rA 密码：4a3s
	
	Spring Boot技术栈博客企业前后端
	链接：https://pan.baidu.com/s/1-8Sij9En3KpmpCU3t4L46w 密码：4951
	
	精通JFinal完整
	链接: https://pan.baidu.com/s/1mTyl5bPzutT8OC4tVWFVWQ 密码: wsns
	
	如果视频失效，请联系QQ重新进行发布。更多视频,更多行业内最新资讯。
	联系微信:17549206263    QQ:1121219793
	
	【全6套视频】软件测试
	链接：https://pan.baidu.com/s/1yMOyu_534eDLUuDGkhwWtw 密码：96i6
	
	基于Spring Boot框架企业级应用系统开发全面实战
	链接：https://pan.baidu.com/s/16CfIDZJ6tWkndM8ORLYvwQ 密码：ebao
	
	数据分析北风课程
	链接：https://pan.baidu.com/s/13pFgBwg-ugAff_RBOAIC-Q 密码：c6k7
	
	世上最全Java高薪面试题视频完整
	链接：https://pan.baidu.com/s/11Skf2nAppOL85Jroz-qNtw 密码：j10n
	
	王文君 多线程编程实战教程一、二、三阶段
	链接: https://pan.baidu.com/s/1tFvuA8SmJRv1WfWHOAOFkg 密码: 6kqf


​	
​	如果视频失效，请联系QQ重新进行发布。更多视频,更多行业内最新资讯。
​	联系微信:17549206263    QQ:1121219793


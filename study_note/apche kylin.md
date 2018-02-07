# **kylin**



##


Kylin沿用了原来数据仓库技术中的Cube概念，把无限数据按有限的维度进行“预处理”，然后将结果（Cube）加载到HBase里，供用户查询使用

单用户级的数据查询、第三方可视化工具的集成、多维Cube建立的维度数极限等的测试。我们还计划用Kafka来导入数据，Spark来加工Cube，用其他产品来代替HBase进而提高数据读取性能，用Kylin的路由选择来桥接新老Cube

Apache Kylin是一种分布式分析引擎，提供Hadoop之上的标准SQL查询接口及多维分析（OLAP）功能



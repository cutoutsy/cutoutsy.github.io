---
layout: post
title: "Storm 0.9.3 released"
date: 2015-05-15 11:11:22
tags: 
		- storm
		- bigdata
---
此版本发布于2014年11月25日
## Kafka整合
Apache Storm自从0.9.2就开始支持Apache   Kafka了,0.9.3版本在Kafka整和带来大量的更新，包括可以往一台或者一个集群写入数据
现在Storm和Kafka整合使得读写都不将受到限制,Storm使用者可以把Kafka作为消息流的源头或者消息源的目的地。这样允许内在topology交流，topology链,将基本的spout、bolt和基本Trident数据流结合，甚至和任何外部支持从Kafka获取数据的系统整合.
更多关于Storm的Kafka整合的信息可以在[storm-kafka project documentation](https://github.com/apache/storm/blob/v0.9.3/external/storm-kafka/README.md)找到。
<!-- more -->
## HDFS整合
许多的流处理用HDFS来存储数据，以便离线分析处理。Apache Storm的HDFS整合包含几个bolt和Trident状态继承让storm开发者更容易的从任何topology向HDFS写入数据。
更多关于Storm的HDFS整合的信息可以在[storm-hdfs project documentation](https://github.com/apache/storm/tree/v0.9.3/external/storm-hdfs)找到。
## HBase整合
Apacge Storm的HBase整合包含一系列的组件，允许topologies实时向HBase写入和查询数据。许多组织使用HBase作为他们大数据批处理、交互、实时工作流策略的一部分。Storm HBase的整合允许使用者利用数据作为流的查询，也作为流计算结果的目的地。
更多关于Storm的HBase整合的信息可以在[storm-hbase project documentation](https://github.com/apache/storm/tree/v0.9.3/external/storm-hbase)找到。
## 减少依赖冲突
在Storm以前的版本中，很常见用户自己拓扑的依赖和Storm自己的依赖产生冲突。在Storm 0.9.3中几个常见的包冲突已经迁移（隐藏）来避免这种情况。开发者可以更自由的使用Storm打包的版本，或者自己提供的版本。
下面的表格列举了依赖包的迁移：
|依赖|原来的包|Storm包
|--|--|--|
|Apache Thrift| org.apache.thrift|org.apache.thrift7|
|Netty|org.jboss.netty|org.apache.storm.netty|
|Google Guava|org.google.common|org.apache.storm.guava|
|            |org.google.thirfparty|org.apache.storm.guava.thirdparty|
|Apache HTTPClient|org.apache.http|org.apache.storm.http|
|Apache Zookeeper|org.apache.zookeeper|org.apache.storm.zookeeper|
|Apache Curator|org.apache.curator|org.apache.storm.curator|
## 多语言改善
Apache Storm 0.9.3增加了Node.js,允许开发者可以用JavaScript来编写spouts和bolts。
除了增加了Node.js的实现，这多语言协议也从实质上得到改善，更加稳健和错误处理能力。
所以，这多语言API不是向下兼容的。有多语言拓扑的使用者在升级之前看看Python，Ruby,和JavaScript的多语言例子来判断升级的影响。
## 完成的更新
> * 将"swap!"变成"reset!"，修复任务版本在supervisor上的bug。
> * Storm json回复编码设为UTF-8
> * 从多语言子进程检查心跳
> * "topology.enable.message.timeout"没有意义
> * exector忽略本地主机名配置
> * 测试超时时间应该被设置
> * 改变在日志里默认的时间格式为ISO8601，以便包含时区
> * Storm-Kafka spout在无效的偏移时保持发送获取请求
> * Guava com.google.thirdpart.publicsuffix不再被隐藏
> * 正在被读取时，不要修改netty服务器的taskToQueueId
> * 一个worker无限重连到另一个死掉的worker
> * 增加tuple作为HBaseValueMapper的输入参数
> * 如果Storm CLI运行不知道的命令，就会已错误代码254退出
> * 不统计bolt acks&fails在总的状态里
> * 修复在Windows下构建
> * 用jquery URL插件替换purl.js
> * maven解决文档和清理隐藏的依赖
> * 增加storm-habse模块
> * Topology可视化在ui上不会被锁住
> * repl命令使用的类已过期
> * netty客户端和监控继承storm的指数退避策略
> * exit-process!并不总是退出进程，是抛出异常
> * 修复任务排序
> * 扩展/storm-kafka:避免NPE在空消息负载时
> * 修复错误消息影响kafka偏移量
> * 正确的文档在STORM-UI-REST-API.md
> * 格式化UI HTML代码
> * 隐藏/迁移那些容易产生依赖冲突的赖包
> * cluster.xml文件不再包含STORM_LOG_DIR值
> * Kafka spout：抛出RuntimeException当在分区里找不到leader
> * 修改storm-kafka文档
> * 增加kafka trident状态以致消息可以发送kafka主题
> * 增加HDFS模块的整合
> * 将spout ids公共管理
> * STORM_CONF_DIR环境变量支持
> * 对于错误拓扑，增加详细的节点和组件页面
> * 为Workers,每个拓扑的类路径和环境变量
> * 去掉过时的通过curator的netty传输
> * 用RunTime.exit()替代RunTime.halt()
> * 在本地模式里，将ZooKeeper进程里去耦
> * 在storm命令里增加对python3的支持
> * 可以在storm-kafka进行序列化
> * 在对UI dom排序前,进行空表的检查
> * 增加日志查看页面和下载
> * Typo在storm_env.ini
> * 在日志里增加shell进程的pid和name
> * Storm UI REST api文档
> * 多种度量特征的方案
> * 多语言python进程进入无限循环
> * supervisor和worker更智能的下载任务代码
> * 更多的Config限制性检查，用Utils.getInt()进行严格的范围检查
> * 将broken jquery.tablesorter.min.js换成最新的
> * 增加更多的storm监控工具,通过交互性来监控storm
> * 测试：允许使用者将TEST-TIMEOUT-MS作为一个拓扑的参数
> * 一个Spout/Bolt可以在不同的timeBucket里用同一个名字注册两次
> * 由于uninten...heartbeats-to-nimbus在supervisor-test中失败
> * 修复当在storm使用apache tika时FileNotFoundException错误
> * 修复时间用默认时区异常
> * 去掉在storm-starter python代码里的引用
> * 当当前偏移过时超过100k时，Kafka Spout默认使用当前最新的偏移
> * 在executor里查找主机名
> * 针对多语言soout和bolt支持日志级别
> * 增加一个可以看STORM JIRA和git pulls的工具
> * 在supervisor-test里加载验证一次不能解决多语言拓扑
> * Storm重分配代码导致多语言拓扑被被分配到一个单独的端口
> * 更新test以致排序是非常明确的
> * 修复在netty client的重连逻辑
> * 给最近的错误增加颜色显示，修复ui模板
> * 修复topology summary页面显示错误的顺序
> * 允许supervisor通过路径操作他们自己的空间
> * 修复快速fail在ShellBolt的异常
> * Storm UI丢失表格样式当tablesorter更新时
> * IllegalArgumentExeptiond替换NullPointerException
> * 由于storm-hdfs pom.xml错误，最新的storm没有build
> * 迁移到curator 2.5.0
> * 例子spout使用错误的名字当连接时
> * 从ShellBolt children报告错误等级消息
> * 多语言日志的日志级别协议可能导致挂起
> * 更新ShellBolt到不存在当关闭的时候
> * 模拟时间提前在test cluster存在后导致断断续续的测试错误
> * 为Config增加metric helpers静态版本
> * 执行utf-8当多语言从stdin读取时
> * 给Storm UI API增加JSON-P支持
> * 为*.worker.childopts提供另外的的String替换
> * 在storm.cmd里增加command remoteconfvalue支持
> * 在desending order里通过空闲的槽来排序supervisor
> * 为没有完成的测试spout改善错误消息
> * 合适的处理InterruptedIOException异常
> * exit-process!不总是退出进程，有时会抛出异常
> * Storm UI不使用UTF-8
> * 跟新Logback版本
> * nodejs多语言协议实现和例子
> * 增加spinner当UI从nimbus加载状态时
> * 丢掉StormSubmitter API
> * Workers从他们自己的supervisor继承storm.conf.file/storm.options属性
> * 增加ZK连接超时时间可配置在Kafka spout
> * 提取ITuple接口
> * 更新DEVELOPER.md，Storm已经从孵化版到正式版
> * 跟新storm-starter README，Storm已经从孵化版到正式版

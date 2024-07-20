# Kafka

###### 传统消息队列的主要应用场景包括：

缓存/消峰、解耦、异步通讯

###### 消息队列的两种模式

![image-20240410152632575](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240410152632575.png)

###### Kafka基础架构

![image-20240410154435333](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240410154435333.png)

###### Kafka需要修改的配置

打开目录下的config/server.properties  文件，

1.修改服务器节点broker.id，具体根据需求配置, 例如：broker.id=0

2. 修改log.dirs，配置Kafka数据目录，一般为Kafka目录下datas文件夹中,例如log.dir=/opt/module/kafka/datas
3. zookeeper.connect, 一般配置在zookeeper根目录，例如：zookeeper.connect=hadoop102:2181,hadoop103:2181,hadoop103:2181/kafka

###### Kafka配置环境变量

`#KAFKA_HOME`
`export KAFKA_HOME=/opt/module/kafka（Kafka绝对路径）`
`export PATH=$PATH:$KAFKA_HOME/bin`

配置完记得分发到其他节点，并source环境配置文件

###### Kafka启动停止命令、脚本

单节点启动： `bin/kafka-server-start.sh -daemon config/server.properties`

单节点停止：`bin/kafka-server-stop.sh`

集群启动停止脚本

`#! /bin/bash`
`case $1 in`
`"start"){`
 `for i in hadoop102 hadoop103 hadoop104`
 `do`
 `echo " --------启动 $i Kafka-------"`
 `ssh $i "/opt/module/kafka/bin/kafka-server-start.sh -`
`daemon /opt/module/kafka/config/server.properties"`
 `done`
`};;`
`"stop"){`
 `for i in hadoop102 hadoop103 hadoop104`
 `do`
 `echo " --------停止 $i Kafka-------"`
 `ssh $i "/opt/module/kafka/bin/kafka-server-stop.sh "`
 `done`
`};;`
`esac`

###### 查看操作主题命令参数：bin/kafka-topics.sh

![image-20240410162200980](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240410162200980.png)

`kafka-topics.sh --bootstrap-server hadoop102:9092 --list`		查看Kafka有哪些主题

`kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --create --partitions 1 --replication-factor 3`

创建主题first, 指定分区数为1，副本数为3

`kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --describe`   查看first主题详细信息

`kafka-topics.sh --bootstrap-server hadoop102:9092 --topic first --alter --partitions 3`  修改主题分区数，只能增大，不能减小。

不能通过命令行的方式修改副本数

###### 生产者生成数据

`kafka-console-producer.sh --bootstrap-server hadoop102:9092 --topic first`   生产者往first主题生成数据

`kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --topic first`  消费者消费first主题数据，增量消费

`kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --topic first --from-beginning`   消费者消费first主题数据，从头开始全部消费

###### 生产者往Kafka发送数据流程：

![image-20240411105709895](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240411105709895.png)

生产者调用send方法发送 --> 经过拦截器可以对数据进行加工或其他操作（可选项，比较复杂，一般不用，flume用拦截器比较多，操作简单）  -->  经过序列化器（为什么不用Java的序列化器，因为Java的序列化器无效数据太大，发送数据会变得很庞大）  -->  再经过分区器 （决定数据取去哪个分区，一个分区创建一个队列，默认是在内存中创建，总大小是32M，每一批次大小是16K。 双端队列，除了队列还有内存池。发送批次数据会创建批次大小，从内存池取出内存，数据发送到Kafka集群后数据会释放，同时将内存释放到内存池中）--> sender线程主动拉取数据（两个或条件：数据累积达到batch.size,或者到达linger.ms设置的时间，默认值0ms，表示没有延迟），一个分区一个节点，发送数据之后如果没有及时应答，最多可以缓存5个对应的请求，通过Selector打通底层链路。集群收到数据后开始同步，进行应答，应答有三种级别，如上图。

###### 异步发送：后面的数据不需要前面的数据发送到Kafka集群就可以继续往队列发送数据

###### 未解决问题：

1. 消费者窗口重启才能接收到新数据
2. 同步发送只能收到一条消息

###### Kafka分区的好处

![image-20240419094354758](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240419094354758.png)

###### Kafka分区策略

![image-20240419101318915](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240419101318915.png)

设置分区必须也设置key值，可以为空

指定某一张MySQL表数据全部到同一个topic分区，可以设置key值为表名

###### 生产者如何提高吞吐量

![image-20240419110344308](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240419110344308.png)


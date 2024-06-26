# zookeeper

### 注意：所有的代码均在GitHub，网址https://github.com/wxguo-shou


###### zookeeper工作机制

![image-20240412164058067](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412164058067.png)

###### zookeeper特点：

![image-20240412164644192](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412164644192.png)

###### zookeeper数据结构

![image-20240412164833420](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412164833420.png)

###### 应用场景：提供的服务包括:统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。

统一命名服务

![image-20240412165307055](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412165307055.png)

统一配置管理

![image-20240412165516830](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412165516830.png)

统一集群管理

![image-20240412165749248](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412165749248.png)

服务器动态上下线

![image-20240412165911793](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412165911793.png)

软负载均衡

![image-20240412170129982](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412170129982.png)

###### 配置参数解读：

 Zookeeper中的配置文件zoo.cfg中参数含义解读如下：

 1）tickTime = 2000：通信心跳时间，Zookeeper服务器与客户端心跳时间，单位毫秒

![image-20240412184304773](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412184304773.png)

4）dataDir：保存Zookeeper中的数据 注意：默认的tmp目录，容易被Linux系统定期删除，所以一般不用默认的tmp目录。 

5）clientPort = 2181：客户端连接端口，通常不做修改。

###### 配置zoo.cfg文件 

（1）重命名/opt/module/zookeeper-3.5.7/conf 这个目录下的 zoo_sample.cfg 为 zoo.cfg [atguigu@hadoop102 conf]$ mv zoo_sample.cfg zoo.cfg 

（2）打开 zoo.cfg 文件 [atguigu@hadoop102 conf]$ vim zoo.cfg 

#修改数据存储路径配置 dataDir=/opt/module/zookeeper-3.5.7/zkData 

#增加如下配置 #######################cluster##########################

 server.2=hadoop102:2888:3888 

server.3=hadoop103:2888:3888 

server.4=hadoop104:2888:3888 

（3）配置参数解读 server.A=B:C:D。

 A 是一个数字，表示这个是第几号服务器； 集群模式下配置一个文件 myid，这个文件在 dataDir 目录下，这个文件里面有一个数据 就是 A 的值，Zookeeper 启动时读取此文件，拿到里面的数据与 zoo.cfg 里面的配置信息比 较从而判断到底是哪个 server。 

B 是这个服务器的地址；

 C 是这个服务器 Follower 与集群中的 Leader 服务器交换信息的端口； 

D 是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。

###### zookeeper选举机制--第一次启动

![image-20240412191958114](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412191958114.png)

###### zookeeper选举机制：非第一次启动

![image-20240412192642983](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412192642983.png)

###### zookeeper集群停启脚本

`#!/bin/bash`
`case $1 in`
`"start"){`
`for i in hadoop102 hadoop103 hadoop104`
`do`
 `echo ---------- zookeeper $i 启动 ------------`
`ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh` 
`start"`
`done`
`};;`
`"stop"){`
`for i in hadoop102 hadoop103 hadoop104`
`do`
 `echo ---------- zookeeper $i 停止 ------------` 
`ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh` 
`stop"`
`done`
`};;`
`"status"){`
`for i in hadoop102 hadoop103 hadoop104`
`do`
 `echo ---------- zookeeper $i 状态 ------------` 
`ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh` 
`status"`
`done`
`};;`
`esac`

###### 节点类型：

![image-20240412204657292](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240412204657292.png)

###### 节点对应操作

create /sanguo "diaochan"

create /sanguo/shuguo "liubei"

注意：创建节点时，要赋值

2）获得节点的值
get -s /sanguo

get -s /sanguo/shuguo

3）创建带序号的节点（永久节点 + 带序号）
（1）先创建一个普通的根节点/sanguo/weiguo
create /sanguo/weiguo "caocao"

（2）创建带序号的节点
create -s /sanguo/weiguo/zhangliao "zhangliao"

create -s /sanguo/weiguo/zhangliao "zhangliao"

create -s /sanguo/weiguo/xuchu "xuchu"

如果原来没有序号节点，序号从 0 开始依次递增。如果原节点下已有 2 个节点，则再排序时从 2 开始，以此类推。

4）创建短暂节点（短暂节点 + 不带序号 or 带序号）
（1）创建短暂的不带序号的节点
create -e /sanguo/wuguo "zhouyu"

（2）创建短暂的带序号的节点
create -e -s /sanguo/wuguo "zhouyu"

（3）在当前客户端是能查看到的  ls /sanguo 
[wuguo, wuguo0000000001, shuguo]
（4）退出当前客户端然后再重启客户端  quit

（5）再次查看根目录下短暂节点已经删除
ls /sanguo
[shuguo]
5）修改节点数据值
set /sanguo/weiguo "simayi"

###### 监听器原理

![image-20240413182250479](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240413182250479.png)

###### 监听器命令行操作

注册监听/sanguo 节点数据变化：  get -w /sanguo	（注册一次，生效一次）

注册监听/sanguo 节点的子节点变化：  ls -w /sanguo	（注册一次，生效一次）

###### 删除节点

1）delete /sanguo/jin 

2）递归删除节点：	deleteall /sanguo/shuguo

3）查看节点状态：	 stat /sanguo

###### 写流程

![image-20240413194123554](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240413194123554.png)

只要有半数以上的节点写数据完成，就会向客户端应答

![image-20240413194413925](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240413194413925.png)

###### 服务器动态上下线

![image-20240413205635084](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240413205635084.png)

###### 分布式锁案例分析

![image-20240413205732228](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240413205732228.png)

**<u>分布式锁部分代码在相应的网址</u>**

###### 企业面试真题（面试重点） 

6.1选举机制 ：半数机制，超过半数的投票通过，即通过。 

（1）第一次启动选举规则： 投票过半数时，服务器 id 大的胜出 

（2）第二次启动选举规则： 

①EPOCH 大的直接胜出 

②EPOCH 相同，事务 id 大的胜出

 ③事务 id 相同，服务器 id 大的胜出 

6.2 生产集群安装多少 zk 合适？ 

安装奇数台。 生产经验： 

⚫ 10 台服务器：3 台 zk； 

⚫ 20 台服务器：5 台 zk； 

⚫ 100 台服务器：11 台 zk； 

⚫ 200 台服务器：11 台 zk

 服务器台数多：好处，提高可靠性；坏处：提高通信延时 

6.3 常用命令 

ls、get、create、delete


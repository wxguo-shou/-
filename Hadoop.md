

![](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240830080412364.png)

NameNode: 记录数据存储位置

2NN：辅助NameNode

DataNode: 数据具体存储位置

![image-20240830081001758](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240830081001758.png)

![image-20240830081456722](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240830081456722.png)

**scp****（secure copy****）安全拷贝**

（1）scp定义

scp可以实现服务器与服务器之间的数据拷贝。（from server1 to server2）

​    （2）基本语法

scp   -r    $pdir/$fname       $user@$host:$pdir/$fname

命令  递归   要拷贝的文件路径/名称  目的地用户@主机:目的地路径/名称



rsync   -av    $pdir/$fname       $user@$host:$pdir/$fname

命令  选项参数  要拷贝的文件路径/名称  目的地用户@主机:目的地路径/名称

​     选项参数说明

| 选项 | 功能         |
| ---- | ------------ |
| -a   | 归档拷贝     |
| -v   | 显示复制过程 |



ls -al  : 可以查看到所有隐藏文件

常用端口号
hadoop3.x
	HDFS NameNode 内部通常端8020/9000/9820

​	HDFS NameNode 对用户的查询端口:9870
​	Yarn查看任务运行情况的:8088

​	历史服务器:19888
hadoop2.x
​	HDFS NameNode 内部通常端8020/9000

​	HDFS NameNode 对户的查谁端日:50070

​	Yarn查看任务运行情况的:8088

​	历史服务器:19888

常用的配置文件
	3.x  core-site.xml	hdfs-site.xml	yarn-site.xml	mapred-site.xml	workers

​	2.x  core-site.xml	hdfs-site.xml	yarn-site.xml	mapred-site.xml	slaves

![image-20240831152329679](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831152329679.png)

HDFS命令

hadoop fs -moveFromLocal：从本地剪切到HDFS

hadoop fs -appendToFile:  文件末尾追加内容

hadoop fs -du -s -h :  统计文件夹大小

hadoop fs -du -h :  统计文件大小



HDFS API操作：创建文件夹、上传、下载、删除、文件更名、文件详情、判断文件还是文件夹

![image-20240831222741184](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831222741184.png)

![image-20240831223234314](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831223234314.png)

![image-20240831223610228](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831223610228.png)

![image-20240831224421141](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831224421141.png)

![image-20240831225516332](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831225516332.png)

请求时间默认3600s, Edits数据默认1000000次满，2NN默认每60s询问NN中Edits数据是否已满。

NN比2NN多了inprogress的数据

![image-20240831231335610](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831231335610.png)

先扫描块信息，然后立即上报NameNode

crc校验，保证数据完整性

![image-20240831232217946](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831232217946.png)

![image-20240831232718124](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240831232718124.png)


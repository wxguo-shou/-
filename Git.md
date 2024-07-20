# Git

###### 版本控制软件基础功能

1.保存和管理文件

2.提供客户端工具进行访问

3.提供不同版本文件的比对功能

###### 分布式版本控制

本地和远程都有仓库，远程服务器故障之后，可以使用本地服务器，待到远程服务器恢复之后，再把本地仓库内容同步到远程仓库，分布式版本控制比较依赖网络

创建文件在仓库目录下，软件可以动态识别文件

![image-20240414184813150](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240414184813150.png)

放在.git 目录下才算放在本地仓库

![image-20240414185416653](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240414185416653.png)



![image-20240414185353335](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240414185353335.png)

增加、修改、删除文件都需要提交

###### 下图表示旧文件没有内容，新文件增加一行

![image-20240414210417539](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240414210417539.png)



###### 减号表示旧文件从第一行发生变化，加号表示新文件第一第二行发生变化

![image-20240414210748935](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240414210748935.png)

###### 版本号作用

1.避免合并时版本冲突

2.定位文件

![image-20240415155613467](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240415155613467.png)

###### 友好查看文件内容

git cat-file -p dfe0770424b2a19faf507a501ebfc23be8f54e7b

文件并没有真正被删除，而是指向发生了变化

![image-20240415161607661](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240415161607661.png)

###### 分支处理中版本变化

![image-20240415162348242](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240415162348242.png)

![image-20240415162851600](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240415162851600.png)

###### 主要操作指令图示

![image-20240415163512536](C:\Users\wxguo\AppData\Roaming\Typora\typora-user-images\image-20240415163512536.png)

### git指令

git -v  :	git的版本

###### 仓库操作

git -init : 	在当前路径创建仓库

git clone https://gitee.com/wanxiangguo/test-gitee-project.git : 克隆远程仓库到本地

git clone https://gitee.com/wanxiangguo/test-gitee-project.git  remote-git-rep : 克隆远程仓库到本地，并自定义仓库名

git config user.name wxguo : 配置仓库用户， 加 --global  表示全局配置

git config user.email xxx@163.com ： 配置邮箱地址， 加 --global  表示全局配置

###### 文件操作

git status :	查看暂存区状态	

git add a.txt :	将文件放入暂存区

git rm --cached a.txt ：	将暂存区文件移回到工作区（不是真正意义上的移动）

git add *.txt ：	将所有工作区 .txt 文件变化  移动到暂存区

git commit -m 描述： 将暂存区文件提交到存储区，增删改文件都是这个命令

git log ： 	查看git历史提交日志

git log --oneline ：	查看git历史提交日志，一次提交只展示一行，版本号只显示前7个

git restore a.txt :	 恢复误删除， 从存储区恢复文件到工作区，如果将删除操作提交，该命令则无法恢复误删除

git reset --hard 6ab18b5： 恢复文件到某个版本，可以解决 git restore 操作无法恢复文件的问题， 但是回丢失提交过程

git revert 4d2cb17：	恢复文件到某个版本， 不会丢失提交记录， 相当于把某个操作又提交一次，把文件恢复到4d2cb17版本之前的操作，所以输入的版本号是想恢复版本的前一个操作文件的版本号

###### 分支操作

git branch 分支名称：	新增分支	（需要.git/refs/heads目录有分支，可以先做一次提交就会有主分支）

git branch -v :	查看当前有哪些分支

git checkout 分支名称：	切换分支

git checkout -b order:	 创建order分支， 并切换到order分支

git branch -d user :	删除user分支

git merge order :	将order分支合并到主分支， 如果有文件冲突，需要自己重新编辑冲突文件，再提交文件，就相当于合并成功。

​							注意： 合并分支文件到主分支， 当前分支必须要切换到主分支

###### 标签操作 （与客户端标签操作含义相同，客户端创建标签，命令行也可以看得到）

git log 59558dfc5caf596a4c513498431a6ea7cb2c83af ：	显示该版本号及版本号之前的提交记录

git tag uptfile 59558dfc5caf596a4c513498431a6ea7cb2c83af : 	将本次提交设置标签为 ： uptfile

git tag：	查看提交标签

git log uptfile:	显示该标签及标签之前的所有提交记录

git tag -d uptfile :	删除uptfile 标签

git checkout -b addfile：  利用标签创建分支， 一个分支就是一次提交的版本。

###### 远程仓库 (仓库名指的是config文件中 [remote "origin"]  双引号里面的信息)

git remote add 仓库名 远程仓库克隆链接  ： 如果当前仓库没有连接远程仓库，则可以通过该命令连接到远程仓库

git remote rename 名称： 重命名仓库名称

git remove 仓库名： 删除远程仓库连接

git push origin:	将本地修改同步到远程仓库， 如果url是ssh地址，则需要安全认证

git pull origin：   将远程仓库修改同步到本地仓库

---
layout:     post
title:      Centos7安装所需软件
subtitle:  重置了云服务器顺便博客上记一下
date:       2018-04-19
author:     RX
header-img: img/post-bg-ioses.jpg
catalog: true
tags:
    - Linux,
   - 配置环境
---

## Centos 7 安装 tomcat7, jdk,mysql,zookeeper
### Centos  安装jdk
  >安装jdk有三种方式
方法一：手动解压JDK的压缩包，然后设置环境变量
方法二：用yum安装JDK
方法三：用rpm安装JDK

>这里只记录第一种方式,建议不要使用yum安装,yum安装是给以后留坑(比如zookeeper 后面介绍)

###手动解压压缩包,然后设置环境变量**
####1.在/usr/目录下创建java目录
>1,[root@localhost ~]# mkdir/usr/java
2,[root@localhost ~]# cd /usr/java

####2,下载，然后解压	
>1,[root@localhost java]# curl -O http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-linux-x64.tar.gz 
2,[root@localhost java]# tar -zxvf jdk-7u79-linux-x64.tar.gz

####3.设置环境变量
>[root@localhost java]# vi /etc/profile

添加如下内容：
>JAVA_HOME=/usr/java/jdk1.7.0_79
JRE_HOME=/usr/java/jdk1.7.0_79/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH

让修改生效：
>[root@localhost java]# source /etc/profile

####4,验证
>[root@localhost java]# java -version
java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

###Centos 安装mysql
>####从最新版本的linux系统开始，默认的是 Mariadb而不是mysql！这里依旧以mysql为例

####1、先检查系统是否装有mysql
>rpm -qa | grep mysql

结果返回空 说明未安装
这里执行安装命令是无效的，因为centos-7默认是Mariadb，所以执行以下命令只是更新Mariadb数据库
>1.    yum install mysql

删除可用
> yum remove mysql

####2、下载mysql的repo源
> `#wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm`

安装mysql-community-release-el7-5.noarch.rpm包

>`# sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm`

![](https://img-blog.csdn.net/20160428092630350)

安装这个包后，会获得两个mysql的yum repo源：/etc/yum.repos.d/mysql-community.repo，/etc/yum.repos.d/mysql-community-source.repo。
![](https://img-blog.csdn.net/20160428092825022)

####3,、安装mysql

>`sudo yum install mysql-server`

根据步骤安装就可以了，不过安装完成后，没有密码(第一次让输入密码直接回车就好)，需要重置密码。

安装后再次查看mysql

![](https://img-blog.csdn.net/20160428094358653)

如果报错，内容含有
>`Error: Package: mysql-community-libs-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: systemd
Error: Package: mysql-community-server-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libstdc++.so.6(GLIBCXX_3.4.15)(64bit)
Error: Package: mysql-community-client-5.6.35-2.el7.x86_64 (mysql56-community)
           Requires: libc.so.6(GLIBC_2.17)(64bit)
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest`


解决：

>`#yum install glibc.i686`
>`# yum list libstdc++*`

####4、重置密码
重置密码前，首先要登录
>`# mysql -u root`

登录时有可能报这样的错：ERROR 2002 (HY000): Can’t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock’ (2)，原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：

>`# sudo chown -R openscanner:openscanner /var/lib/mysql`

如果报chown: 无效的用户: "openscanner:openscanner"错误，更换命令，并用 ll 查看目录权限列表
>`1.     chown root /var/lib/mysql/`
`2.     ll`

![](https://img-blog.csdn.net/20160428100032349)

附：
① 更改文件拥有者 (chown )
[root@linux ~]# chown 账号名称 文件或目录
② 改变文件的用户组用命令 chgrp
[root@linux ~]# chgrp 组名 文件或目录
③ 对于目录权限修改之后，默认只是修改当前级别的权限。如果子目录也要递归需要加R参数
Chown -R : 进行递归,连同子目录下的所有文件、目录

然后，重启服务：

>`service mysqld restart`

接下来登录重置密码：

>` mysql -u root -p

>`mysql > use mysql;`
`mysql > update user set password=password('123456') where user='root';`
`mysql > exit;`

>重启mysql服务后才生效 # service mysqld restart

必要时加入以下命令行，为root添加远程连接的能力。链接密码为 “root”（不包括双引号）

>`mysql> GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "root";`

####6、查询数据库编码格式，确保是 UTF-8
>`show variables like "%char%";`

![](https://img-blog.csdn.net/20160428102233187)

进入mysql命令行后 输入 

>`set names utf8;`

（测试数据库数据）
再进入数据库 use test;
在导入sql脚本 source test.sql;

####7、开放3306端口号
firewalld 防火墙（centos-7）运行命令,并重启：

>`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
`firewall-cmd --reload`

iptables 防火墙（centos6.5及其以前）运行命令
>`vim /etc/sysconfig/iptables`

在文件内添加下面命令行，然后重启
>`-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT`

>`# service iptables restart`

外部链接访问效果（一般建立sql数据库和数据表，建议通过远程链接控制，直观易操作）

![](https://img-blog.csdn.net/20160428105307246)

附：

出现“Warning: Using a password on the command line interface can be insecure.”的错误

第一种方法、修改数据库配置文件
1、我们需要修改数据库配置文件，这个要看我们数据库的配置的，有些是在/etc/my.cnf，有些是/etc/my.conf

![](https://img-blog.csdn.net/20160428105943780)

我们需要在[client]部分添加脚本：
>`socket=/var/lib/mysql/mysql.sock`  ( mysql.sock 文件位置 )
`  host=localhost`
 `user=数据库用户`
`password='数据库密码'`

这里参数要修改成我们自己的。

2、采用命令导出和导入数据库
其实在这个时候，我们如果采用”详解使用mysqldump命令备份还原MySQL数据用法整理[http://www.laozuo.org/5047.html](http://www.laozuo.org/5047.html)“介绍的方法也是可以使用的，虽然依旧有错误提示，但是数据库还是可以导出的。您肯定和老左一样是追求细节的人，一点点问题都不能有，但我们可以用下面的命令导出和导入，就没有错误提示。

导出数据库
>`mysqldump --defaults-extra-file=/etc/my.cnf database > database.sql`

导入数据库
>`mysql --defaults-extra-file=/etc/my.cnf database < database.sql`

这里我们可以看到上面的命令和以前常用的快速导入和导入命令有所不同了，需要加载我们配置的MYSQL配置文件，这个“/etc/my.cnf”要根据我们实际的路径修改。用这样的命令导出备份和导入是没有错误提示的。

登陆数据库
>`# mysql -u root -p`

###Centos 安装Tomcat

####a.下载tomcat linux的包，地址：[http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)，我们下载的版本是8.0，下载方式如图：
![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207115357730-1218185959.png)

b.因为tomcat的安装依赖于Java jdk，所以我们需要在判断linux系统下面是否安装jdk

　　　　b.1 使用（Xshell）连接到Linux系统下面

　　　　b.2 输入命令：java -version，如果显示jdk版本号，则证明已经安装，如果不显示，则证明没有安装，如果没有安装，请参考下面地址进行安装：[http://www.cnblogs.com/hanyinglong/p/5025635.html](http://www.cnblogs.com/hanyinglong/p/5025635.html) ，如图所示：

![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207114836277-1490667290.png)

a.通过上面准备工作之后，我们现在已经拥有了可以安装和发布的环境，如果没有，请自行查询安装。
　　b.然后在Xshell中使用命令跳转到local下面创建者自己的文件夹：kencery
　　　　b.1  cd usr/local/   mkdir kencery   cd kencery/
　　c.然后使用Xftp将tomcat复制到kencery文件夹下面，如图所示：
![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207115036668-797378328.png)

d.将上传的Tomcat8.0解压，解压之后重命名为tomcat，如图所示：
　　　　d.1 tar -zxv -f apache-tomcat-8.0.29.tar.gz 
　　　　d.2 mv apache-tomcat-8.0.29 tomcat
　　　　d.3 cd tomcat
![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207115438246-592817034.png)

e.解析完成后即可以启动Tomcat，检查是否安装成功，命令如下，如图所示：
　　　　/usr/local/kencery/tomcat/bin/startup.sh
　　　　
![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207115129418-1148488543.png)

出现如图上所示的信息，则表示启动成功。这时候我们可以在windows下面使用http://ip:8080访问，如能够显示Tomcat的主页，则表示不需要进行任何操作了，如不能显示，则需要在Linux中开放防火墙的8080端口。
　　f.在Linux下面的防火墙里面开放8080端口 会用命令如下：
　　　　f.1  vim /etc/sysconfig/iptables
　　　　f.2  打开之后按键盘（i）进入编辑模式，写入开发8080端口，如图所示：
![](https://images2015.cnblogs.com/blog/359161/201512/359161-20151207115156699-845018622.png)
f.3  写完之后我们按键盘（ESC）按钮退出，然后按（:wq）保存并且关闭Vim。
　　g.之后重启防火墙,命令如下：
　　　　service iptables restart 
　　h.然后再次在浏览器中输入http://ip:8080，如果看到tomcat系统界面，说明安装成功，你可以进行下一步了。
　　i.停止Tomcat的命令是：/usr/local/tomcat/bin/shutdown.sh
####Linux中设置tomcat的服务器启动和关闭
a.如2所示，我们已经完成了对tomcat的安装，接下来就可以部署项目，但是这里存在一个问题，那就是Linux的系统和重启我们每次都需要接路径并且执行命令，那么我们可以设置成service的形式来实现这个功能。
　　b.执行命令：vim /etc/rc.d/init.d/tomcat，创建脚本文件，在文件中写入如下代码，保存并且退出

```
#!/bin/bash
# /etc/rc.d/init.d/tomcat
# init script for tomcat precesses
# processname: tomcat
# description: tomcat is a j2se server
# chkconfig: 2345 86 16
# description: Start up the Tomcat servlet engine.

if [ -f /etc/init.d/functions ]; then
. /etc/init.d/functions
elif [ -f /etc/rc.d/init.d/functions ]; then
. /etc/rc.d/init.d/functions
else
echo -e "\atomcat: unable to locate functions lib. Cannot continue."
exit -1
fi
RETVAL=$?
CATALINA_HOME="/usr/local/kencery/tomcat"   #tomcat安装目录，你安装在什么目录下就复制什么目录
case "$1" in
start)
if [ -f $CATALINA_HOME/bin/startup.sh ];
then
echo $"Starting Tomcat"
$CATALINA_HOME/bin/startup.sh
fi
;;
stop)
if [ -f $CATALINA_HOME/bin/shutdown.sh ];
then
echo $"Stopping Tomcat"
$CATALINA_HOME/bin/shutdown.sh
fi
;;
*)
echo $"Usage: $0 {start|stop}"
exit 1
;;
esac
exit $RETVAL

Linux
```
c.给文件添加权限，使得脚本文件可以执行，命令为  chmod 755 /etc/rc.d/init.d/tomcat
　　d.将其添加到服务中，命令为 chkconfig --add /etc/rc.d/init.d/tomcat
　　e.然后将下面的配置文件加到tomcat中的catalina.sh文件中的最后面，命令为：vim /usr/local/kencery/tomcat/bin/catalina.sh
　　　　export JAVA_HOME=/usr/local/kencery/javajdk   #javajdk的安装路径，使用echo $JAVA_HOME命令可以读取
　　　　export CATALINA_HOME=/usr/local/kencery/tomcat
　　　　export CATALINA_BASE=/usr/local/kencery/tomcat
　　　　export CATALINA_TMPDIR=/usr/local/kencery/tomcat/temp
　　f.以上所有工作顺利进行并且没有报错，则配置完成，你可以输入命令service tomcat start和service tomcat stop进行验证(请自行实验)。
####4.Linux中设置tomcat的开机启动
a. 通过第三步的设置我们可以很方便的设置tomcat的启动和关闭，但是这里存在一个问题，那就是当服务器关机重启的时候，服务不能随计算机的启动而自己启动，那么我们可以将tomcat服务设置为开机启动。
　　b.打开linux设置开启启动的文件，将下面的配置文件写入此文件的最后，命令为：vim /etc/rc.d/rc.local
　　　　export JAVA_HOME=/usr/local/kencery/javajdk
　　　　export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
　　　　export PATH=$PATH:$JAVA_HOME/bin
　　　　export CATALINA_HOME=/usr/local/kencery/tomcat/
　　　　#tomcat自启动
　　　　/usr/local/kencery/tomcat/bin/startup.
　　c.tomcat依赖于Java的jdk，所以设置的时候讲jdk也同步导入。
　　d.完成上面的步骤之后我们就可以将centos关机重启检查一番。
#### 最后配置server.xml 增加utf-8编码
```
 <Connector URIEncoding="UTF-8" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

###Centos 7 安装zookeeper
 1、创建 /usr/local/services/zookeeper 文件夹：
    mkdir -p /usr/local/services/zookeeper

2、进入到 /usr/local/services/zookeeper 目录中：
    cd /usr/local/services/zookeeper

3、下载 zookeeper-3.4.9.tar.gz：
    wget [https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.9/zookeeper-3.4.9.tar.gz)

4、解压缩 zookeeper-3.4.9.tar.gz：
    tar -zxvf zookeeper-3.4.9.tar.gz

5、进入到 /usr/local/services/zookeeper/zookeeper-3.4.9/conf 目录中：
    cd zookeeper-3.4.9/conf/

6、复制 zoo_sample.cfg 文件的并命名为为 zoo.cfg：
    cp zoo_sample.cfg zoo.cfg

7、用 vim 打开 zoo.cfg 文件并修改其内容为如下：
```
  # The number of milliseconds of each tick

    # zookeeper 定义的基准时间间隔，单位：毫秒
    tickTime=2000

    # The number of ticks that the initial
    # synchronization phase can take
    initLimit=10
    # The number of ticks that can pass between
    # sending a request and getting an acknowledgement
    syncLimit=5
    # the directory where the snapshot is stored.
    # do not use /tmp for storage, /tmp here is just
    # example sakes.
    # dataDir=/tmp/zookeeper

    # 数据文件夹
    dataDir=/usr/local/services/zookeeper/zookeeper-3.4.9/data

    # 日志文件夹
    dataLogDir=/usr/local/services/zookeeper/zookeeper-3.4.9/logs

    # the port at which the clients will connect
    # 客户端访问 zookeeper 的端口号
    clientPort=2181

    # the maximum number of client connections.
    # increase this if you need to handle more clients
    #maxClientCnxns=60
    #
    # Be sure to read the maintenance section of the
    # administrator guide before turning on autopurge.
    #
    # [http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance)
    #
    # The number of snapshots to retain in dataDir
    #autopurge.snapRetainCount=3
    # Purge task interval in hours
    # Set to "0" to disable auto purge feature
    #autopurge.purgeInterval=1
```
8、保存并关闭 zoo.cfg 文件:
   
9、进入到 /usr/local/services/zookeeper/zookeeper-3.4.9/bin 目录中：
    cd ../bin/
 
10、用 vim 打开 /etc/ 目录下的配置文件 profile：
    vim /etc/profile
    并在其尾部追加如下内容：
```
 # idea - zookeeper-3.4.9 config start - 2016-09-08
 
    export ZOOKEEPER_HOME=/usr/local/services/zookeeper/zookeeper-3.4.9/
    export PATH=$ZOOKEEPER_HOME/bin:$PATH
    export PATH
 
    # idea - zookeeper-3.4.9 config start - 2016-09-08
```
11、使 /etc/ 目录下的 profile 文件即可生效：
    source /etc/profile

12、启动 zookeeper 服务：
    zkServer.sh start
    如打印如下信息则表明启动成功：
    ZooKeeper JMX enabled by default
    Using config: /usr/local/services/zookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED

13、查询 zookeeper 状态：
    zkServer.sh status

14、关闭 zookeeper 服务：
    zkServer.sh stop
    如打印如下信息则表明成功关闭：
    ZooKeeper JMX enabled by default
    Using config: /usr/local/services/zookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    Stopping zookeeper ... STOPPED

15、重启 zookeeper 服务：
    zkServer.sh restart
    如打印如下信息则表明重启成功：
    ZooKeeper JMX enabled by default
    Using config: /usr/local/services/zookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    ZooKeeper JMX enabled by default
    Using config: /usr/local/services/zookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    Stopping zookeeper ... STOPPED
    ZooKeeper JMX enabled by default
    Using config: /usr/local/services/zookeeper/zookeeper-3.4.9/bin/../conf/zoo.cfg
    Starting zookeeper ... STARTED

ZooKeeper学习总结  [http://www.linuxidc.com/Linux/2016-07/133179.htm](https://www.linuxidc.com/Linux/2016-07/133179.htm)

[Ubuntu](https://www.linuxidc.com/topicnews.aspx?tid=2 "Ubuntu") 14.04安装分布式存储Sheepdog+ZooKeeper  [http://www.linuxidc.com/Linux/2014-12/110352.htm](https://www.linuxidc.com/Linux/2014-12/110352.htm)

CentOS 6安装sheepdog 虚拟机分布式储存  [http://www.linuxidc.com/Linux/2013-08/89109.htm](https://www.linuxidc.com/Linux/2013-08/89109.htm)

ZooKeeper集群配置 [http://www.linuxidc.com/Linux/2013-06/86348.htm](https://www.linuxidc.com/Linux/2013-06/86348.htm)

使用ZooKeeper实现分布式共享锁 [http://www.linuxidc.com/Linux/2013-06/85550.htm](https://www.linuxidc.com/Linux/2013-06/85550.htm)

分布式服务框架 ZooKeeper -- 管理分布式环境中的数据 [http://www.linuxidc.com/Linux/2013-06/85549.htm](https://www.linuxidc.com/Linux/2013-06/85549.htm)

ZooKeeper集群环境搭建实践 [http://www.linuxidc.com/Linux/2013-04/83562.htm](https://www.linuxidc.com/Linux/2013-04/83562.htm)

ZooKeeper服务器集群环境配置实测 [http://www.linuxidc.com/Linux/2013-04/83559.htm](https://www.linuxidc.com/Linux/2013-04/83559.htm)

ZooKeeper集群安装 [http://www.linuxidc.com/Linux/2012-10/72906.htm](https://www.linuxidc.com/Linux/2012-10/72906.htm)

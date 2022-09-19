## Hadoop-3.1.0完全分布式

### 安装前准备

Tips:  “#” 后的都代表命令

#### 三台虚拟机

操作系统：CentOS 7

网络连接：桥接网络

1. master IP:192.168.1.100
2. slave01 IP:192.168.1.101
3. slave02 IP:192.168.1.102

#### 修改主机名

master

`# hostname master`

`# vim /etc/hostname`修改为master

` # hostnamectl`查看hostname

slave01

` # hostname slave01`

` # vim /etc/hostname`修改为slave01

` # hostnamectl`查看hostname

slave02

` # hostname slave02`

` # vim /etc/hostname`修改为slave02

`# hostnamectl`查看hostname



### 配置网络

#### 静态IP地址

master

`# vim /etc/sysconfig/network-scripts/ifcfg-ens33`

修改

```sh
BOOTPROTO="static"
ONBOOT="yes"
IPADDR="192.168.1.100"
NETMASK="255.255.255.0"
GATEWAY="192.168.1.1"
```

`# systemctl restart network`重启网络服务

slave01：与上述过程一致，IPADDR="192.168.1.101"

slave02：与上述过程一致，IPADDR="192.168.1.102"

#### DNS

可选，若要配置yum源建议配置

`# vim /etc/resolv.conf`

```
nameserver 114.114.114.114
nameserver 8.8.8.8
```

#### 配置hosts

master

`# vim /etc/hosts`编辑文件新加行

```shell
192.168.1.100	master
192.168.1.101	slave01
192.168.1.102	slave02
```

在master下

`# source /etc/hosts`

`# scp /etc/hosts root@slave01:/etc/`键入yes以及root用户密码

`# scp /etc/hosts root@slave02:/etc/`键入yes以及root用户密码

在slave01下

`# cat /etc/hosts`查看hosts是否和master修改的一致

在slave02下

`# cat /etc/hosts`查看hosts是否和master修改的一致

#### 配置yum源

根据需求配置网络yum源或本地yum源，以下配置为清华网络yum源

```
# sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org|baseurl=https://mirrors.tuna.tsinghua.edu.cn|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
         
# sudo yum clean all
# sudo yum makecache
```

#### 关闭防火墙

每个节点都需要执行

`# systemctl stop firewalld.service`

开机禁止启动防火墙服务

`# systemctl disable firewalld.service `

### 配置SSH

每台虚拟机安装ssh服务

`# yum install openssh-server`

在master生成ssh公钥

`# ssh-keygen -t rsa`连续回车生成公钥

`# cd ~/.ssh`进入ssh目录

`# cat id_rsa.pub >> authorized_keys`把公钥写入文件

在slave01、slave02创建.ssh文件夹

`# mkdir .ssh`

将authorized_keys传输到两个节点

`# scp authorized_keys slave01:~/.ssh/`

`# scp authorized_keys slave02:~/.ssh/`

测试ssh免密登录

```
[root@master .ssh]$ ssh master
Last login: Sat Mar 23 17:11:13 2019 from master
[root@master ~]$ exit
logout
Connection to master closed.
[root@master .ssh]$ ssh slave01
Last login: Sat Mar 23 17:04:46 2019 from master
[root@slave1 ~]$ exit
logout
Connection to slave1 closed.
[root@master .ssh]$ ssh slave02
Last login: Sat Mar 23 15:31:54 2019 from master
[root@slave2 ~]$ exit
logout
Connection to slave2 closed.
```

Tips: 注意使用exit命令中止ssh连接

### 时间同步

以下步骤三台虚拟机都需要执行

#### 安装ntp

`# yum install ntp`

#### 配置ntp服务

`# vim /etc/ntp.conf`

```
restrict 192.168.1.100 nomodify notrap nopeer noquery
restrict 192.168.1.1 mask 255.255.255.0 nomodify notrap
```

选择一个master为主节点修改其配置文件

`# vim /etc/ntp.conf`

注释server 0~n 并添加以下内容

```
server 127.127.1.0
Fudge 127.127.1.0 stratum 10
```

继续修改主节点外配置文件

`# vim /etc/ntp.conf`

注释server 0~n 并添加以下内容

```
server 192.168.1.100
Fudge 192.168.1.100 stratum 10
```

#### 启动ntp服务

`# service ntpd start`

`# ntpstat`

等待5～10分钟后执行出现以下内容表示成功

```
synchronised to NTP server (192.168.1.100) as stratum 等
```

#### 设置开机启动

`# chkconfig ntpd on`

#### 测试ntp服务

在master节点执行

`# date --date '12:30:00'`

在slave节点查看时间是否更改，同为12:30分则配置成功

` date`

### 配置Java环境

#### 卸载Open JDK

`# rpm -qa | grep java`

* rpm 是一种用于打包及安装工具

* -q 代表 query，a 代表 all
* grep: 用于文本搜索

会输出以下两个或以上文件

```
java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64
java-1.8.0-openjdk-headless-1.8.0.102-4.b14.el7.x86_64
```

删除名称中带openjdk的包

`# rpm -e --nodeps java-1.8.0-openjdk-1.8.0.102-4.b14.el7.x86_64`

`# rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.102-4.b14.el7.x86_64`

验证是否已经移除openjdk

`# java -version` 若输出的不是Java版本则表示成功

Tips: 请复制自己设备输出的内容

#### 安装Oracle JDK

创建应用程序文件夹

`mkdir /opt/app`

使用你熟悉的ftp软件上传jdk到master

将.gz后缀的jdk包解压缩

`# tar -zxvf jdk-8u201-linux-x64.tar.gz -C /opt/app/`

配置Java环境变量

`# vim /etc/profile`

```shell
# jdk
export JAVA_HOME=/opt/app/jdk1.8.0_201
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

使配置生效

`source /etc/profile`

测试配置是否成功

`java -version`

### 安装Hadoop

使用你熟悉的ftp软件上传Hadoop到master

将.gz后缀的Hadoop包解压缩

`# tar -zxvf hadoop-3.1.0.tar.gz -C /opt/app/`

进入Hadoop配置文件夹

`# cd /opt/app/hadoop-3.1.0/etc/hadoop`

#### 创建data文件夹

```
# cd /opt/app/hadoop-3.1.0
# mkdir -p data/tmp
# mkdir -p data/namenode
# mkdir -p data/datanode
```

#### 创建日志文件夹

```
cd /opt/app/hadoop-3.1.0
mkdir logs
```

#### 配置Hadoop环境变量

`# vim /etc/profile`

```shell
# hadoop
export HADOOP_HOME=/opt/app/hadoop-3.1.0
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

使配置生效

`source /etc/profile`

#### 修改hadoop-env.sh

`# vim hadoop-env.sh`

```shell
export JAVA_HOME=/opt/app/jdk1.8.0_201

export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
```

Tips: 注意jdk版本是否一致

#### 修改core-site.xml

`# vim core-site.xml`

```xml
<configuration>
	<!-- namenode -->
	<property>
		<name>fs.default.name</name>
		<value>hdfs://master:9000</value>
	</property>
	
	<!-- Hadoop回收周期 -->
	<property>
		<name>fs.trash.interval</name>
		<value>420</value>
	</property>
</configuration>
```

#### 修改hdfs-site.xml

`# vim hdfs-site.xml`

```xml
<configuration>
	<!-- 副本数量 -->
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
	
	<!-- namenode web管理端口 -->
	<property>
		<name>dfs.http.address</name>
		<value>master:50070</value>
	</property>
	
	<!-- namenode 物理路径 -->
	<property>
		<name>dfs.namenode.dir</name>
		<value>/opt/app/hadoop-3.1.0/data/namenode</value>
	</property>
	
	<!-- datanode 物理路径 -->
	<property>
		<name>dfs.datanode.dir</name>
		<value>/opt/app/hadoop-3.1.0/data/datanode</value>
	</property>
	
	<!-- 临时目录 -->
	<property>
		<name>dfs.tmp.dir</name>
		<value>/opt/app/hadoop-3.1.0/data/tmp</value>
	</property>
	
	<!-- secondary namenode web管理端口 -->
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>slave02:50090</value>
	</property>
</configuration>
```

#### 修改mapred-site.xml

`# vim mapred-site.xml`

```xml
<configuration>
	<!-- 使用yarn与服务端通信 -->
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
	
	<!-- jobhistory server -->
	<property>
		<name>mapreduce.jobhistory.address</name>
		<value>master:10020</value>
	</property>
	
	<!-- jobhistory server web管理端口 -->
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>master:19888</value>
	</property>
</configuration>
```

#### 修改yarn-site.xml

`# vim yarn-site.xml`

```xml
<configuration>

<!-- Site specific YARN configuration properties -->
	<!-- resourcemanager -->
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>slave01</value>
	</property>
	
	<!-- nodemanager运行附属服务 -->
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
	
	<!-- 开启日志聚集功能 -->
	<property>
		<name>yarn.log.aggregation-enable</name>
		<value>true</value>
	</property>
	
	<!-- 日志保存周期 -->
	<property>
		<name>yarn.log.aggregation.retain-seconds</name>
		<value>420</value>
	</property>
</configuration>
```

#### 修改workers

`# vim workers`

```
master
slave01
slave02
```

#### 同步应用程序

```
# scp -r /opt/app slave01:/opt/
# scp -r /opt/app slave02:/opt/
//执行时间会非常久，建议先上传好jdk和hadoop，再克隆主机后同步配置
//此过程适合新手，不易出错
```

#### 启动Hadoop

在master节点格式化

`# cd /opt/app/hadoop-3.1.0/bin`

`# hdfs namenode -format`

出现以下信息说明格式化成功

```
INFO common.Storage: Storage directory /opt/app/hadoop-3.1.0/namenode has been successfully formatted.
```

在master启动Hadoop服务

`# start-all.sh`

或

```
# start-dfs.sh
# start-yarn.sh
```

若要停止Hadoop服务

`# stop-all.sh`

或

```
# stop-dfs.sh
# stop-yarn.sh
```

启动JobHistoryServer

进入sbin目录

`# cd /opt/app/hadoop-3.1.0/sbin`

启动服务：`./mr-jobhistory-daemon.sh start historyserver`

停止服务：`./mr-jobhistory-daemon.sh stop historyserver`

查看Hadoop服务进程（master、slave都可以查询）

`# jps`

```
Jps
DataNode
NameNode
NodeManager
JobHistoryServer
```

浏览器访问以下链接，查看Hadoop服务

```
http://192.168.1.100:50070/
http://192.168.1.101:8088/
http://192.168.1.100:19888/
```

若要修改Hadoop配置，修改完成后需要同步配置至slave节点

`# scp -r /opt/app/hadoop-3.1.0/etc/hadoop slave01:/opt/app/hadoop-3.1.0/etc/`

`# scp -r /opt/app/hadoop-3.1.0/etc/hadoop slave02:/opt/app/hadoop-3.1.0/etc/`

Tips: Linux操作非常灵活，可以使用多种方式达到目的，灵活运用才是硬道理，代码不要一味复制粘贴，根据实际情况变化

#### 问题解决

HDFS进程单独启动

```
# hadoop-daemons.sh start datanode
# hadoop-daemon.sh start namenode/secondaaynamenode
```

Yarn进程单独启动

```
# yarn-daemons.sh start nodemanager
# yarn-daemon.sh start resourcemanager
```

关闭某些进程

``` 
# kill -s 9 进程号
```


# openEuler20安装openGauss2

> 2023年9月10日 Kai 未经授权，谢绝转载

本教程介绍了如何在 openEuler20上安装openGauss2，在openEuler22/23上已经可以自带openGauss，我推荐您使用22/23或更高版本

在openEuler20上安装openGauss2可能也适用在其他linux系统上，如CentOS、ubuntu等，其中可能有不同的系统设置和命令，还请您自行替换，如果您能写下（零基础起步）的教程就更好了

*由于主要在终端中进行操作，所以这里多是以code的形式展示，安装方法有很多种，这里只是例举其一二，具体内容可以自行搜索*

[返回主教程](./readme.md)

******************

进入openEuler操作台，使用远程工具连接系统（建议）

## 在开始安装前

开始安装前，我们需要了解一些东西

```
'#' 在井号后面的是注释/解释

vi "/root/..."  # vi后面跟上文本路径来编辑文本 （有些版本是 vim） vim 是 Linux 的文本编辑器（之一）
# 在 vim 中按下'i'进入编辑模式，按下 [Esc] 退出编辑模式，输入':'添加指令，'w'是write（写入）,'q'是退出

cat  # 也是一种编辑文件的命令

## 系统指令
su - omm  # 使用 su 切换用户，exit注销用户
mkdir  # 创建文件夹
tar  # 操作压缩包
cd  # 类似Windows 进入路径
```

另外建议各位给虚拟机做个备份（快照），虚拟机需要有网络（可以``ping baidu.com``测试）等条件就不赘述了

## 开始安装

1. 我们要先知道电脑主机名以及虚拟机的网络IP，在安装过程中我们已经设置好了

```
#进入命令窗口，这里的 openGuassMachine 就是我们设置的主机名
[root@openGuassMachine ~] #

# 输入 ifconfig 查看网卡（或者nmcli）
[root@openGuassMachine ~] # nmcli
# 输出类似下面（这里我使用了识图工具，有些地方没校对的请不要在意）
>>>cnp0s3: comected to enpes3
>>>		Intel 82540EM
>>>		ethernet (e1000)，08:0:2?:66:99:0?，hw，mtu 1508
>>>		ip4 default
>>>		inet4 10.0.2.15/24		# 10.0.2.15 就是系统的IP（之一）
>>>		route4 0.0.0.0/0
>>>		route4 10.0.2.0/24
>>>		inet6 fe88::9e17:50ec:ef26:65f9/64
>>>		route6 fe8B::/64
>>>		route6 ffeg::/8
>>>	......
```



2. 编写脚本

   *这里将逐句代码编写成了shell脚本，您也可以自己逐句运行*

   ```vi /root/openGuass_auto_install.sh  # 在root目录下创建shell脚本```

   使用 'i' 进入编辑模式，插入以下代码，主机名和IP地址要根据自己的实际情况修改（如果中文异常，请把中文删去）

```
#!/bin/bash

##########################################
# Author Kai
# openEuler 20.03 LTS
# 2023-9-8
##########################################

export MY_HOSTNAME=openGuassMachine             ## 主机名
export MY_HOSTIP=10.0.2.15						## IP地址

## 关闭防火墙
systemctl stop firewalld.service
systemctl disable firewalld.service
echo "[Info] closed firewall"

## 配置字符及环境变量
cat >>/etc/profile<<EOF
export LANG=en_US.UTF‐8
EOF
cat >>/etc/profile<<EOF
export packagePath=/opt/software/openGauss
EOF
source /etc/profile
cat >>/etc/profile<<EOF
export LD_LIBRARY_PATH=$packagePath/script/gspylib/clib:$LD_LIBRARY_PATH
EOF
source /etc/profile


## 关闭swap交换内存
swapoff -a


## 配置yum
mv /etc/yum.repos.d/openEuler_x86_64.repo /etc/yum.repos.d/openEuler_x86_64.repo.bak  # 备份原来的配置文件
curl -o /etc/yum.repos.d/openEuler_x86_64.repo  https://mirrors.huaweicloud.com/repository/conf/openeuler_x86_64.repo  # 下载“源”文件


## yum下载其他配置
yum install libaio* -y
yum install libnsl* -y
# 下载python
cd /usr/bin
mv python python.bak
ln -s python3 /usr/bin/python
cd ~

## 安装openGuass
mkdir -p /opt/software/openGauss
cd /opt/software/openGauss  					# 写入gauss配置文件
cat >>clusterconfig.xml<<EOF
<?xml version="1.0" encoding="UTF-8"?> 
<ROOT> 
	<!-- openGauss 整体信息 --> 
	<CLUSTER> 
		<PARAM name="clusterName" value="dbCluster" /> 
		<PARAM name="nodeNames" value="$MY_HOSTNAME" /> 
		<PARAM name="backIp1s" value="10.0.3.15"/>
		<PARAM name="gaussdbAppPath" value="/opt/gaussdb/app" /> 
		<PARAM name="gaussdbLogPath" value="/var/log/gaussdb" /> 
 		<PARAM name="gaussdbToolPath" value="/opt/huawei/wisequery" /> 
 		<PARAM name="corePath" value="/opt/opengauss/corefile"/> 
 		<PARAM name="clusterType" value="single-inst"/> 
	</CLUSTER> 
 	<!-- 每台服务器上的节点部署信息 --> 
	<DEVICELIST> 
 		<!-- node1 上的节点部署信息 --> 
 		<DEVICE sn="1000001"> 
 			<PARAM name="name" value="$MY_HOSTNAME"/> 
 			<PARAM name="azName" value="AZ1"/> 
 			<PARAM name="azPriority" value="1"/> 
			<!-- 如果服务器只有一个网卡可用，将 backIP1 和 sshIP1 配置成同一个 IP --> 
		<PARAM name="backIp1" value="$MY_HOSTIP"/> 
 		<PARAM name="sshIp1" value="$MY_HOSTIP"/> 
 
	<!--dbnode--> 
	<PARAM name="dataNum" value="1"/> 
	<PARAM name="dataPortBase" value="7654"/> 
	<PARAM name="dataNode1" value="/gaussdb/data/$MY_HOSTNAME"/> 
	</DEVICE> 
	</DEVICELIST> 
</ROOT>
EOF

# 下载安装包
wget https://opengauss.obs.cn-south-1.myhuaweicloud.com/2.0.0/x86_openEuler/openGauss-2.0.0-openEuler-64bit-all.tar.gz
tar -zxvf openGauss-2.0.0-openEuler-64bit-all.tar.gz
tar -zxvf openGauss-2.0.0-openEuler-64bit-om.tar.gz
chmod 755 -R /opt/software
cd /opt/software/openGauss/script
python gs_preinstall -U omm -G dbgrp -X /opt/software/openGauss/clusterconfig.xml

echo "[Info] Successfully"  						# 输出消息

```

​		使用 ```sh /root/openGuass_auto_install.sh  # 运行shell脚本```

​		在后段会要求设置 omm 账户密码，密码同样要求 3 种类型，不能过短。

​		使用``chmod 755 -R /opt/software  # 修改文件夹权限``

​		基本上已经完成了安装和系统的配置，您可以再搜索一下如何清理刚刚下载的安装包等

## 配置openGauss2（openEuler20）

这一步开始之前，我们先重启以下虚拟机，方法很多，可以使用命令``reboot``重启，（使用远程工具）重新进入操作系统后我们继续

```
# 使用 omm 账户进行配置
su - omm  # 登录 omm 账户

cd /opt/software/openGauss/script

# 数据库初始化
gs_install -X /opt/software/openGauss/clusterconfig.xml --gsinit-parameter="--encoding=UTF8" --dn-guc="max_process_memory=2GB" --dn-guc="shared_buffers=128MB" --dn-guc="bulk_write_ring_size=128MB" --dn-guc="cstore_buffers=16MB
# 过程中会要求输入密码（直接使用管理员密码，我也不知道他这里是不是要求管理员密码）
# 注意 clusterconfig.xml 文件的路径
###################
# 参数说明如下：
# encoding 设置字符集；
# max_process_memory 设置一个数据库节点可用的最大物理内存；
# shared_buffers 设置 openGauss 使用的共享内存大小；
# bulk_write_ring_size 大批量数据写入触发时，该操作使用的环形缓冲区大小；
# cstore_buffers 设置列存所使用的共享缓冲区的大小。
###################

exit # 退出 omm 账户（可选，防止下面编辑权限不够）

# 关闭防火墙
systemctl disable firewalld.service
systemctl stop firewalld.service
```

## 配置数据库访问

```
[root@openGaussMachine]
# 设置侦听地址和账户加密方式（这里使用我上面设置的安装路径，下同）
vi "/gaussdb/data/openGuassMachine/postgresql.conf"

# --------- 这里要编辑文件，编辑、保存方式不再赘述 ---------
# 找到 listen_addresses 改为 '*'
listen_addresses = '*'  # 设置侦听地址（这句不是命令，是样例）
# 找到 password_encryption_type 为 0
password_encryption_type = 0  # 设置加密方式（这句不是命令，是样例）
# ----------------------- 保存 -----------------------

# 设置访问许可
vi "/gaussdb/data/openGuassMachine/pg_hba.conf"
# ----------------------- 编辑 -----------------------
# 添加IP（这里设置允许全部IP），使用MD5加密
host	all		all		0.0.0.0/0		md5
# ----------------------- 保存 -----------------------

su - omm  # 进入 omm 账户（已经登登录的不用执行）

# 重启gauss服务
gs_om -t stop  # 关闭服务
gs_om -t start  # 开启服务

# 进入初始数据库 postgres （gauss安装完成后自带的数据库）
gsql -d postgres -p 7654 -r
# 7654 端口在刚刚 clusterconfig.xml 中设置了

# 数据库操作指南 [华为官方文档（下载）]( https://support.huaweicloud.com/usermanual-opengauss/GaussDB 用户指南_中国站上线.pdf)

# 变成 postgres=# 说明你已经进入了 postgres 数据库
show port;  # 可以查看端口（其实就是上面的7654）

# 你可以修改一下 omm 账户的密码（建议），使其变成MD5加密保存的密码
alter role omm identified by '新密码' replace '旧密码';

# 创建新用户，管理账户不能远程管理 'testuser' 用户名自取
CREATE USER testuser IDENTIFIED BY '你要设置的密码';
ALTER USER testuser SYSADMIN;  # 给予新账户管理权限

\q  # 退出数据库
```



**到此你就完成了 postgres数据库的配置**

![庆祝的表情包](./assets/success.jpeg)

[返回主教程](./readme.md)

---
layout: post
title: Oracle数据库静默安装
categories: Oracle
description: 多年前写的青涩文章，留个纪念！
keywords: Oracle, 静默安装
---

> 本文适用于想要在Linux上面安装Oracle数据库，但是却因为某些原因打不开图形界面的用户。

> 需要挂载当前Linux的ISO文件，[配置本地yum源](/2017/09/20/linux-yum-repos/)，用于安装依赖包，可以通过纯命令的模式安装Oracle数据库。

- 环境：RHEL6.5 + Oracle11g / CentOS6.3 + Oracle11g

- 使用软件：p13390677_112040_Linux-x86-64_1of7.zip / p13390677_112040_Linux-x86-64_2of7.zip

- 硬件需求：

- 1. 物理内存不少于1G
  2. 硬盘可以空间不少于5G
  3. swap分区空间不少于2G
  4. 支持256色以上显卡
  5. cpu主频不小于550mHZ

**注意：下文开头符号含义：# root用户执行，$ oracle用户执行，// 注释不要执行**

**注意：下文开头符号含义：# root用户执行，$ oracle用户执行，// 注释不要执行**



## 一、设置服务器

### 1.重建主机的Oracle用户、组。

```sh
//删除用户oracle
# userdel -r oracle
//创建组(-g 指定组id，一般不需要，除非有需求 (e.g. groupadd -g 500 oinstall))
# groupadd oinstall
# groupadd dba
//(-g 指定所属组，-G 指定所属副组，-p 指定密码)
# useradd -g oinstall -G dba -m oracle -p oracle
```

### 2.安装好Oracle 需要的rpm依赖包。

```shell
//检查没有安装的包
# rpm -q binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel glibc glibc-common glibc-devel gcc- gcc-c++ libaio-devel libaio libgcc libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel pdksh ksh
//安装依赖包
# yum install binutils compat-libstdc++-33 elfutils-libelf elfutils-libelf-devel glibc glibc-common glibc-devel gcc- gcc-c++ libaio-devel libaio libgcc libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel pdksh ksh
//注：pdksh没有安装，可以忽略。安装了ksh。本地yum源配置可参考文章开头引用的另一篇文章。
```

### 3.修改Linux配置文件limits.conf和sysctl.conf

```shell
# vi /etc/security/limits.conf
//下面的是文本内容
oracle               soft     nproc    2047
oracle               hard     nproc    16384
oracle               soft     nofile   1024
oracle               hard     nofile   65536
oracle               soft     stack    10240
 
# vi /etc/sysctl.conf
//下面的是文本内容
//XXXXXXXXXX 代表共享内存字节数(一般75%物理内存,其中(512M)536870912 (2G)2147483648)
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = XXXXXXXXXX
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048586
 
//注：重启主机或者输入命令 sysctl -p 生效当前配置
```

## 二、安装数据库

### 1.安装前准备

```shell
//计划安装位置：/u02/app/oracle
//数据存储目录：/u02/oradata
//安装包位置：  /u02/soft
//创建文件夹并修改所属用户、组
# mkdir -p /u02/app/oracle && chown -R oracle:oinstall /u02/app
# mkdir -p /u02/soft && chown -R oracle:oinstall /u02/soft
//切换用户
# su oracle
$ cd /u02/soft
//解压安装包
$ unzip p13390677_112040_Linux-x86-64_1of7.zip
$ unzip p13390677_112040_Linux-x86-64_2of7.zip
```

### 2.初步处理应答文件

```shell
//2.1 先备份原应答文件
$ cd /u02/soft/database/response
$ mkdir rspbak
$ cp *.rsp ./rspbak
 
//2.2 删除应答文件中的注释行(以#开头)，vi编辑替换或者直接使用sed命令快速替换
$ sed -i 's/^#.*$//g' *.rsp
 
//2.3 刪除沒有內容的空行(^$)，vi编辑替换或者直接使用sed命令快速替换
$ sed -i '/^$/d' *.rsp
```

### 3.静默安装数据库软件

```shell
//3.1 编辑db_install.rsp文件
$ vi /u02/soft/database/response/db_install.rsp
//这里只是展示文件内容以供对比
oracle.install.responseFileVersion=/oracle/install/rspfmt_dbinstall_response_schema_v11_2_0
oracle.install.option=INSTALL_DB_SWONLY
ORACLE_HOSTNAME=localhost.localdomain
UNIX_GROUP_NAME=oinstall
INVENTORY_LOCATION=/u02/app/oraInventory
SELECTED_LANGUAGES=en,zh_CN
ORACLE_HOME=/u02/app/oracle/product/11.2.0/dbhome_1
ORACLE_BASE=/u02/app/oracle
oracle.install.db.InstallEdition=EE
oracle.install.db.EEOptionsSelection=false
oracle.install.db.optionalComponents=oracle.rdbms.partitioning:11.2.0.4.0,oracle.oraolap:11.2.0.4.0,oracle.rdbms.dm:11.2.0.4.0,oracle.rdbms.dv:11.2.0.4.0,oracle.rdbms.lbac:11.2.0.4.0,oracle.rdbms.rat:11.2.0.4.0
oracle.install.db.DBA_GROUP=dba
oracle.install.db.OPER_GROUP=
oracle.install.db.CLUSTER_NODES=
oracle.install.db.isRACOneInstall=
oracle.install.db.racOneServiceName=
oracle.install.db.config.starterdb.type=
oracle.install.db.config.starterdb.globalDBName=
oracle.install.db.config.starterdb.SID=
oracle.install.db.config.starterdb.characterSet=AL32UTF8
oracle.install.db.config.starterdb.memoryOption=true
oracle.install.db.config.starterdb.memoryLimit=
oracle.install.db.config.starterdb.installExampleSchemas=false
oracle.install.db.config.starterdb.enableSecuritySettings=true
oracle.install.db.config.starterdb.password.ALL=
oracle.install.db.config.starterdb.password.SYS=
oracle.install.db.config.starterdb.password.SYSTEM=
oracle.install.db.config.starterdb.password.SYSMAN=
oracle.install.db.config.starterdb.password.DBSNMP=
oracle.install.db.config.starterdb.control=DB_CONTROL
oracle.install.db.config.starterdb.gridcontrol.gridControlServiceURL=
oracle.install.db.config.starterdb.automatedBackup.enable=false
oracle.install.db.config.starterdb.automatedBackup.osuid=
oracle.install.db.config.starterdb.automatedBackup.ospwd=
oracle.install.db.config.starterdb.storageType=
oracle.install.db.config.starterdb.fileSystemStorage.dataLocation=
oracle.install.db.config.starterdb.fileSystemStorage.recoveryLocation=
oracle.install.db.config.asm.diskGroup=
oracle.install.db.config.asm.ASMSNMPPassword=
MYORACLESUPPORT_USERNAME=
MYORACLESUPPORT_PASSWORD=
SECURITY_UPDATES_VIA_MYORACLESUPPORT=
DECLINE_SECURITY_UPDATES=true
PROXY_HOST=
PROXY_PORT=
PROXY_USER=
PROXY_PWD=
PROXY_REALM=
COLLECTOR_SUPPORTHUB_URL=
oracle.installer.autoupdates.option=
oracle.installer.autoupdates.downloadUpdatesLoc=
AUTOUPDATES_MYORACLESUPPORT_USERNAME=
AUTOUPDATES_MYORACLESUPPORT_PASSWORD=
 
//3.2 静默安装软件
$ cd /u02/soft/database/
$ ./runInstaller -silent -force -noconfig -responseFile /u02/soft/database/response/db_install.rsp
 
//安装成功应有的输出提示：
Starting Oracle Universal Installer...
 
Checking Temp space: must be greater than 120 MB.   Actual 21313 MB    Passed
Checking swap space: must be greater than 150 MB.   Actual 4015 MB    Passed
Preparing to launch Oracle Universal Installer from /tmp/OraInstall2015-11-30_11-40-10AM. Please wait ...[oracle@JY-DB01 database]$ [WARNING] [INS-13014] Target environment do not meet some optional requirements.
   CAUSE: Some of the optional prerequisites are not met. See logs for details. /u01/app/oraInventory/logs/installActions2015-11-30_11-40-10AM.log
   ACTION: Identify the list of failed prerequisite checks from the log: /u01/app/oraInventory/logs/installActions2015-11-30_11-40-10AM.log. Then either from the log file or from installation manual find the appropriate configuration to meet the prerequisites and fix it manually.
You can find the log of this install session at:
 /u01/app/oraInventory/logs/installActions2015-11-30_11-40-10AM.log
The installation of Oracle Database 11g was successful.
Please check '/u01/app/oraInventory/logs/silentInstall2015-11-30_11-40-10AM.log' for more details.
 
As a root user, execute the following script(s):
        1. /u02/app/oracle/product/11.2.0/dbhome_1/root.sh
 
 
Successfully Setup Software.
 
//3.3 按提示root用户执行脚本
# /u02/app/oracle/product/11.2.0/dbhome_1/root.sh
 
Check /u02/app/oracle/product/11.2.0/dbhome_1/install/root_JY-DB01_2015-11-30_11-48-22.log for the output of root script
//注意：如果机器之前没有安装其他数据库，这里就应该是提示执行两个脚本，按具体提示执行即可。
 
//3.4 配置环境变量，下文的jdcwsbs可按需更改。
$ vi /home/oracle/jdcwsbs
 
export ORACLE_SID=jdcwsbs
export ORACLE_BASE=/u02/app/oracle
export ORACLE_HOME=/u02/app/oracle/product/11.2.0/dbhome_1
export NLS_LANG="SIMPLIFIED CHINESE_CHINA.AL32UTF8"
export NLS_DATE_FORMAT="YYYY-MM-DD HH24:Mi:SS"
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
export PATH=$ORACLE_HOME/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/oracle/bin
echo "--------------------------"
echo "ORACLE_BASE"=$ORACLE_BASE
echo "ORACLE_HOME"=$ORACLE_HOME
echo "ORACLE_SID"=$ORACLE_SID
echo "--------------------------"
echo "NLS_LANG"=$NLS_LANG
echo "NLS_DATE_FORMAT"=$NLS_DATE_FORMAT
echo "LD_LIBRARY_PATH"=$LD_LIBRARY_PATH
echo "PATH"=$PATH
echo "--------------------------"
 
//执行source jdcwsbs即可切换到新数据库环境变量
$ source jdcwsbs 
//下面是执行命令之后返回的信息
--------------------------
ORACLE_BASE=/u02/app/oracle
ORACLE_HOME=/u02/app/oracle/product/11.2.0/dbhome_1
ORACLE_SID=jdcwsbs
--------------------------
NLS_LANG=american_america.ZHS16GBK
NLS_DATE_FORMAT=YYYY-MM-DD HH24:Mi:SS
LD_LIBRARY_PATH=/u02/app/oracle/product/11.2.0/dbhome_1/lib
PATH=/u02/app/oracle/product/11.2.0/dbhome_1/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/oracle/bin
--------------------------
//注意：如果需要默认用户变量，则oracle用户配置~/.bash_profile文件。
```

### 4.静默安装监听

```shell
//4.1 编辑netca.rsp文件
$ vi /u02/soft/database/response/netca.rsp
//这里只是展示文件内容以供对比
[GENERAL]
RESPONSEFILE_VERSION="11.2"
CREATE_TYPE="CUSTOM"
[oracle.net.ca]
INSTALLED_COMPONENTS={"server","net8","javavm"}
INSTALL_TYPE=""typical""
LISTENER_NUMBER=1
LISTENER_NAMES={"LISTENER"}
LISTENER_PROTOCOLS={"TCP;1521"}
LISTENER_START=""LISTENER""
NAMING_METHODS={"TNSNAMES","ONAMES","HOSTNAME"}
NSN_NUMBER=1
NSN_NAMES={"EXTPROC_CONNECTION_DATA"}
NSN_SERVICE={"PLSExtProc"}
NSN_PROTOCOLS={"TCP;HOSTNAME;1521"}
 
//4.2 静默创建监听
$ $ORACLE_HOME/bin/netca /silent /responsefile /u02/soft/database/response/netca.rsp
 
Parsing command line arguments:
    Parameter "silent" = true
    Parameter "responsefile" = /u02/soft/database/response/netca.rsp
Done parsing command line arguments.
Oracle Net Services Configuration:
Profile configuration complete.
Oracle Net Listener Startup:
    Running Listener Control: 
      /u02/app/oracle/product/11.2.0/dbhome_1/bin/lsnrctl start LISTENER
    Listener Control complete.
    Listener started successfully.
Listener configuration complete.
Oracle Net Services configuration successful. The exit code is 0
```

### 5.静默dbca建库

```shell
//5.1 编辑dbca.rsp文件
$ vi /u02/soft/database/response/dbca.rsp
//这里只是展示文件内容以供对比
[GENERAL]
RESPONSEFILE_VERSION = "11.2.0"
OPERATION_TYPE = "createDatabase"
[CREATEDATABASE]
GDBNAME = "jdcwsbs"
SID = "jdcwsbs"
TEMPLATENAME = "General_Purpose.dbc"
CHARACTERSET = "AL32UTF8"
MEMORYPERCENTAGE = "60"
EMCONFIGURATION = "LOCAL"
SYSPASSWORD = "oracle"
SYSTEMPASSWORD = "oracle"
DBSNMPPASSWORD = "oracle"
SYSMANPASSWORD = "oracle"
[createTemplateFromDB]
SOURCEDB = "myhost:1521:orcl"
SYSDBAUSERNAME = "system"
TEMPLATENAME = "My Copy TEMPLATE"
[createCloneTemplate]
SOURCEDB = "orcl"
TEMPLATENAME = "My Clone TEMPLATE"
[DELETEDATABASE]
SOURCEDB = "orcl"
[generateScripts]
TEMPLATENAME = "New Database"
GDBNAME = "orcl11.us.oracle.com"
[CONFIGUREDATABASE]
[ADDINSTANCE]
DB_UNIQUE_NAME = "orcl11g.us.oracle.com"
NODELIST=
SYSDBAUSERNAME = "sys"
[DELETEINSTANCE]
DB_UNIQUE_NAME = "orcl11g.us.oracle.com"
INSTANCENAME = "orcl11g"
SYSDBAUSERNAME = "sys"
 
//上面就可以成功建库，但绝大多数情况[CREATEDATABASE]下还需要指定一些其他参数，因为默认的可能不符合实际要求，尤其是你创建的数据库字符集必须要按你的设计需求显示指定。
 
//本次规划数据库存储目录：/u02/oradata
# mkdir -p /u02/oradata && chown oracle:oinstall /u02/oradata
//修改引用的通用模板General_Purpose.dbc
$ cd $ORACLE_HOME/assistants/dbca/templates/
$ cp General_Purpose.dbc General_Purpose.dbc.bak
//vi替换{ORACLE_BASE}/oradata为新的存储路径/u02/oradata(需要打开文件)
$ vi General_Purpose.dbc
:%s#{ORACLE_BASE}/oradata#/u02/oradata#g
//或者sed直接快速替换(不需要打开文件)
sed -i 's#{ORACLE_BASE}/oradata#/u02/oradata#g' General_Purpose.dbc
 
//5.2 静默创建数据库
$ $ORACLE_HOME/bin/dbca -silent -responseFile /u02/soft/database/response/dbca.rsp
 
Enter SYS user password: 
 
Enter SYSTEM user password: 
 
Copying database files
1% complete
3% complete
11% complete
18% complete
26% complete
37% complete
Creating and starting Oracle instance
40% complete
45% complete
50% complete
55% complete
56% complete
60% complete
62% complete
Completing Database Creation
66% complete
70% complete
73% complete
85% complete
96% complete
100% complete
Look at the log file "/u02/app/oracle/cfgtoollogs/dbca/jdcwsbs/jdcwsbs.log" for further details.
//注意：如果已经在响应文件中配置sys和system密码，上面就不会提示你输入密码了。
```

### 6.静默dbca删库(看清楚，删除数据库，千万不要建完就删啊^_^)

```shell
//6.1 静默删除数据库
$ dbca -silent -deleteDatabase -sourceDB jdcwsbs -sysDBAUserName sys -sysDBAPassword oracle
 
Connecting to database
4% complete
9% complete
14% complete
19% complete
23% complete
28% complete
47% complete
Updating network configuration files
48% complete
52% complete
Deleting instance and datafiles
76% complete
100% complete
Look at the log file "/u02/app/oracle/cfgtoollogs/dbca/jdcwsbs.log" for further details.
```

祝贺自己安装完成，开始使用吧！！！


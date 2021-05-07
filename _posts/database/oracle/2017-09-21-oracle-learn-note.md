---
layout: post
title: Oracle数据库学习笔记
categories: Oracle
description: 多年前写的青涩文章，留个纪念！
keywords: Oracle
---

> 平时工作的时候使用的是Oracle数据库。

> 虽然刚工作时从来没有接触过，但是现在windows和linux的安装以及常用的一些功能早已不在话下，本篇文章将会记录下以后的学习以及经常使用到的一些命令，方便他人，同时自己查阅也方便了不少，闲话少说，开始完善我的学习笔记吧！

> 今天是2020-01-21日，时隔两年多，还是觉得服务器闲着吃灰有点不值得，就把之前的文章都搬了过来，以后继续加油吧，坚持下去！

> 2017-09-21开始记录
>
> 2017-09-28添加sys和system用户解锁（第13条）
>
> 2017-10-12添加修改用户密码并设置密码永久有效（第14条）
>
> 2020-01-21搬运
>
> 2021-04-21最后一次搬运了吧，我觉得以后不会再搬了！



```sql
-- 1.CASE WHEN 语句
-- 含义 当SEX=1 返回男，当SEX=2 返回女，其他情况 返回其他。
-- 简单Case函数
CASE SEX
WHEN '1' THEN '男'
WHEN '2' THEN '女'
ELSE '其他' END
-- Case搜索函数
CASE WHEN SEX = '1' THEN '男'
WHEN SEX = '2' THEN '女'
ELSE '其他' END
 
-- 2.DECODE 语句
-- 含义：如果条件=值1，则返回结果1，如果条件=值2，则返回结果2，... 都不满足则返回默认结果。
DECODE(条件, 值1, 结果1, 值2, 结果2,... 值N, 结果N, 默认结果)
 
-- 3.NVL函数 格式如下
-- 含义：如果expr1为空那么返回expr2，如果expr1不为空则返回expr1。
NVL(expr1,expr2) 
 
-- 4.清除表中所有数据
truncate table xxx;
delete from xxx;
 
-- 5.创建同义词
create or replace synonym DM_GY_YHHB for DM_GY_YHHB@gt3;
 
-- 6.查看数据库数据链路
select * from dba_db_links;
-- 创建公有的数据链路（public）
create public database link GT3
    connect to username identified by "password" 
    using '
    (DESCRIPTION =
        (ADDRESS_LIST =
            (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
        )
        (CONNECT_DATA =
            (SERVICE_NAME = xxx)
        )
    )';
-- 删除公有的数据链路（public）
drop public database link GT3;
 
-- 7.查看数据库服务器编码
select userenv('language') from dual;
 
-- 8.创建表空间
create tablespace xxxx_data
logging --启用记录日志
datafile 'D:\app\Administrator\oradata\orcl\xxxx_data.dbf'
size 100M
autoextend on
extent management local uniform size 128k
segment space management auto;
-- 创建临时表空间
create temporary tablespace xxxx_temp
tempfile 'D:\app\Administrator\oradata\orcl\xxxx_temp.dbf'
size 100M
extent management local;
--创建用户 指定密码
create user xxxx identified by xxxx
default tablespace xxxx_data
temporary tablespace xxxx_temp;
-- 给用户分配权限
grant connect,resource,dba to xxxx;
 
-- 9.删除用户
drop user xxx cascade;
-- 如果用户无法删除，查看用户连接情况
select username, sid, serial# from v$session where username='xxx';
-- 杀掉此用户进程资源
alter system kill session'sid, serial#';
 
-- 10.查看用户表空间在硬盘位置
select t1.name,t2.name from v$tablespace t1,v$datafile t2 where t1.ts# = t2.ts#;
-- 删除表空间
drop tablespace jsptbszd_data;
drop tablespace jsptbszd_data including contents and datafiles;
 
-- 11.在所有表里找某一个列名
SELECT * FROM USER_TAB_COLUMNS WHERE COLUMN_NAME = UPPER('列名');
 
-- 12.导入导出expdp和impdp的使用
-- 查询所有目录
select * from dba_directories;
-- 删除目录
--DROP DIRECTORY DB_BAK;
-- 创建目录
create directory db_bak as 'C:\estdump';
-- 授权(需要使用其他用户)
grant read,write on directory db_bak to gscgs;
-- 按照表空间导出，查询表空间
select * from dba_data_files;
--导出
expdp gscgs/gscgs DIRECTORY=db_bak DUMPFILE=gscgs20170622.dmp TABLESPACES=GSCGS_DATA,GSCGS_DATA_PART2014,GSCGS_DATA_PART2015,GSCGS_DATA_PART2016,GSCGS_DATA_PART2017,GSCGS_DATA_PART2018,GSCGS_DATA_PART2019,GSCGS_DATA_B_PART2015,GSCGS_DATA_B_PART2016,GSCGS_DATA_B_PART2017,GT3_DATA;
--导入
impdp gscgs/gscgs@oraname(需配置) DIRECTORY=db_bak DUMPFILE=gscgs20170622.dmp TABLESPACES=GSCGS_DATA,GSCGS_DATA_PART2014,GSCGS_DATA_PART2015,GSCGS_DATA_PART2016,GSCGS_DATA_PART2017,GSCGS_DATA_PART2018,GSCGS_DATA_PART2019,GSCGS_DATA_B_PART2015,GSCGS_DATA_B_PART2016,GSCGS_DATA_B_PART2017,GT3_DATA;
 
-- 13.解锁sys和system用户
-- 打开cmd输入
sqlplus / as sysdba
sql> alter user system account unlock;
 
-- 14.修改用户密码，设置密码永久有效(默认180天)
-- 打开cmd输入
sqlplus / as sysdba
sql> alter user 用户名 identified by 新密码;
sql> ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED ;
```


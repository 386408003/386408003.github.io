---
layout: post
title: Oracle数据库学习笔记
categories: Oracle
description: 多年前写的青涩文章，留个纪念！
keywords: Oracle
---

> 平时工作的时候使用的是Oracle数据库。

> 虽然刚工作时从来没有接触过，但是现在 windows 和 linux 的安装以及常用的一些功能早已不在话下，本篇文章将会记录下以后的学习以及经常使用到的一些命令，方便他人，同时自己查阅也方便了不少，闲话少说，开始完善我的学习笔记吧！

> 今天是2020-01-21日，时隔两年多，还是觉得服务器闲着吃灰有点不值得，就把之前的文章都搬了过来，以后继续加油吧，坚持下去！

> 2017-09-21 开始记录
>
> 2017-09-28 添加 sys 和 system 用户解锁
>
> 2017-10-12 添加修改用户密码并设置密码永久有效
>
> 2020-01-21 搬运
>
> 2021-04-21 最后一次搬运了吧，我觉得以后不会再搬了！
>
> 2021-05-18 添加 xxx() over([partition by] order by) 句型详解



## 一、数据库操作

### 1. 表空间

```sql
-- 创建表空间
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

-- 查看用户表空间在硬盘位置
select t1.name,t2.name from v$tablespace t1,v$datafile t2 where t1.ts# = t2.ts#;

-- 删除表空间
drop tablespace jsptbszd_data;
drop tablespace jsptbszd_data including contents and datafiles;
```

### 2. 用户

```sql
--创建用户并指定密码
create user xxxx identified by xxxx
default tablespace xxxx_data
temporary tablespace xxxx_temp;

-- 给用户分配权限
grant connect,resource,dba to xxxx;

-- 删除用户
drop user xxxx cascade;

-- 如果用户无法删除，查看用户连接情况
select username, sid, serial# from v$session where username='xxx';
-- 杀掉此用户进程资源
alter system kill session'sid, serial#';
```

### 3. 查看数据库服务器编码

```sql
-- 查看数据库服务器编码
select userenv('language') from dual;
```

### 4. 同义词

```sql
-- 创建同义词
create [or replace] [public] synonym DM_GY_YHHB for DM_GY_YHHB@gt3;
-- 删除同义词
drop [public] synonym DM_GY_YHHB;
```

### 5. 数据链路

```sql
-- 查看数据库数据链路
select * from dba_db_links;
-- 创建公有的数据链路（public）
create [public] database link GT3
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
drop [public] database link GT3;
```

### 6. 清除表中所有数据

```sql
-- 清除表中所有数据
delete from DM_GY_YHHB;
truncate table DM_GY_YHHB;
```

### 7. 按照列名查找表

```sql
-- 在所有表里找某一个列名
SELECT * FROM USER_TAB_COLUMNS WHERE COLUMN_NAME = UPPER('列名');
```

### 8. 解锁用户

```sql
-- 解锁 sys 和 system 用户
-- 打开cmd输入
sqlplus / as sysdba
sql> alter user system account unlock;
```

### 9. 修改密码，设置密码失效时间

```sql
-- 修改用户密码，设置密码永久有效(默认180天)
-- 打开cmd输入
sqlplus / as sysdba
sql> alter user 用户名 identified by 新密码;
sql> ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
```

### 10. 数据备份

```sql
-- 备份表到另一个表(丢失索引等信息)
create table dm_gy_swjg_old as select * from dm_gy_swjg_new;

-- 导入导出 expdp 和 impdp 的使用
-- 查询所有目录
select * from dba_directories;
-- 删除目录
drop directory db_bak;
-- 创建目录
create directory db_bak as 'C:\testdump';
-- 授权(需要使用其他 dba 用户)
grant read,write on directory db_bak to gscgs;
-- 按照表空间导出，查询表空间
select * from dba_data_files;
--导出
expdp gscgs/gscgs DIRECTORY=db_bak DUMPFILE=gscgs20170622.dmp TABLESPACES=GSCGS_DATA,GSCGS_DATA_PART2019,GT3_DATA;
--导入(oraname为要导入的数据库链路)
impdp gscgs/gscgs@oraname DIRECTORY=db_bak DUMPFILE=gscgs20170622.dmp TABLESPACES=GSCGS_DATA,GSCGS_DATA_PART2019,GT3_DATA;
```

### 11. 模拟 Mysql 主键自增

```sql
-- 表名
SELECT * FROM SYS_MENU;
-- 创建自增序列
CREATE SEQUENCE SEQ_SYS_MENU_ID
MINVALUE 1
NOMAXVALUE
INCREMENT BY 1
START WITH 1
NOCACHE;
-- 创建表数据插入前的触发器，主键的值从触发器获取
CREATE OR REPLACE TRIGGER MENU_INSERT_TRIGGER
  BEFORE INSERT ON SYS_MENU
  FOR EACH ROW
BEGIN
  SELECT SEQ_SYS_MENU_ID.NEXTVAL INTO :NEW.MENU_ID FROM DUAL;
END;
/
-- 最后的斜杠不能少
```

### 12. Oracle 中的分号和斜杠

```sql
-- ; 分号表示一个语句的结束
-- / 表示执行前面的一个代码块
-- commit; 表示提交一个事务
-- 命令窗口下，普通语句同时使用 ; 和 / 会导致语句执行两次。
```



## 二、基础语法

### 1. CASE WHEN 语句

```sql
-- 含义：当 SEX=1 返回男，当 SEX=2 返回女，其他情况 返回其他。
-- 简单 Case 函数
CASE SEX
WHEN '1' THEN '男'
WHEN '2' THEN '女'
ELSE '其他' END
-- Case 搜索函数
CASE WHEN SEX = '1' THEN '男'
WHEN SEX = '2' THEN '女'
ELSE '其他' END
```

### 2. DECODE 语句

```sql
-- 含义：如果条件=值1，则返回结果1，如果条件=值2，则返回结果2，... 都不满足则返回默认结果。
DECODE(条件, 值1, 结果1, 值2, 结果2,... 值N, 结果N, 默认结果)
```

### 3. NVL函数 格式如下

```sql
-- 含义：如果expr1为空那么返回expr2，如果expr1不为空则返回expr1。
NVL(expr1,expr2) 
```

### 4. xxx() over([partition by] order by) 句型详解

**此小结可以说是以下几篇文章的总结，图片是文章中自带图片。**

- https://www.cnblogs.com/qiuting/p/7880500.html
- https://blog.csdn.net/xue_yanan/article/details/80047050
- https://www.cnblogs.com/cnmarkao/p/3756876.html



1. xxx() 函数有如下取值：

   - row_number()：会为查询出来的每一行记录生成一个序号，依次排序且不会重复，必须要用 over 子句选择对某一列进行排序才能生成序号。

   - rank()：对查询出来的记录进行排名， over 子句中排序字段值相同的序号是一样的，产生的序号**不连续**。

   - dense_rank()：与 rank 函数类似，但是生成序号时是**连续**的。

   - ntile()：将有序分区中的行分发到指定数目的组中，各个组有编号，类似分区，可与 partition by 同时使用。

```sql
select * from student;
```

![](/images/posts/database/oracle/1-150417091946411.png)

```sql
-- row_number() 顺序排序
select name,course,row_number() over(partition by course order by score desc) rank from student;
```

![](/images/posts/database/oracle/1-20171122173313899.png)

```sql
-- rank() 跳跃排序，如果有两个第一级别时，接下来是第三级别
select name,course,rank() over(partition by course order by score desc) rank from student;
```

![](/images/posts/database/oracle/1-20171122173252321.png)

```sql
-- dense_rank() 连续排序，如果有两个第一级别时，接下来是第二级别
select name,course,dense_rank() over(partition by course order by score desc) rank from student;
```

![](/images/posts/database/oracle/1-20171122173221118.png)

```sql
-- ntile() 将有序分区中的行分发到指定数目的组中
```

![](/images/posts/database/oracle/1-281305144162381.png)

![](/images/posts/database/oracle/1-281305149001066.png)

![](/images/posts/database/oracle/1-281305155255707.png)

2. partition by 分区：

```sql
select sno,sname,course,score,rank() over(order by score desc) as 名次 from student;
```

![](/images/posts/database/oracle/1-201804231108048.png)

```sql
select sno,sname,course,score,rank() over(partition by course order by score desc) as 名次 from student;
```

![](/images/posts/database/oracle/1-20180423110820873.png)

```sql
select sno,sname,course,score,dense_rank() over(partition by course order by score desc) as 名次 from student;
```

![](/images/posts/database/oracle/1-20180423110838444.png)


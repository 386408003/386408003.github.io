---
layout: post
title: Oracle字符集查看以及修改
categories: Oracle
description: 多年前写的青涩文章，留个纪念！
keywords: Oracle, Charset
---

> 影响Oracle数据库字符集最重要的参数是NLS_LANG参数。

> 它的格式如下：NLS_LANG=language_territory.charset它有三个组成部分(语言、地域和字符集)，每个成分控制了NLS子集的特性，其中：

- Language：指定服务器消息的语言，影响提示信息是中文还是英文
- Territory：指定服务器的日期和数字格式
- Charset：指定字符集

> 如:AMERICAN_ AMERICA. ZHS16GBK 从NLS_LANG的组成我们可以看出，**真正影响数据库字符集的其实是第三部分**。



## 一、客户端和服务端字符集查看

```sql
--服务端字符集查看
select userenv('language') from dual;
--结果类似如下:AMERICAN_AMERICA.ZHS16GBK
```

```shell
客户端字符集查看
1)UNIX环境($ 是linux提示符)
$ echo $NLS_LANG
AMERICAN_AMERICA.ZHS16GBK

2)Windows环境
就是注册表里面相应OracleHome的NLS_LANG。
还可以在dos窗口里面自己设置，比如：
set nls_lang=AMERICAN_AMERICA.ZHS16GBK。

3)查询dmp文件的字符集
dmp文件的第2和第3个字节记录了dmp文件的字符集。查看第2第3个字节的内容即可。
如0354，然后用以下SQL查出它对应的Oracle字符集:
select nls_charset_name(to_number('0354','xxxx')) from dual;
结果类似如下:ZHS16GBK
在unix平台下可直接查看
cat exp.dmp |od -x|head -1|awk '{print $2 $3}'|cut -c 3-6
```



## 二、客户端和服务端字符集修改

```sql
--服务端字符集修改
--并没有亲自测试过，有机会肯定会测试下发布实测版
SHUTDOWN IMMEDIATE;
STARTUP MOUNT EXCLUSIVE;
ALTER SYSTEM ENABLE RESTRICTED SESSION;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0;
ALTER SYSTEM SET AQ_TM_PROCESSES=0;
ALTER DATABASE OPEN;
ALTER DATABASE NATIONAL CHARACTER SET INTERNAL_USE UTF8;
SHUTDOWN immediate;
STARTUP;
```

```shell
客户端字符集修改
1)UNIX环境($ 是linux提示符)
$ NLS_LANG="simplified chinese"_china.zhs16gbk
$ export NLS_LANG
编辑oracle用户的profile文件

2)Windows环境
编辑注册表，WIN+R 输入 regedit
找到 HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE\HOME0
NLS_LANG 修改为需要的字符集即可
或者在命令窗口设置：
set nls_lang=AMERICAN_AMERICA.ZHS16GBK

3)dmp文件字符集修改
最简单的就是直接用UltraEdit修改dmp文件的第2和第3个字节。
比如想将dmp文件的字符集改为ZHS16GBK，可以用以下SQL查出该种字符集对应的16进制代码: 
select to_char(nls_charset_id('ZHS16GBK'), 'xxxx') from dual;
0354
然后将dmp文件的2、3字节修改为0354即可。
```


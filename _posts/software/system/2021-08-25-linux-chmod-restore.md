---
layout: post
title: 不小心执行了 chmod 755 / 之后
categories: Linux
description: 不小心执行了 chmod 755 / 直觉告诉我不太对，立马百度，然后补救，所以省了很多事
keywords: system, linux, chmod
---

> 本来是查 nginx 提示 permission denied 的问题，一不小心差点闯祸，Root 使用还是要小心啊。

## 1. 从 Permission denied 开始说起

1. 今天要把前端页面部署到 nginx，本身特简单一个事情，页面扔服务器，修改 nginx 配置添加一个 server 更新配置完事。
2. 结果出了幺蛾子，访问页面提示 403 Forbidden ，查日志一看 Permission denied 。
3. 哎，我用的 Root 启动的 nginx 啊，权限问题？
4. ps -ef | grep nginx 查看了主进程是 root 后面的 worker(干活的进程) 都是 nobody。
5. 百度了下也都说用户改一下就行了，或者就是权限修改下，思考了一下还是改权限吧，打开的这个链接刚好有命令，复制粘贴吧。
6. chmod 755 / home/www/ 没细看，直接粘贴过去修改了一下 chmod 755 / home/dist/
7. 对，就是这个空格，执行了之后提示了几个 Permission denied ，心里就有点犯嘀咕，什么情况？直觉告诉我不对劲。
8. 仔细翻了翻记录，心里咯噔一下，出事了，有个空格。
9. 保留作案现场，百度下这个 755 有什么后果，查到了好多 777 ，心想 755 可能问题不大吧。
10. 这个连接是不敢关了，再打开一个连接试试，完蛋，打不开了，肯定有问题，看看咋补救吧。

## 2. 备份权限信息并还原

1. 百度了几篇基本上俩方案，虽然是测试服务器，问题不大，第一条也是直接 pass 。

   1. 重装系统。
   2. 找一台机器备份权限，这边还原。

2. 连上另外一台服务器，备份权限。

   ```shell
   # 备份根目录及其子目录
   getfacl -R / > /tmp/acl.bak
   # 备份 /bin 目录
   getfacl -R /bin > /tmp/acl.bak
   ```

   

3. 然后出问题的服务器 xshell、xftp 连接都还在，问题不大。主要是 xshell 的 ssh 连接还在，要不真的要折腾死我了。

   ```shell
   # 上传到服务器之后，进行还原
   setfacl --restore acl.bak
   ```

   

4. 因为两个服务器还是有点差异的，备份的目录权限跟实际的目录权限还是有区别的，所以比较好补救。

5. 服务器上有好几个服务，其中受到影响的除了系统(权限也已经还原)，还剩下 oracle，百度了一下，找到了解决方案。

   ```shell
   # $ORACLE_HOME可以直接使用，最好还是换成实际的目录把，下面第三节讲危害
   chmod 6751 $ORACLE_HOME/bin/oracle
   ```

   

6. 这次修复基本上就结束了，花了十来分钟，有惊无险，以后执行可能会导致危险的操作应该会给自己多一点的提醒。

## 3. 总结下可能有危险的操作

### 3.1 这里的代码千万不要复制粘贴

```shell
# 这里问题出在 / 目录后面还有一个空格，导致后面被截断
chmod 755 / home/dist
chmod 777 / home/dist
rm -rf / home/dist
# 这里问题更容易遇见，在某个环境变量为空的情况下，下面的 $ORACLE_HOME 等于没有
chmod 755 $ORACLE_HOME/
chmod 777 $ORACLE_HOME/
rm -rf $ORACLE_HOME/
```



### 3.2 如何防止此类事件出现

#### 3.2.1 chmod 类

- 提前备份好目录权限，以备不时之需。

```shell
getfacl -R / > /home/acl.bak
```



#### 3.2.2 rm 类

- 给 rm 加上一个回收站吧，也有个后悔药吃。
- 将下面代码添加到 ~/.bashrc 文件末尾后，执行 source ~/.bashrc 即可。
  - rm 移动文件到 ~/.trash
  - lstrash 查看回收站的文件
  - untrash 还原回收站里面的文件
  - cltrash 清空回收站
- 当回收站文件与要删除文件同名，会提示覆盖，如果类型不一样，一个是文件，另一个是文件夹，会删除失败。

```shell
# add trash for safe rm
mkdir -p ~/.trash

alias rm=trash
#alias r=trash
alias lstrash='ls ~/.trash'
alias untrash=undeltrash
alias cltrash=cleartrash

trash()
{
    mv $@ ~/.trash/
}

undeltrash()
{
    mv -i ~/.trash/$@ ./
}

cleartrash()
{
    read -p "clear all? [y/n] : " confirm
    [ $confirm == 'y' ] || [ $confirm == 'Y' ] && /bin/rm -rf ~/.trash/*
}
```




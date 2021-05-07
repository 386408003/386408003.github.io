---
layout: post
title: Oracle安装无法访问临时位置解决
categories: Oracle
description: 多年前写的青涩文章，留个纪念！
keywords: Oracle, 无法访问临时位置
---

> 安装Oracle11g数据库十次有七八次都会遇到的一个问题无法访问临时位置的问题！

> 每次都会百度找到怎么建立C$共享，而且解决的方法各不相同，但是目的都是一样的，下面就描述下问题情况，以及最近一次成功解决问题的方法，之后有新的方法，或者此方法解决不了，将会持续更新！

问题描述：

```
原因 - 无法访问临时位置。
操作 - 请确保当前用户具有访问临时位置所需的权限。
附加信息:
- 所有节点上的框架设置检查都失败
- 原因: 问题的原因不可用
- 操作: 用户操作不可用 失败节点概要 pc-20130618muam
- 无法从节点 "pc-20130618muam" 检索 exectask 的版本
- 原因: 问题的原因不可用
- 操作: 用户操作不可用
```

解决方案：

```shell
首先使用命令行执行命令
net share C$=C:
测试一下 c$ share 是否成功
在cmd里输入 net use \\localhost\c$
失败: 系统错误53
The network path was not found.
成功: 命令成功完成。
 
检查AutoShareServer和AutoShareWks注册表值，以确保未将它们设置为0。
依次点击"开始→运行"，输入regedit，然后按回车键进入注册表编辑器。
找到HKEY_LOCAL_MACHINE\System\Current ControlSet\Services\LanmanServer\Parameters
如果LanmanServerParameters子项中的AutoShareServer和AutoShareWks 的DWORD值配置的数值为0，则将该值更改为1
 
重启系统，重新安装即可。
```


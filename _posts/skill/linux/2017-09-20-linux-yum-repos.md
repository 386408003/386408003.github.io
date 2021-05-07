---
layout: post
title: Linux本地yum源配置
categories: Linux
description: 多年前写的青涩文章，留个纪念！
keywords: CentOS, Redhat, yum, Linux
---

## 一、CentOS本地yum源配置

1. 创建挂载目录

   ```sh
   mkdir -p /media/cdrom
   ```

2. 挂载对应系统版本的iso光盘镜像文件

   ```sh
   mount -o loop -t iso9660 /opt/rhel-server-6.2-x86_64-dvd.iso /media/cdrom
   ```

3. 进入配置目录

   ```sh
   cd /etc/yum.repos.d/
   ```

4. 禁用网络yum源，CentOS-Base.repo是yum 网络源的配置文件

   ```sh
   mv CentOS-Base.repo CentOS-Base.repo.bak
   ```

5. 备份原yum源配置，CentOS-Media.repo 是yum 本地源的配置文件

   ```sh
   cp CentOS-Media.repo CentOS-Media.repo.bak
   ```

6. 配置本地yum源

   ```sh
   vi CentOS-Media.repo
   ```

   ```properties
   #库名称，做到全局唯一不重复
   [c6-media]
   #名称描述
   name=CentOS-$releasever - Media
   #yum源目录，源地址
   baseurl=file:///media/cdrom/
   #是否启用该yum源，0为禁用
   gpgcheck=1
   #检查GPG-KEY，0为不检查，1为检查
   enabled=1
   #gpgcheck=0时无需配置
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

7. 最后测试yum是否可用：

   ```sh
   yum clean all
   yum list
   ```



## 二、RHEL 本地yum源配置

1. 创建挂载目录

   ```sh
   mkdir -p /media/cdrom
   ```

2. 挂载对应系统版本的iso光盘镜像文件

   ```sh
   mount -o loop -t iso9660 /opt/rhel-server-6.2-x86_64-dvd.iso /media/cdrom
   ```

3. 配置yum文件如下

   ```sh
   vi /etc/yum.repos.d/rhel-source.repo
   ```

   ```properties
   #yun源的名字，做到全局唯一不重复
   [rhel-iso]
   #注释信息
   name=Red Hat Enterprise Linux $releasever - $basearch - Source
   #yum源的路径，支持三种协议：http、ftp、file，其中file表示本地文件，/media/cdrom/才是真实路径
   baseurl=file:///media/cdrom/
   #1表示启用，0表示禁用
   enabled=1
   #指纹校验，为0表示不校验
   gpgcheck=0
   #校验参考的文件
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-RedHat-release
   ```

4. 最后测试yum是否可用：

   ```shell
   yum clean all
   yum list
   ```

   
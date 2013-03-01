---
layout: post
title: "安装软件程序 chapter8 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, commadn line, 学习]
---
{% include JB/setup %}

# 第8章 安装软件程序

包管理系统(Package Management System, PMS)

## 8.1 包管理基础

PMS记录

* 已安装软件包
* 每个包安装了什么文件
* 每个已安装软件包的版本

软件包存储在服务器上 **库(repository)**

依赖关系,PMS会自动检测依赖关系,并安全额外需要的软件包

PMS现在还没有标准工具

现存主要的PMS基础工具是`dpkg`和`rpm`

基于Debian的发行版, 如Ubuntu和Linux Mint, PMS工具的底层用的是dpkg命令,与PMS交互,安装,管理和删除软件包

基于Red Hat的发行版, 比如Fedra,openSUSE和Mandriva, PMS工具的底层是rpm命令

这两个命令是PMS的核心,但不是全部,还会有一些额外的工具,使得管理简单

## 8.2 基于Debian的系统

`dpkg` 是基于Debian系PMS工具的核心

* apt-get
* apt-cache
* aptitude

`aptitude` 本质上是apt工具和dpkg的前端, dpkg是一个软件包管理系统工具, aptitude则是一个完整的软件包管理系统

aptitude可以避免一些问题,如软件依赖关系缺失,系统环境不稳定以及其他一些不必要的麻烦

### 8.2.1 用aptitude管理软件包

`aptitude` 来开启交互式程序

如果知道了系统上的那些软件包,显示特定包的详细信息

`aptitude show package_name` 显示package_name包的详细信息,并不表示这个包已经安装了,只是显示从软件库中得到的详细的软件包信息

无法通过aptitude看到的一个细节是所有跟摸个特定软件包关联的所有文件的列表,要得到这个列表需要用`dpkg`命令

`dpke -L package_name` 查找和package_name相关的所有文件

`dpkg --search absolute_file_name` 可以反向查找某个特定文件属于哪个软件包

相当犀利啊

### 8.2.2 用aptitude安装软件包

`aptitude search package_name_key_word` 查找特性的软件包

每个软件包前面有一个标示符`i`表示已经安装了,`p`表示未安装,`v`表示什么呢?

`sudo aptitude install package_name` 安装package

要确认是否安装,用aptitude search搜索一下,标示符是否为`i`

本地安装
`sudo dpkg -i software_name`

### 8.2.3 用aptitude更新软件

`aptitude safe-upgrade` 将包所有包更新到最新

* `aptitude full-upgrade`
* `aptitude dist-upgrade`

将所有软件包升级到到最新,于safe-upgrade的区别是不检查包与包之间的依赖关系.

刚安装完的系统尤其需要safe-upgrade

### 8.2.4 用aptitude卸载软件

只删除软件包但不删除数据和配置文件,可以用aptitude的`remove`选项,要删除软件包和相关数据和配置文件,可以用`purge`选项

看软件包是否删除,也可以用`aptitude search`选项, 如果软件包前面的标示符是`c`,意味着软件删除了,但是配置文件未删除,`p`表示配置文件也删除了

### 8.2.5 aptitude库

`/etc/apt/sources.list` 存储着软件库

例子

    deb http://us.archive.ubuntu.com/ubuntu/ maverick main restricted
	
    deb (or deb-src) address distribution_name package_type_list

`deb(or deb-src)` 表明了软件包的类型,deb说明是编译后程序的源,deb-src是源代码源

`address` 是软件库的web地址

`distribution_name` 是特定软件库的发行版版本的名称,如这里是`maverick`,但是并不能说明你使用的是Ubuntu Maverick Mint, 只是说明Linux发行版本在用Linux Maverick Meercat软件库

`package_type_list` 可能不止一个词,表名库里面有什么类型的包, main restricted universe partner

## 8.3 基于Red Hat 的系统

* yum
* urpm
* zypper

这些前端都是基于rpm命令行工具的

### 8.3.1 列出已安装包

`yum list installed`

`rpm -qa`

`zypper search -I`

`yum list package` 列出package,不一定已经安装

`yum list installed package` 列出package是否安装

`yum provides file_name` 查看file_name文件是什么软件的文件

### 8.3.2 用yum安装软件

`yum install package_name` 安装package_name,自动安装依赖关系

`su -` 切换好root用户

**本地安装(local installation)**

`yum local install package_name.rpm`

### 8.3.3 用yum更新软件

`yum list updates` 列出所有针对已安装包的可用更新

`yum update package_name` 更新package_name软件包

`yum update` 更新所有软件

### 8.3.4 用yum卸载软件

`yum remove package_name` 删除软件包,但保留配置文件和数据文件

`yum erase package_name` 删除软件和数据

### 8.3.5 处理损坏的包依赖关系

在同时安装多个软件包时,某个包的软件以来关系可能会被另一个包的安装覆盖掉,**损坏的包依赖关系**

`yum clear all` 先试试这个命令,这命令干什么的?

`yum update` 在试试更新

`yum deplist package_name` 命令显示所有包的依赖关系以及什么软件可以提供这些库依赖关系,一旦知道某个包需要的库后就可以安装他们了


如果这些都还没解决

`yum update --skip-broken` 忽略依赖关系损坏的包,更新其他软件包

### 8.3.6 yum软件库

`yum repolist` 显示当前使用的软件库,库位于`/etc/yum.repos.d`, 需要添加正确的URL并获得必要的加密密匙

## 8.4 从源码安装

`tar` 解压下载的软件包

阅读README

`./configure` 根据系统配置软件,如使用正确的编译器, 正确的包以来关系等等

`make` 安装文件,将源码编译成二进制可执行文件,完成后在目录下有可用程序

`make install` 安装到Linux的一个常见的位置.


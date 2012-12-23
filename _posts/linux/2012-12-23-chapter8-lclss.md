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


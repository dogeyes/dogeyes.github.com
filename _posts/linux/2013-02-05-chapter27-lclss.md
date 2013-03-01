---
layout: post
title: "shell脚本编程进阶 chapter27 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第27章 shell脚本进阶

## 27.1 监测系统统计数据

### 27.1.1 系统快照报告

**快照报告**是特定时间点由系统的统计数据构成的图

#### 1. 需要的功能

`uptime` `df` `free` `ps`

##### 运行时间

`uptime` 

* 当前时间
* 系统运行的天数,小时和分钟数
* 当前登陆到系统上的用户数
* 一分钟,五分钟,十分钟的平均负载

##### 磁盘使用情况

`df` 命令

##### 内存使用情况

`free`命令那个

##### 僵尸进程

`ps` 命令

#### 2. 创建快照脚本

### 27.1.2 系统统计数据报告

#### 1. 需要的工具

`vmstat` 命令

#### 2. 创建捕捉脚本

#### 3. 生成报告脚本

#### 4. 运行脚本

## 27.2 问题跟踪数据库

### 27.2.1 创建数据库

* 报告问题的日期
* 解决问题的日期
* 问题的描述及症状
* 问题解决办法的描述

创建空数据库,创建表,4个数据字段,和一个id字段

mysql 的`describe`命令显示表的字段

创建一个适当权限的新账户

### 27.2.1 记录问题

简易脚本封装

#### 1. 需要的功能

mysql 的 INSERT命令

命令以`;`结尾和以`\G`结尾显示方式不同

#### 2. 创建脚本

### 27.2.3 更新问题

#### 1. 需要的功能

mysql的update功能

#### 2. 创建脚本

#### 3. 附加的修改

### 27.2.4 查找问题

#### 1. 需要的功能

mysql 的 `SELECT` 功能

     SELECT * FROM problem_logger
     WHERE prob_symptoms LIKE '%yum%'\G
     #查找prob_symptoms字段有yum字符的项
     
     SELECT * FROM problem_logger
     WHERE prob_symptoms LIKE '%yum%'
     OR
     prob_solutions LIKE '%yum%'\G
     
`LIKE`命令不支持多个关键字

`REGEXP` 命令支持多个关键字

     SELECT * FROM problem_logger
     WHERE prob_symptoms REGEXP 'yum|dash'
     OR
     WHERE prob_solutions REGEXP 'yum|dash'
     
#### 2. 创建脚本


     
     
---
layout: post
title: "chapter4 LCLSS"
description: ""
category: LCLSS
tags: [学习,linux,command line]
---
{% include JB/setup %}

# 第4章 更多的bash shell命令

## 4.1 监测程序

### 4.1.1 探查进程

进程(process) 是程序运行在系统上

`ps` 显示系统上所有程序的许多信息

参数多,非常复杂

默认情况下只显示当前用户的进程

进程号(PID, Process ID)

终端(TTY)

`ps`命令曾经有两个版本,现在两个版本融合在一起了,使得问题更为复杂.

3种不同的命令行参数

* Unix风格的参数,前面加单破折号
* BSD风格的参数,前面不加破折号
* GNU风格的长参数,前面加双破折号

#### 1. Unix风格的参数

* `-A` 显示所有
* `-N` 显示与指定参数不符的所有进程
* `-a` 显示除控制进程(session leader)和无终端的进程外的所有进程
* `-d` 显示除控制进程外的所有进程
* `-e` 显示所有进程
* `-C cmdlist` 显示包含在*cmdlist*列表中的进程
* `-G grplist` 显示组ID在*grplist*列表中的进程
* `-U userlist` 显示属主的用户ID在*userlist*列表中的进程
* `-g grplist` 显示会话或组ID在*grplist*列表中的进程

跪了,都列出来有意义吗?

想多了

`ps -ef` 显示所有进程的完整格式

`ps -l` 长格式输出

`-H` 可以把输出进程组织成一个层级格式

#### 2. BSD风格的参数

#### 3. GNU全字参数

`--forest` 参数,显示进程的层级信息

### 4.1.2 实时监测进程

`ps`只能看某一时间点的进程及其状态

`top` 可以观察频繁换进换出内存的进程的趋势

系统概况:当前时间,系统运行时间,登入的用户数,系统平均负载

系统平均负载: 1分钟,5分钟,15分钟

`top` 交互式命令

### 4.1.3 结束进程

Linux上,进程间通过**信号**来通信

* 1  HUP   挂起
* 2  INT   中断
* 3  QUIT  结束运行
* 9  KILL  无条件终止
* 11 SEGV  段错误
* 15 TERM  尽可能终止
* 17 STOP  无条件停止运行,但不终止
* 18 TSTP  停止或暂停, 但继续在后台运行
* 19 CONT  在STOP或TSTP之后恢复执行

两个命令可以向进程发送信号: `kill` 和 `killall`

#### 1 kill命令

通过PID给进程发送信号,默认发送TERM信号, 只能用PID不能用命令名

要发送信号,必须是root或者进程的属主

`kill PID`

如果程序忽略TERM信号,就要用其他信号,通常先试试TERM信号,再试试HUP或者INT信号,KIll信号最强,直接终止进程,有可能损坏文件

`kill -s HUP 309` 挂起309进程

#### 2 killall命令

支持通过进程名来结束进程,支持通配符

`killall http*` 结束http开头的所有进程


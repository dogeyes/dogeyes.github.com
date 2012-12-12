---
layout: post
title: "chapter3 linux command"
description: ""
category: linux command
tags: [学习, linux, command]
---
{% include JB/setup %}

# chapter3 基本的bash shell命令

## 3.1 启动shell

`/etc/passwd`文件包含了所有系统用户账户列表以及每个用户的基本配置信息.

     root:*:0:0:System Administrator:/var/root:/bin/sh

七个字段

1. 用户名
2. 密码
3. 用户的系统UID(用户ID)
4. 用户的系统GID(组ID)
5. 用户的全名
6. 用户的默认主目录
7. 用户默认的shell程序

bash程序同样使用命令行参数来修改所启动shell的类型,bash支持的可定义启动shell类型的命令行参数

<table>

  <tr>
    <td>-c string</td>
	<td>从string中读取命令并处理他们</td>
  </tr>
  
  <tr>
    <td>-r</td>
	<td>启动限制性shell,限制用户在默认目录下活动</td>
  </tr>
  
  <tr>
    <td>-i</td>
	<td>启动交互行shell,允许用户输入</td>
  </tr>
  
  <tr>
    <td>-s</td>
	<td>从标准输入读取命令</td>
  </tr>
</table>

默认情况下,bash shell启动时会自动处理用户主目录下的`.bashrc`文件中的命令.许多linux发行版在此文件中加载特殊的**共用文件**,在共用文件中保存着针对所有系统用户的命令和设置,共用文件位于`/etc/bashrc`,它经常设置各种应用程序中用到的环境变量

## 3.2 shell提示符

bash shell提示符(默认$)

有两个环境变量用来控制命令行提示符的格式:

* PS1: 控制默认命令提示符的格式
* PS2: 控制后续命令提示符的格式

     echo $PS1
	 
	 echo $PS2
	 
***
bash shell提示符字符
***

可以通过设定PS1和PS2来改变提示符,但是这个提示符只在当前shell下有用,重新开启后又是默认的提示符

     PS1="\t"

提示符变为时间

## 3.3 bash手册

     man date
	 
查找`date`命令的bash 手册

    man bash
	
通过空格下翻,也可以通过上下键来翻页,`q`退出

## 3.4 浏览文件系统

### 3.4.1 Linux文件系统

Linux将文件存储在单个目录结构中,这个目录我们称为**虚拟目录(virtual directory)**.

**根(root)**目录

Linux用`/`来划分目录.`\`用来转义

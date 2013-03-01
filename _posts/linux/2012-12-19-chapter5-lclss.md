---
layout: post
title: "使用linux环境变量 chapter5 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, 学习, linux, command line]
---
{% include JB/setup %}

# 第5章 使用linux环境变量

## 5.1 什么是环境变量

`环境变量(environment variables)` bash shell用来存储有关shell会话和工作环境的信息

它允许你在内存中存储数据,这些数据可以用来标识用户账户,系统,shell的特性及其其他存储的数据

* 全局变量
* 局部变量

### 5.1.1 全局变量

全局变量不仅对shell会话可见,对shell创建的子进程也可见. 局部变量只对shell可见

系统环境变量大写,区别于普通用户的环境变量

`printenv` 查看全局变量

`echo $HOME` 显示HOME环境变量的值

### 5.1.2 局部环境变量

局部环境变量只能在定义他们的进程中可见.

`set` 显示进程的所有环境变量(局部,全局),貌似没有方法访问单个局部变量,可以用`grep`啊

## 5.2 设置环境变量

### 5.2.1 设置局部环境变量

`test=testing` 设置**test**环境变量的值为**testing**

`$test` 来引用test环境变量, 很简单

`test = 'testing a long string'` 将含有空格的字符串赋值给变量,要用`'`界定字符串的开始和结束

自己创建的环境变量用小写,系统环境变量用大写,有助于区分

环境变量,等号,值之间没有空格,否则错误,各部分被当成命令

新建进程中无法使用之前的环境变量

### 5.2.2 设置全局环境变量

先创建一个局部环境变量,再把它导出到全局变量中

`export` 命令

     $test=testing
	 $echo $test //有
	 $bash //新建进程
	 $echo $test //无,因为是局部变量
	 $exit //退回到主进程
	 $export test //将环境变量导出到全局变量
	 $bash
	 $echo $test //有

## 5.3 删除环境变量

`unset` 命令来删除环境变量

在处理全局环境变量时比较麻烦,如果在子进程中删除了一个全局变量,它只对子进程有效,全局变量在父进程中依然有效

因此要在父进程中删除?

## 5.4 默认shell环境变量

* HOME
* PATH
* PS1
* PS2

PATH环境变量是以`:`分割的shell查找命令的目录的列表,相当重要

并不是说有变量都会在set时输出,因为不是说有变量都有值

## 5.5 设置PATH环境变量

`PATH` 定义的命令行输入命令的搜索路径,如果找不到命令,会产生错误

     echo $PATH
	 PATH=$PATH:/home/user/test
	
可以在PATH后面添加`/home/user/test` 目录

    PATH=$PATH:.
	
将当前目录加到PATH,但是最好不要这样,会出问题的

## 5.6 定位系统环境变量

启动bash shell时,默认情况下bash在几个文件中查找命令,这些文件被称为**启动文件**

bash shell 3三种启动方式

1. 登录时当做默认登录shell
2. 作为非登录shell的交互式shell
3. 作为运行脚本的非交互shell

### 5.6.1 登录shell

4个启动文件

1. /etc/profile
2. $HOME/.bash_profile
3. $HOME/.bash_login
4. $HOME/.profile

1是系统上默认的bash shell主启动文件,2,3,4是用户专有的启动文件

#### 1. /etc/profile

bash shell的主启动文件,系统上每个用户登录时都会执行这个启动文件,执行里面的命令,不同的发行版放了不同的命令

#### 2. $HOME目录下的启动文件

提供用户专属的启动文件来定义用户专有的环境变量

### 5.6.2 交互式shell

shell不是在登录时启用,而是通过`bash`命令启动的,称为交互式shell

交互式shell不启动/etc/profile,而是去查看`$HOME/.bashrc`是否存在

### 5.6.3 非交互式shell

系统执行脚本时使用的shell

`BASH_ENV` 检查要启动的文件

## 5.7 可变数组

环境变量一个功能就是可以当做数组使用,值可以按单个值或整个数组来引用

`mytest=(one two three four five)` 括号包围,空格分隔

但是`echo mytest` 只显示`one`

`echo ${test[2]}` 引用test的第二个值(从0开始), 注意是大括号包围

`echo ${test[*]}` 显示整个数组

`test[2]=seven` 改变第二个值

`unset test[2]` 删除第二个值,但是

`echo ${test[2]}` 是空,而不是后面的前移,其他的不变

`unset test` 来删除整个数组

可变数组和其他shell环境不通用,在多种shell环境下编写大量脚本会不便,因此不常用

## 5.8 使用命令别名

**命令别名** 允许为通用命令(和他们的参数在一起)创建一个别名,这样就能通过最少的键入调用想要的命令了

`alias -p` 查看别名列表

`alias li='ls -il'` 创建li别名

和局部环境变量差不多,自在定义他们的shell中有效

当然可以在`.bashrc` 文件里定义别名,这样启动时运行这个文件的shell都会有这个别名



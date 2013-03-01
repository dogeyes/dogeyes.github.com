---
layout: post
title: "呈现数据 chapter14 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第14章 呈现数据

## 14.1 理解输入和输出

* 在显示器上显示输出
* 将输出重定向到文件中

### 14.1.1 标准文件描述符

**文件描述符**来标示每个文件对象

|文件描述符 |缩写   |描述    |
|---------|------|--------|
|0        |STDIN |标准输入 |
|1        |STDOUT|标准输出 |
|2        |STDERR|标准错误 |

#### 1. STDIN

输入重定向(`<`),Linux会用重定向指定的文件来替换标准输入文件描述符.

#### 2. STDOUT

`>`输出重定向

`>>`重定向追加

#### 3. STDERR

标准错误输出

### 14.1.2 重定向错误

#### 1.只重定向错误

`2>` 将错误消息重定向

#### 2.重定向错误和数据

    ls -al test1 test2 test3 badtest 2> test6 1> test7

分别重定向

`&>` 错误和输出重定向到同一个文件

错误消息优先级较高,错误一般都在输出文件的头部

## 14.2 在脚本中重定向输出

* 临时重定向每行输出
* 永久重定向脚本中的所有命令

### 14.2.1 临时重定向

    echo "This is an error" >&2  注意有&符号

这将会使这条文本重定向到STDERR文件描述符所指的位置

### 14.2.2 永久重定向

`exec` 重定向

    exec 1>testout

`exec`会重新启动一个shell,并将STDOUT文件描述符重定向到文件,脚本中所有STDOUT的输出都会被重定向到文件

## 14.3 在脚本中重定向输入

`exec 0< testfile`  将STDIN重定向为文件testfile

## 14.4 创建自己的重定向

### 14.4.1 创建输出文件描述符

    exec 3>test13out
    
    echo "this should display on the monitor"
    echo "and this  should be stored in the file " >&3
    echo "Then this should be back on the monitor"
    
用`exec`创建文件描述符

`exec 3>>test13out` 追加文件

### 14.4.2 重定向文件描述符

    exec3>&1  将3重定向到1,类似于保存1到3
    exec 1>test14out
    echo "This should store in the output file"
    echo "along with this line."
    
    exec 1>&3  从3恢复


为什么有的时候有`&`, 有的时候没有

### 14.4.3 创建输入文件描述符

    exec 6<&0
    
    exec0<testfile
    
    count=1
    while read line
    do
      echo "Line #$count: $line"
      count=$[ $count + 1 ]
    done
    exec 0<&6
    read -p "Are you done now?" answer
    echo $answer
    
### 14.4.4 创建读写文件描述符

    exec 3<> testfile
    read line <&3
    echo "Read: $line"
    echo "This is a test line" >&3
    
维护一个当前位置指针,写的时候会覆盖


### 14.4.5 关闭文件描述符

重定向到特殊符号`&-`

`exec 3>&-`  关闭文件描述符3

    exec 3> test17file
    echo "this is a test line of data" >&3
    
    exec 3>&-
    echo "this won't work" >&3
    
如果重新将3定向到test17file,那么文件被覆盖

## 14.5 列出打开的文件描述符

`lsof` 显示当前linux系统上打开的每个文件的有关信息

`/usr/sbin/lsof`

`-p` 指定PID

`-d` 指定显示的文件描述符的个数

`$$` 当前进程的PID

`-a`选项对两个选项结果执行AND运算

    /usr/sbin/lsof -a -p $$ -d 0.1.2
    
    exec 3> test18file1
    exec 6> test18file2
    exec 7<testfile
    
    lsof -a -p $$ -d

## 14.6 阻止命令输出

`null`文件

`/dev/null`

可以输入重定向,也可以输出重定向

`/dev/null > testfile` 删除testfile中的数据

## 14.7 创建临时文件

`/tmp` 在启动时自动删除其中所有文件

`mketmp` 在/tmp中创建一个唯一的临时文件, 不用默认的umask值,把文件的读写权分配给属主,其他人没法访问它

### 14.7.1 创建本地临时文件

`mktemp testing.XXXXXX`  6个X

返回创建的文件名

要在脚本中使用,保存输出的文件名

    tempfile=`mktemp test19.XXXXXX`
    
    exec 3>&tempfile
    
    echo "This script writes to temp file $tempfile"
    
    echo "This is the first ling" >&3
    echo "This is the second line" >&3
    echo "This is the third line" >&3
    exec 3>&-
    
    echo "Done creating temp file. The contents are"
    
    cat $tempfile
    
    rm-f $tempfile 2> /dev/null
    
### 14.7.2 在/tmp目录创建临时文件

`-t` 选项会强制`mktemp`在系统的临时目录来创建文件,并且返回全路径名

### 14.7.3 创建临时目录

`-d`选项告诉`mktemp`来创建一个临时目录而不是临时文件,可以对目录进行操作

## 14.8 记录消息

`tee` 相当于管道的一个T型接头, 将从STDIN过来的数据同时发送给两个目的地,一个是STDOUT,一个是`tee`命令行指定的文件名:

`tee filename`

因为数据是从STDIN来的,因此可以用管道,重定向等

默认情况下是覆盖的,使用`-a`选项来进行追加

    tempfile=test22file
    
    echo "This is the start of the test" | tee $tempfile
    echo "This is the second line of the test" | tee -a $tempfile
    echo "This is the third line of the test" | tee -a $tempfile
    

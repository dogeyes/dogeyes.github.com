---
layout: post
title: "更多的bash shell命令 chapter4 LCLSS"
description: ""
category: LCLSS
tags: [学习, linux, command line, LCLSS]
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

## 4.2 检测磁盘空间

### 4.2.1 挂载存储媒体

把存储媒体放在虚拟目录下,成为**挂载**

可移动存储媒体  自动挂载

#### mount命令

`mount`  默认输出所有当前挂载的媒体

* 媒体的设备文件名
* 媒体挂载到虚拟目录的挂载点
* 文件系统类型
* 已挂载媒体的访问状态

`mount -t type device directory` 手动挂载命令  type指定了磁盘被格式化的文件系统类型

* vfat
* ntfs
* iso9660

`mount -t vfat /dev/sdb1 /media/disk` 将vfat类型sdb1 挂载到 /media/disk目录

#### umount命令

移除一个设备要先卸载

`umount [directory | device ]` 支持通过设备文件或挂载点来指定要卸载的设备,若有程序在运行则不允许卸载(device is busy)

`lsof [directory | device]` 可以查看访问或者使用设备的进程,无法卸载时,先使用该命令看进程

### 4.2.2 使用df命令

`df` 命令查看已挂载磁盘的使用情况

* 设备的设备文件位置
* 能容纳多少个x块  (x不一定有1k,有500)
* 已使用
* 未使用
* 使用空间比
* 挂载到了哪个挂载点上

`df -h` 比较容易读的形式

#### du命令

`du` 显示特定目录(默认为当前目录)的磁盘使用情况

这个列表是从一个目录层级的最底部开始的,然后按文件,子目录,目录逐级向上的

默认命令并没有太大用处

`-c` 显示所有已列出文件总的大小
`-h` 人类可读方式输出
`-s` 显示每个输出参数的总计

`du -sh *` 可以显示当前目录下每个文件和目录的大小

## 4.3 处理数据文件

### 4.3.1 数据排序

`sort file` 对file文件内容进行默认排序,貌似是按照字符串进行排序的

`sort -n file` 按照数字进行排序

`sort -M file` 按照月份(month)进行排序

`-k` 和 `-t` 参数也比较有用,`-t`确定域的分隔符,`-k`确定按第几个域进行排序

`sort -t ':' -k 3 -n /etc/passwd` 对passwd每行按`:`进行分割,按第三个域进行排序

`du sh * | sort -nr` 可以按大小查看当前目录下文件和目录的大小

### 4.3.2 搜索数据

`grep [options] pattern [file]` 命令来搜索某一行

`-v` 选项显示那些不匹配的行

`-n` 选项在行前显示行号

`-c` 仅显示搜索到的行数

`-e` 可以添加多个pattern, 或操作`grep -e [pattern1] -e [pattern2] file`

`egrep` 命令是`grep`的一个分支,允许POSIX正则表达式

`fgrep` 允许将一系列字符串作为pattern, 比如输入一个含多个字符串的文件.

### 4.3.3 数据压缩

#### bzip2工具

* `bzip2` 压缩文件
* `bzcat` 显示压缩文件的内容
* `bunzip2` 解压`.bz2` 文件
* `bzip2recover` 尝试修复损坏的文件

默认压缩文件会替换源文件

#### gzip 工具

* gzip
* gzcat
* gunzip

#### zip工具

* `zip` 压缩文件
* `zipcloak` 加密压缩
* `zipnote` 解压注释
* `zipsplit` 将大的zip文件分成小的
* `unzip` 解压文件

zip的好处是可以用`-r`命令压缩整个文件夹

### 4.3.4 归档数据

`tar function [options] object1 object2 ...` 

function参数决定`tar`命令做什么工作

function

* `-A` 将一个归档文件连接到另一个归档文件之后
* `-c` 新建归档文件
* `-d` 检查一个归档文件和文件系统的差别
* `--delete` 从归档文件中删除文件
* `-r` 将文件压缩添加到一个归档文件的末尾
* `-u` 用新文件替换旧文件,更新
* `-x` 解压文件

Option

* `-C dir` 转换到一个特定的目录
* `-f file` 输出到文件file
* `-j` 重定向输出到bzip2进行压缩(tar 只进行归档不进行压缩?)
* `-p` 保留文件许可(permission)
* `-v` 列出正在进行处理的文件
* `-z` 重定向到gzip进行压缩




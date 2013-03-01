---
layout: post
title: "控制脚本 chapte15 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第15章 控制脚本

## 15.1 处理信号

### 15.1.1 重温Linux信号

 |信号| 值   |   描述     |
 |---|------|-----------|
 |1  |SIGUP |挂起进程     |
 |2  |SIGINT|终止进程     |
 |3  |GIGQUIT|停止进程    |
 |9  |SIGKILL|无条件终止进程|
 |15 |SIGTERM|无可能的话终止进程|
 |17 |SIGSTOP|无条件停止进程,但不终止进程|
 |18 |SIGTSTP|停止或暂停进程,但不终止进程|
 |19 |SIGCONT|继续运行停止的进程|
 
 
### 15.1.2 产生信号

#### 1.终止进程

`CTRL-C` 产生SIGINT信号,终止当前进程

#### 2. 暂停进程

`CTRL-Z` 产生SIGTSTP信号,停止当前进程

`PS`命令中`T`状态的进程表示停止的进程

`kill -9 PID` 饭送SIGKILL信号终止进程

### 15.1.3 捕捉信号

`trap` 命令

`trap commands signals` 指定shell脚本要观察那些Linux信号并从shell中拦截, 如果脚本收到trap命令中列出的信号,他会阻止它被shell处理,而在本地处理它

    trap "echo ' Sorry! I have traped Ctrl-C'" SIGINT SIGTERM
    echo this is a test program
    count=1
    while [ $count -le 10 ]
	do
		echo "Loop #$count"
		sleep 5
		count=$[ $count + 1 ]
	done
	echo This is the end of the test program
	
每次使用'CTRIL-C'组合键,脚本都会执行trap命令中指定的echo语句,而不是忽略此信号并允许shell停止该脚本

### 15.1.4 捕捉脚本的退出

捕捉shell脚本的退出, 在`trap`命令后面加上EXIT就行

    trap "echo byebye" EXIT
    
    count=1
    while [ $count -le 5 ]
    do
      echo "Loop #$count"
      sleep 3
      count=$[ $count + 1 ]
    done
    
### 15.1.5 移除捕捉

    trap "echo byebye" EXIT
    
    count=1
    while [ $count -le 5 ]
    do
      echo "Loop #$count"
      sleep 3
      count=$[ $count + 1 ]
    done
    
    trap - EXIT  #移除捕捉EXIT信号
    
如果捕捉到信号在移除之前那么信号还是会本处理

## 15.2 以后台模式运行脚本

后台(background)

### 15.2.1 后台运行脚本

在命令后加`&`符号

    ./test &
    
### 15.2.2 运行多个后台作业

可以通过`ps`命令来查看运行中的脚本

### 15.2.3 退出终端

ps命令的输出中,每个后台进程都绑定到了该终端会话的终端上(pts/0),如果进程会话退出,后台进程也会退出

### 15.3 在非控制台下运行脚本

在终端会话中启动脚本,然后让进程一直以后台模式运行,直到其完成,即使退出了终端

`nohup` 命令

`nohup` 命令运行了另外一个命令来阻断所有发送给该进程的SIGHUP信号

    nohup ./test1 &
    
STDOUT和STDERR被重定向到nohup.out文件

## 15.4 作业控制

启动,停止,无条件终止,恢复作业--作业控制

### 15.4.1 查看作业

`jobs` 命令,显示当前作业

* -l 列出进程PID和作业号
* -n 列出上次shell发出的通知后改变了状态的作业
* -p 只列出作业的PID
* -r 只列出运行中的作业
* -s 只列出已停止的作业

`+`表示默认作业

`-`表示下一个默认作业

### 15.4.2 重启停止的作业

`bg 作业号` 后台重启

`fg 作业号` 前台重启

## 15.5 调度谦让度

默认所有进程具有相同的**调度优先级(schduling priority)**

-20(最高优先级)到+20(最低优先级),默认使用0级来启动

`nice` 命令,调整优先级

### 15.5.1 nice命令

`nice -n 10 ./test4 > test4out &` 

貌似普通用户只能降低优先级

### 15.5.2 renice命令

`renice` 改变系统上已经运行命令的优先级

    renice 10 -p PID
    
将PID进程的优先级调整到10

## 15.6 定时运行作业

`at`命令

`cron`表

### 15.6.1 用at命令来计划执行作业

`at`的守护进程`atd`以后台模式运行,并检查作业队列来运行作业

`atd` 会检查系统上的一个特殊目录(通常位于/var/spool/at)来获取用`at`命令提交的作业,如果作业时间和当前时间匹配,atd守护进程会运行此作业

#### 1. at命令的格式

`at [ -f filename ] time`

默认情况下`at`会将STDIN的输入放到队列中,可以用`-f`参数来指定读取的命令(脚本)的文件名

时间

* 小时分钟格式, 如10:15
* ~AM/~PM指示符,比如10:15~AM
* 特定可命名的时间,比如now,noon,midnight或者teatime(4~PM)

如果时间已经过去,那么会在第二天运行该作业

日期

* 标准日期格式MMDDYY, MM/DD/YY或DD.MM.YY
* 文本格式,比如Jul 4或Dec 25

时间增量

* 当前时间+25min
* 明天10:15~25min
* 10:15+7天 

(应该是英文吧…)

使用`at`命令,作业会被提交到**作业队列(job queue)**中

针对不同的优先级,有26种不同的作业队列,a-z

字母排序越高,优先级越低,`-q`参数指定优先级

#### 2. 获取作业的输出

作业默认输出到所有者的mail?,如果脚本没有任何输出,默认不生成邮件,`-m`选项使得无论是否有输出都会生成一封邮件

#### 3. 列出等待的作业

`atq` 命令查看系统中有哪些作业在等待

#### 4. 删除作业

`atrm 作业号` 删除作业

### 15.6.2 计划定期执行脚本

`cron` 程序定期执行作业

`cron` 在后台运行并检查特殊的称作**cron时间表(cron table)**,来获得计划执行的作业

#### 1. cron 时间表

格式

    min hour dayofmonth month dayofweek command

允许用特定值,值范围(1~5),通配符(*)来指定条目

    15 10 * * * command  #每天10:15执行命令command
    
    15 16 * * 1 command #每周一16:15分执行,周日是0
    
    00 12 1 * * command #每月第一天中午
    
每月最后一天,如何?

    00 12 * * * if[`date +%d -d tomorrow` = 01 ];then; command
    
通过一个判断语句来实现

命令列表必须是命令或者脚本的全路径名,可加重定向符

    15 10 * * * /home/rich/test4 > test4out
    
#### 2. 构建cron时间表

`crontab`命令

`-l` 参数列出已有的时间表

`-e` 参数为cron时间表添加条目,会打开一个文本编辑器

#### 3. cron目录

如果不要求精确的执行时间,用预配置的cron脚本目录会更方便

* /etc/cron.daily
* /etc/cron.hourly
* /etc/cron.monthly
* /etc/cron.weekly

#### 4. anacron程序

cron对过期的命令不再执行

`anacron`知道作业错过了计划好的运行,会尽快执行作业,关机错过执行好的作业会自动运行

`anacron`只处理位于cron目录的程序,比如/etc/cron.monthly, 他用时间戳来决定作业是否在适当的计划间隔内运行了,每个cron目录都有一个时间戳文件,位于/var/spool/anacron

`anacron`有自己用来检查作业目录的表(通常位于/etc/anacrontab)

`anacron`时间表的格式与cron时间表略有不同

    period delay identifier command
    
    1 5 cron.daily nice run-parts --report /etc/cron.daily
    
* period 多久运行一次,以天为单位
* delay 在系统启动后多久运行错过的脚本
* command 包含run-parts程序和和一个cron脚本目录名, run-parts负责运行目录中传给他的任何脚本
* identifier 是一种特别的非空白字符串,如cron-weekly,用来唯一识别日志消息和错误E-mail中的作业

anacron不运行hourly的脚本,因为它不处理小于一天的脚本

## 15.7 启动运行

### 15.7.1 开机时运行脚本

#### 1. 开机过程

内核加载到内存并运行,第一件事情就是开始UNIX System V init过程或Upstart init过程,然后这个进程负责启动Linux系统上的所有其他进程

* System V init过程

读取`/etc/inittab`文件, inittab列出系统运行级(run level), 不同的运行级启动不同的程序和脚本

Red Hat

|运行级   |描述      |
|--------|---------|
|0       | 关机     |
|1       |单用户    |
|2       |多用户,不支持网络|
|3       |全功能多用户,支持网络|
|4       |可定义用户         |
|5       |多用户模式,支持网络和图形化X Window会话|
|6       |重启      |

Debian发行版,如ubuntu和Linux Mint,不区分2-5级

Ubuntu甚至没有/etc/inittab文件,默认运行级2, 可以创建一个/etc/inittab文件来修改默认优先级

**开机脚本(startup script)**

有些发行版本将开机脚本放在`/etc/rc#.d`目录, #是运行级

其他使用`/etc/init.d`目录

还有些使用`/etc/init.d/rc.d`目录

* Upstart init 过程

不关心运行级,而关注时间

系统开机称为`开机事件`

`/etc/event.d`或者`/etc/init`目录下的文件来启动

许多Upstart实现仍然会调用位于`/etc/init.d`和`/etc/rc#.d`目录总的脚本

#### 2. 定义自己的开机脚本

最好不要和Linux发行版上已有的独立开机脚本文件混起来

大多数发行版本提供了一个本地开机文件专门让系统管理员添加开机时运行脚本

|发行版  |文件位置|
|-------|-------|
|debian |/ect/init.d/rc.local|
|Fedora |/etc/rc.d/rc.local|
|Mandriva |/etc/rc.local|
|openSuse|/etc/init.d/boot.local|
|Ubuntu |/etc/rc.local|
|centos |/etc/rc.local|

### 15.7.2 在新shell中启动

* .bash_profile  新shell是*新登陆*生成
* .bashrc 新shell启动,包括新登录生成

为所有用户运行一个脚本,`/etc/bashrc`文件





---
layout: post
title: "编写脚本实用工具 chapter26 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第26章 编写脚本使用工具

## 26.1 监测磁盘空间

### 26.1.1 需要的功能

`du`,每个文件和目录显示磁盘的使用情况

`-s`选项用来在目录一级总结整体使用情况

    du -s /home/* #home目录下各文件和目录占用空间大小
    
`-S`选项,为每个目录和子目录分别提供一个总计

`sort`命令对输出进行排序 `-r`反向, `-n`对数值进行排序

在对输出格式进行调整

    sed '{11,$D; =}' #只显示前面10行
    sed 'N; s/\n/ /' #将行号和内容合并到同一行
    gawk '{printf $1 ":" "\t" $2 "\t" $3 "\n"}'
    
### 26.1.2 创建脚本

    #!/bin/bash
    
    CHECK_DIRECTORIES="/var/log /home"
    
    DATE=$(date '+%m%d%y')
    
    exec > disk_space_$DATE.rpt
    
    echo "Top Ten Disk Space Usage"
    echo "for $CEHCK_DIRECTORIES Directories"
	
	for DIR_CHECK in $CHECK_DIRECTORIES
	do
	    echo ""
	    echo "The $DIR_CHECK Directory:"
	    
	    du -S $DIR_CHECK 2> /dev/null |
	    sort -rn |
	    sed '{11,$D; =}' |
	    sed 'N; s/\n/ /' |
	    gawk '{printf $1 ":" "\t" $2 "\t" $3 "\n"}'  
	done
    

### 26.1.3 运行脚本

可以手动运行

也可以通过cron表来定期运行

## 26.2 进行备份

### 26.2.1 归档数据文件

#### 1. 需要的功能

`tar` 命令

    tar -cf archive.tar /home/user/backup_test 2>/dev/null
    #将文件夹归档成一个tar文件
    
可以加`-z`选项进行压缩

    tar -zcf archive.tar /home/user/backup_test 2>/dev/null
    
使用配置文件包含你要归档的文件或目录

通过脚本读取配置文件

    exec < $CONFIG_FILE
    read FILE_NAME
    
处理过程中简单的判断文件是否存在

    if [ -f $FILE_NAME -o -d $FILE_NAME ]
    then
        FILE_LIST="$FILE_LIST $FILE_NAME"
    else
        echo
        echo "$FILE_NAME, does not exist."
        echo "Obviously, I will not include it in this archive."
        echo "It is listed on line $FIlE_NO of the config file."
        echo "Continuing to build archive list…"
        echo
    fi
    FILE_NO=$[ $FILE_NO + 1 ]
    
    
#### 2. 创建按日期归档的脚本

    DATE=`date +%m%d%y`
    FILE=archive$DATE.tar.gz
    CONFIG_FILE=/home/user/archive/Files_To_Backup
    DESTINATION=/home/user/archive/$FILE
    
    if [ -f $CONFIG_FILE ]
    then
        echo
    else
        echo
        echo "$CONFIG_FILE does not exist"
        echo "BACKUP not completed due to missing Configuration File"
        echo
        exit
    fi
    
    FILE_NO=1
    exec < $CONFIG_FILE
    
    read FILE_NAME
    while [ $? -eq 0 ]
    do
        if [ -f $FILE_NAME -o -d $FILE_NAME ]
    then
        FILE_LIST="$FILE_LIST $FILE_NAME"
    else
        echo
        echo "$FILE_NAME, does not exist."
        echo "Obviously, I will not include it in this archive."
        echo "It is listed on line $FIlE_NO of the config file."
        echo "Continuing to build archive list…"
        echo
    	fi
    	FILE_NO=$[ $FILE_NO + 1 ]
    	read FILE_NAME
    done
    
    tar -czf $DESTINATION $FILE_LIST 2>/dev/null
    
#### 3. 运行按日期归档的脚本

#### 4. 创建按小时归档的脚本

按小时归档归档文件将很多,可以考虑构建目录层级

`mkdir -p` 创建新目录 如果目录已经存在就不再创建

    CONFIG_FILE=/home/user/archive/hourly/Files_To_Backup
    BASEDEST=/home/user/archive/hourly
    
    DAY=`date +%d`
    MONTH=`date +%m`
    TIME=`date +%K%M'
    
    mkdir -p $BASEDEST/$MONTH/$DAY
    
    DESTINATION=$BASEDEST/$MONTH/$DAY/archive$TIME.tar.gz
    
#### 5. 运行按小时归档的脚本

### 26.3 管理用户账户

### 26.3.1 需要的功能

删除用户账户

1. 获取要删除账户的正确账户名
2. 强制终止正在系统上运行的属于该账户的进程
3. 确认系统上属于该账户的所有文件
4. 删除用户账户

#### 1. 获取正确用户名

#### 2. 删除属于用户的进程

`ps -u usename` 指定用户的进程

`cut` 命令提取进程PID

#### 3. 查找属于用户账户的文件

`find` 命令查找文件

    find / -user username #查找username的文件
    
#### 4. 删除账户

`userdel username` 命令删除账户

### 26.3.2 创建脚本

____
完整的delete__user脚本
____

运行脚本




 
  
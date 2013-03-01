---
layout: post
title: "正则表达式 chapte19 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第19章 正则表达式

## 19.1 什么是正则表达式

### 19.1.1 定义

过滤文本的模式模版

### 19.1.2 正则表达式的类型

难点在于不止一种类型的正则表达式

**正则表达式引擎(regular expression engine)**,是解释正则表达式模式并使用这些模式进行文本匹配的底层软件

Linux中

* POSIX基本正则表达式(BRE)引擎
* POSIX扩展正则表达式(ERE)引擎

## 19.2 定义BRE模式

### 19.2.1 纯文本

直接用纯文本匹配,区分大小写,空格也可以匹配

### 19.2.2 特殊字符

    .*[]^${}\+?|()

这些都是特殊字符,不能在文本模式中单独使用

`\`转义

尽管`/`不是特殊字符,但是

    echo "3 / 2" | sed -n '///p'
    
是错误的,`/`在此处也要转义

    echo "3 / 2" | sed -n '/\//p'
    
### 19.2.3 锚字符

#### 1. 锁定在行首

`^`脱字符(caret character),定义行首开始模式

如果`^`不在模式的第一位,那么会被当做普通字符(如果只用了脱字符,不需要转义;如果模式首位有脱字符,那么需要转义)

#### 2. 锁定在行尾

`$`美元符定义了行尾锚定点

     echo "This is a good book" | sed -n '\book$\p'
     
#### 3. 组合锚点

    sed -n '/^this is a test$/p' data4
    
    sed '\^$\d' data5  #删除空行
    

### 19.2.4 点字符

`.`匹配任何单字符除了换行符,点字符必须匹配一个字符

### 19.2.5 字符组

字符组(character class), 使用`[]`

    sed -n '/[ch]at/p' data6
    


### 19.2.6 排除字符组

在字符组开头加**脱字符**

    sed -n '/[^ch]at/p' data6
    
### 19.2.7 使用区间

    sed -n '/^[0-9][0-9][0-9][0-9][0-9]&/p' data5
    
    sed -n '/[a-ch-m]at/p' data6 #多区间
    
### 19.2.8 特殊字符组

特殊字符组

|组             |描述                          |
|--------------|------------------------------|
|[[:alpha:]]   | 匹配任意字母字符,不管是大写还是小写|
|[[:alnum:]]   | 匹配任意字母字符和数值           |
|[[:blank:]]   | 匹配空格或制表符                |
|[[:digit:]]   | 匹配数字                       |
|[[:lower:]]   | 匹配小写字母                   |
|[[:upper:]]   | 匹配大写字母                   |
|[[:print:]]   | 匹配可打印字符                 |
|[[:punct:]]   | 匹配标点符号                   |
|[[:space:]]   | 匹配任意空白字符:空格,制表符,NL,FF,VT和CR|

### 19.2.9 星号

放置在字符后面说明该字符会在匹配模式的文本中出现0次或多次

    echo 'ik' | sed -n '/ie*k/p'
    
    echo "this is a regular pattern expression" | sed -n '
    /regular.*expression/p'
    
`.*` 结合表示任意多个任意字符

## 19.3 扩展正则表达式

POSIX ERE模式包括一些Linux应用和工具使用的若干额外符号,gawk能够识别ERE,但是sed不能

### 19.3.1 问号

问号表明前面的字符可以出现0次或1次

    echo "bt" | gawk '/be?t/{print $0}'
    
问号可以和字符组一起使用

### 19.3.2 加号

加号表明前面的字符可以出现1次或多次,至少出现一次

    echo "bet" | gawk '/be+t/{print $0}'

### 19.3.3 使用花括号

为可重复的正则表达式指定一个上限, 通常成为**区间(interval)**

* m-----正则表达式准确出现m次
* m, n -正则表达式至少出现m次,最多出现n次

默认情况下.gawk不会识别正则表达式区间,必须为gawk程序指定--re-interval命令行选项来识别正则表达式区间

    echo "bt" | gawk --re-interval '/be{1}t/{print $0}'
    
### 19.3.4 管道符号

逻辑OR

    expr1|expr2|…
    
    echo "The cat is asleep" | gawk '/cat|dog/{print $0}
    
### 19.3.5 聚合表达式

用圆括号聚合起来,该组会被当成一个特殊字符

    echo "Sat" | gawk '/Sat(urday)?/{print $0}'
    
## 19.4 实用中的正则表达式

### 19.4.1 目录文件计数

    mypath=`sed 's/:/ /g'`
    count=0
    for directory in $mypath
    do
      check=`ls directory`
      for item in $check
      do
        count=$[ $count - 1 ]
      done
      echo "$directory - $count"
      count=0
    done
    
### 19.4.2 验证电话号码

    ^\(?[2-9][0-9]{2}\)?(| |-|\.)[0-9]{3}( |-|\.)[0-9]{4}$
    
### 19.4.3 解析邮箱

    username@hostname

username    

* 点号
* 单破折线
* 加号
* 下划线

服务器名和域名

* 点号
* 下划线

解析邮箱的正则表达式

    ^([a-zA-Z0-9_\-\.\+]+)@([a-zA-Z0-9_\-\.]+)\.([a-zA-Z]{2,5})$


---
layout: post
title: "sed进阶 chapter20 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第20章 sed进阶

## 20.1 多行命令

* N: 将数据流中的下一行加进来创建一个多行组来处理
* D: 删除多行组中的一行
* P: 打印多行组中的一行

### 20.1.1 next命令

#### 1. 单行的next命令

    #data1
    This is the header line.
    
    This is a data line.
    
    This is the last line.
    #要删除第一个空行而保留第二个空行
    
    sed '/^$/d' data1 #会删除两个空行
    
    sed '/head/{n; d}' data1
    #head搜索到第一行,n跳到下一行,d删除这一行,接着命令行又从第一行开始,但是接下来没有带head的行
    
#### 2. 合并文本行

单行next命令会将数据流的下一文本行移动到sed编辑器的工作空间(模式空间). 多行版本的next命令(用大写N)会将下一文本行加到已经在模式空间中的文本上,我这样两个文本合并到一个模式空间,文本行仍然用换行符分隔,但是sed现在会将两行文本当成一行来处理

    #data2
    This is the header line.
    This is the first data line.
    This is the second data line.
    This is the last line.
    
    sed '/first/{N ; s/\n/ /}' data2
    
    #data3
    All System
    Administrators should attend this meeting.
    
    #跨行搜索
    sed '
    s/System Administrators/Desktop User/  #在一行中的情况
    N
    s/System\nAdministrators/Desktop\nUser/ #在两行的情况
    ' data3
    
### 20.1.2 多行删除命令

`d`是单行删除命令

`D` 是多行命令

    sed 'N ; /System\nAdministrator/d' data4
    #有可能把模式空间中的两行都删除
    
    #data5
    
    This is the header line.
    This is a data line.
    
    This is the last line.
    
    sed '/^$/{ N; /header/D}' data5 #删除第一行空行
    
### 20.1.3 多行打印命令

`P` 打印模式空间中多行的第一行

    sed -n 'N; /System\nAdministrator/P' data3
    #只输出含有System的那一行
    
## 20.2 保持空间

**模式空间(pattern space)**是一块活动缓冲区,在sed编辑器zhi'i执行命令时保存sed编辑器要检验的文本

**保持空间(hold space)**, 另一块活动缓冲区,可以在处理模式空间中其他行时用保持空间来临时保存一些行

sed保持空间的命令

|命令    |描述                     |
|-------|-------------------------|
|h      |将模式空间复制到保存空间     |
|H      |将模式空间附加到保持空间     |
|g      |将保持空间复制到模式空间     |
|G      |将保持空间附加到模式空间     |
|x      |交换保持空间和模式空间的内容  |

    #data2
    This is the header line.
    This is the first line.
    This is the second line.
    This is the last line.
    
    sed -n '/first/{
    h   #复制到保持空间
    p   #打印模式空间
    n   #模式空间提取文本下一行
    p   #打印模式空间
    g   #保持空间复制到模式空间
    p   #打印模式空间
    }' data2

## 20.3 排除命令

`!` 感叹号命令排除(negate)命令,让原本会起作用的命令不起作用

    sed -n '/header/!p' data2
    #含header行不打印
    
    sed 'N: s/System.Administrator/Desktop User/' data4
    The first meeting of the Linux Desktop User' group will  be held on Tuesday #这行的System Administrator改变了
    All System Administrators should attend this meeting. #这行没改,因为N命令失败,命令结束
    
    sed '$!N /System.Administrator/Desktop User/' data4
    The first meeting of the Linux Desktop User' group will  be held on Tuesday #这行的System Administrator改变了
    All Desktop Users should attend this meeting. #这行改了,因为$!N,表示最后一行不实行N命令
    
将文本按行反向

    sed -n '1!G; h; $p' data3

`tac` 命令也按行反向文本

## 20.4 改变流

sed编辑器会从脚本顶部开始执行命令并一直处理到脚本的结尾(D命令是一个例外,会强制sed编辑器返回到脚本的顶部,而不读取新的行)

sed提供了一个方法来改变命令脚本的流,实现类似结构化编程环境的结果

### 20.4.1 跳转

跳转(branch)命令`b`的格式如下

    [address]b [label]
    
address决定哪行或哪些行的数据会触发跳转,label定义了要跳转的位置,如果没有加label就跳转到脚本末尾

    sed '{2,3b ; s/This is/Is this/;s/line./test?/}' data2
    #在2,3两行命令脚本直接跳到了最后
    
标签以冒号开始,最多7个字符

    :label2
    
    sed '{/first/b jump1; s/This is the/No jump on/
    :jump1
    s/This is the/Jump here on/}' data2
    
也可以往前跳转:

    echo "This, is, a, test, to, remove, commas. " | sed -n'{
    :start
    s/.//1p
    b start
    }'   #无限循环了
    
    echo "This, is, a, test, to, remove, commas. " | sed -n'{
    :start
    s/.//1p
    /,/b start   #只有在匹配,时才跳转
    }'   
    
### 20.4.2 测试

测试(test)命令`t`也可以改变sed脚本的流,测试命令会基于替换命令的输出跳转到一个标签,而不是基于地址跳转到一个标签

如果替换命令成功匹配并替换了一个模式,测试命令就会跳转到指定的标签,如果为匹配成功就不会跳转

    [address]t [label]
    #没有指定label,匹配成功会跳转到最后
    
    sed '{
    s/first/matched/
    t
    s/This is the/No match on/
    }' data2
    
    echo "This, is, a, test, to, remove, commas"
    | sed '{
    :start
    s/,//1p
    t start
    }'
    
    
## 20.5 模式替换

### 20.5.1 and符号

and符号`&`来代表替换命令中的匹配模式

    echo "The cat sleeps in his hat" | sed 's/.at/"&"/g'
    The "cat" sleeps in his "hat"
    #&代表匹配的模式
    
### 20.5.2 替换单独的单词

用圆括号来定义替换模式的子字符串,要转义圆括号来将他们识别为聚合字符而不是普通的圆括号,这跟转义特殊字符正好相反

    echo "The System Administrator manual" | sed '
    s/\(System\) Administrator/\1 User/'
    The System User manual
    #\1来引用\(System\)匹配的部分
    
    echo "That furry cat is pretty" | sed 's/furry \(.at\)/\1/'  
    #即使用通配符又使用子串
    
    echo "1234567" | sed '{
    start
    s/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/
    t start
    }'
    
    #{}操作也要转义
    #\1代表第一部分,\2代表第二部分
    #但感觉这个有问题啊,在有多个匹配的情况下是最长匹配的?
    
## 20.6 在脚本中使用sed

### 20.6.1 使用包装脚本

可以将sed编辑器命令放到shell包装脚本(wrapper)中

在包装脚本总可以将普通的shell变量及参数和sed编辑器脚本一起使用

    #reverse
    sed -n '{
    1!G
    h
    $p
    }' $1
    
    ./reverse data2
    
### 20.6.2 重定向sed的输出

    #fact
    
    factorial=1
    counter=1
    number=$1
    
    while[ $counter -le $number ]
    do
      factorial=$[ $factorial * $counter ]
      counter=$[ $counter + 1 ]
    done
    
    result=`echo $factorial | sed '{
    :start
    s/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/
    t start
    }'`
    
    echo "The result is $result"
    
## 20.7 创建sed实用工具

### 20.7.1 加倍行间距

    sed '$!G' data2
    #出了最后一行后面不加空行,其他行后面都加空行
    
### 20.7.2 对可能含有空白行的文件加倍行间距

    sed '{/^$/d; $!G}' data6
    #先删除空白行
    
### 20.7.3 给文件中的行编号

    sed '=' data2 #在每行之前显示行号
    
    sed '=' data2 | sed 'N; s/\n/ /'
    #将行号和数据显示在同一行
    
### 20.7.4 打印末尾行

    sed -n '$p' data2  #打印末尾一行
    
打印末尾若干行,创建滚动窗口(rolling window)

    sed '{
    :start
    $q
    N
    11,$D
    b start
    }' data2 #打印末尾的10行
    
### 20.7.5 删除行

#### 1. 删除连续的空白行

    sed '/./,/^$/!d' data6
    #删除多余的空行,只有一个空行的不会被删除
    #这里有多行匹配时是用最短匹配的?
    
#### 2. 删除开头的空白行

    sed '/./,$!d' data6
    #删除开头的空白行
    
#### 3. 删除结尾的空白行

    sed '{
    :start
    /^\n*$/{$d; N; b start }
    }' data6
    #删除结尾的行
    
    
### 20.7.6 删除HTML标签

不能直接用`<`和`>`,因为是使用最长匹配,一行中如果有两个标签就不对了

    sed 's/<[^>]*>//g' data9
    #在标签里面的符号排除>号
   	

  
    
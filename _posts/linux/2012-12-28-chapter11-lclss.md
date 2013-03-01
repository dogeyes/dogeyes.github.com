---
layout: post
title: "使用结构化命令 chapter11 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, 学习, linux, command line]
---
{% include JB/setup %}

# 第11章 使用结构化命令

结构化命令（structured command）

## 11。1 使用if-then语句

    if command1
	then
	   command2
	fi

如果command的退出状态码是0，那么运行command2，否则直接跳过

    if date
	then 
	  echo "it worked"
	 fi

`then`语句块中可以有多条命令

也可以写成

    if command1; then
	    command2
	fi
	
## 11.2 if-then-else 语句

    if command1
	then
	    command2
	else
	    command3
	fi
	
## 11.3 嵌套if

    if command1
	then
	    command2
	elif  command3
	then
	    more commands
	fi
	
## 11.4 test命令

`if-then`不能测试跟命令的退出状态码无关的条件

`test` 命令提供了在`if-then` 语句总测试不同条件的途径.

`test condition` 

    if test condition
	then
	    commands
	fi
	
bash提供了一种更为方便的方法

    if [ condition ]   注意左括号右侧和右括号左侧有空格
	then
	    commands
	fi
	
`test`命令可以判断3类条件

* 数值比较
* 字符串比较
* 文件比较

### 11.4.1 数值比较

* n1 -eq n2
* n1 -ge n2
* n1 -gt n2
* n1 -le n2
* n1 -lt n2
* n1 -ne n2

test 无法处理由`bc`计算出来的浮点数

bash中只能使用整数

### 11.4.2 字符串比较

* str1 = str2
* str1 != str2
* str1 < str2
* str1 > str2
* -n str1  检查str1的长度是否非0
* -z str1  检查str1的长度是否为0

谨慎,这里大于号小于号要转义,否则会被当成重定向

    if [ baseball > hockey ]
	then
	    echo wrong
	else
	    echo right
	fi
	
会输出wrong,但事实上 baseball 比 hockey小, `>`被解释为重定向输出,产生了hockey文件,命令被执行完成了,所以状态码是`0`

    if [ baseball \> hockey ]
	then
	    echo wrong
	else
	    echo right
	fi
	
<del>这里的大小写处理方式和sort不同, 这里是大写< 小写, 而sort是小写 < 大写</del>

貌似现在一致了啊,都是按照ascii来进行了

如果用数学比较符号来比较数值,会被当成字符串,数值比较要用文本代码,这里好诡异

通过`-n` 和 `-z`来确定变量是否有值

### 11.4.3 文件比较

* -d file 检查file是否存在并是一个目录
* -e file 检查file是否存在
* -f file 检查file是否存在并是一个文件
* -r file 检查file是否存在并可读
* -s file 检查file是否存在并非空
* -w file 检查file是否存在并且可读
* -x file 检查file是否存在并且可执行
* -O file 检查file是否存在并属当前用户所有
* -G file 检查file是否存在并且默认组与当前用户相同
* file1 -nt file2 检查file1是否比file2新
* file1 -ot file2 检查file1是否比file2旧

在检查 `-nt` 和 `-ot`之前先确认文件存在

## 11.5 复合条件检测

* [ condition1 ] && [ condition2 ]
* [ condition1 ] || [ condition2 ] 

## 11.6 if-then的高级特性

* 用于数学表达式的双尖括号
* 用于高级字符串处理功能的双方括号

### 11.6.1 使用双尖括号

`(( expression ))` 允许更高级数学表达式放入比较中

*****
双尖括号命令符号
*****

不需要将双尖括号的大于号转义

    val1=10
    if (( $val1 ** 2 > 90 ))
	then
	    (( val2 = $val1 ** 2 ))
		echo "The square of $val1 is $val2"
	fi

### 11.6.2 使用双方括号

`[[ experssion ]]` 提供了针对字符串比较的高级特性

`模式匹配(pattern matching)`

    if [[ $USER == d* ]] 
	then
	   echo "Hello $USER"
	fi
	
## 11.7 case命令

    case variable in
	pattern1 | pattern2) commands1;;
	patter3) command2;;
	*) default command;;
	esac


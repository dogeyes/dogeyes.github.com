---
layout: post
title: "更多的结构化命令 chapte12 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第12章 更多的结构化命令

## 12.1 for命令

    for var in list
	do
	  commands
	done
	
list参数指定了迭代中要用的一系列值,可以通过几种不同的途径来指定列表中的值.

var包含列表中的当前值,第一个迭代使用列表中的第一个值,第二个迭代用列表中的第二个值

    for var in list; do

### 12.1.1 读取列表中的值

直接列出来

    for test in A B C D E F
	do
	  echo $test
	done

退出循环后test保持最终的值F,并且可以在后面的命令中使用

### 12.1.2 读取列表中复杂的值

列表中的值有`'`, `'`会被忽略,要用`"`来引用包含`'`的部分,或这用`\'`转义

列表中的值是按**空格**来分的,如果元素本身含有空格,那得到的结果不是我们想要的,用`"`或者`'`来将整个值包围

### 12.1.3 从变量读取列表

可以将一系列值都存储在一个变量中

    list="A B C D E F G"
	list=$list" H"   拼接字符串的常用方法
	for test in $list
	do
	  echo $test
	done
	
### 12.1.4 从命令读取

还记得`` ` ``可以用来引用任何命令输出的结果吗,这个结果也可以用在循环列表中

    for state in `cat $file`
	do
	  echo "Visit beautiful $state"
	done
	
输出file变量中存储的文件中的内容,貌似是按单词分的而不是按行分的

### 12.1.3 更改字段分隔符

环境变量IFS, **内部字段分割符(internal field separator)**, IFS定义了bash shell用作字段分隔符的一系列字符

默认情况下使用**空格**,**制表符**,**换行符** 作为分割符

可以在shell脚本中临时换一下IFS

    IFS=$'\n'  不是直接 IFS='\n'这个不太搞得懂
	
一般可以先保存原来的IFS,用完后再赋值回来

    IFS.OLD=$IFS
	IFS=$'\n'
	<use the new IFS value in code>
	IFS=$IFS.OLD
	
`IFS=:`将IFS设定为`:`

`IFS=$'\n:;'"` 将换行符,冒号,分号,双引号设定为分隔符 仔细看看

### 12.1.16 用通配符读取目录

可以用for命令来自动遍历满是文件的目录,可以在文件名或路径名中使用通配符.它会强制shell使用文件扩展匹配(file globbing), 文件扩展匹配是生成匹配指定的通配符的文件名或路径名的过程

    for file in /home/dexctor/*
	do
	   if [ -d "$file" ]   这里要用双引号是因为$file可能有空格
	   then
	     echo "$file is a directory"
	   elif [ -f "$file" ]
	   then
	     echo "$file is a file"
	   fi
	done
	
还可以将目录查找方法和列表方法合并进同一个for语句中

    for file in /home/rich/.b* /home/rich/badtest
	....
	
## 12.2 C语言风格的for命令

    for (( a = 1; a < 10; a++))
	
* 给变量赋值可以用空格
* 条件中的变量不以`$`开头
* 算式不必用expr命令格式

    for (( i = 1; i <= 10; i++))
	do
	  echo "The next number is $i"
	done
	ehco "$i"
	
i最后为11

### 12.2.2 使用多个变量

    for(( a = 1, b = 10; a <= 10; a++, b--))
	do
	  echo "$a - $b"
	done
	

## 12.3 while命令

### 12.3.1 while的基本格式

    while test command
	do
	  other command
	done
	
while中定义的test命令和if-then定义的格式是一样的,和if-then语句中一样,你可以使用任何普通的bash shell命令,或者用test命令作为条件,比如变量值

while命令的关键是,指定的test命令的退出状态码必须随着循环中运行的命令改变,如果退出状态码不变,那while循环将会一直不停地循环

    var1=10
	while [ $var1 -gt 0 ]
	do
	  echo $var1
	  var1=$[ $var1 - 1 ]
	done
	
### 12.3.2 使用多个测试命令

while命令允许在while语句行定义多个测试命令,只有最后一个测试命令的状态码会被用来决定什么时候结束循环

    var1=10
	while echo $var1
	      [ $var1 -ge 0 ]
    do
	    echo "this is in the loop"
		$var1=$[ $var1 - 1 ]
	done
	
每个测试都在单独的一行上

## 12.4 until命令

until和while工作方式相反,until命令要求指定一个通常输出非零退出状态的测试命令,只有测试命令的退出状态码非零,bash shell才会指定循环中列出的那些命令,一旦测试命令返回退出状态码为0, 循环就结束了

    until test commands
	do
	   other commands
	done
	
until亦可以有多个测试命令,由最后一个命令的退出状态码决定

## 12.5 嵌套循环

**嵌套循环(nested loop)**

    for(( a = 1; a <= 3; ++a))
	do
	  echo "Starting loop $a:"
	  for(( b = 1; b <=3; ++b))
	  do
	    echo "   Inside loop: $b"
	  done
	done

## 12.6 循环处理文件数据

* 使用嵌套循环
* 修改IFS环境变量

如处理/etc/passwd文件中的数据,先用`'\n'`来划分,再用`':'`来划分,可以得到每一项

    IFS.OLD=$IFS
	IFS=$'\n'
	for entry in `cat /etc/passwd`
	do
	  echo "Values in $entry -"
	  IFS=:
	  for value in $entry
	  do
	    echo "  $value"
	  done
	done
	
## 12.7 循环控制

* break命令
* continue命令

### 12.7.1 break命令

#### 1. 跳出单个循环

#### 2. 跳出内部循环

#### 3. 跳出外部循环

`break n` 跳出n层循环,默认为1

### 12.7.2 continue命令

`contine` 也可以使用多层, `continue n`定义了要继续的循环层级.

## 12.8 处理循环的输出

可以在`done`命令之后添加一个处理命令

    for file in /home/rich*
	do
	  if [ -d "$file" ]
	  then
	    ehco "$file is a directory"
	  elif
	    echo "$file is a file"
	  fi
	done > output.txt
	
shell会将for命令的结果重定向到文件output.txt中.

还可以管接到另一个命令

'done | sort'



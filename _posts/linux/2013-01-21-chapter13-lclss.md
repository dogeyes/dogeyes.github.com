---
layout: post
title: "处理用户输入 chapte13 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第13章 处理用户输入

## 13.1 命令行参数

    ./addem 10 30

向脚本addem传递了两个参数10和30

### 13.1.1 读取参数

`位置参数(positional parameter)` 分配给命令行输入的所有参数

`$0`是程序名,`$1`是第一个参数,`$2`是第二个参数,...,`$9`是第九个参数 (最多只有9个参数吗?)

    factorial=1
    for(( number = 1; number <= $1; number++)
    do
      factorial=$$[ $factorial *  $number ]
    done
    echo The factorial of $1 is $factorial

多个命令行参数

    total=$[ $1 * $2 ]
    echo the first parameter is $1
    echo the second parameter is $2
    echo the total value is $total

参数当然也可以是**字符串**

参数使用空格分隔的,当一个参数中包含空格时,参数要用引号包含

当参数多于9个的时候还是可以应用,但是方式要简单转换

    ${11}


### 13.1.2 读取程序名

`$0`参数来获取程序的名字

    echo The command entered is: $0

但是
	./test    调用的结果是./test

用
    /home/rich/test 调用的结果是/home/rich/test


使用`basename`命令,只返回程序名而不包括路径

    name=`basename $0`
	echo the command entered is: $name

这样返回的就是test了

### 13.1.3 测试参数

使用数据之前有必要测试数据是否存在

    if [ -n $1 ]
	then
		echo Hello $1, glad to meet you
	else
        echo "Sorry, you did not identify yourself."
    fi

## 13.2 特殊参数变量

### 13.2.1 参数计数

`$#` 特殊变量含有脚本运行时就有的命令行参数个数

    if [ $# -ne 2 ]
    then
       echo Usage: test9 a b
    else
        total=$[ $1 + $2 ]
        echo the total is $total
    fi

取命令行最后一个参数

    ${!#}   注意$#不能使用,换成!#才能正常工作

### 13.2.2 抓取所有的数据

`$*`和`$@`抓取命令行上所有参数

`$*` 将所有参数存储为*一个*字符串

`$@` 将所有参数存为一张表,参数之间相互分离

    count=1
    for param in "$*"
    do
       echo "\$* Parameter #$count = $param" 这里只执行一次
       count=$[ $count + 1 ]
    done

    count=1
    for param in "$@"
    do 
        echo "\$@ Parameter #$count = $param" 执行$#次
        count=$[ $count + 1 ]
    done

## 13.3 移动变量

`shift` 命令会根据它们的相对位置来移动命令行参数

默认情况下将每个参数变量减一,$3会变成$2,$2会变成$1,$1被删除,$0还是命令的名称

    count=1
    while [ -n "$1" ]
    do
        echo "Parameter #$count = $1"
        count=$[ $count + 1 ]
        shift
    done

`shift n` 来移动n位


## 13.4 处理选项

### 13.4.1 查找选项

#### 1. 处理简单的选项

可以像处理参数一样用`shift`来处理选项

    while [ -n "$1" ]
    do
        case "$1" in
        -a) echo "Found the -a option" ;;
		-b) echo "Found the -b option" ;;
        -c) echo "Fount the -c option" ;;
        *) echo "$1 is not an option" ;;
        esac
        shift
    done

#### 2. 分离参数和选项

`--`来表明选项结束,参数开始

    while [ -n "$1" ]
	do
        case "$1" in
        -a) echo "Found the -a option" ;;
        -b) echo "Found the -b option" ;;
        -c) echo "Found the -c option" ;;
        --) shift
            break ;;
        *) echo "$1 is not an option" ;;
		esac
		shift
	done

	count=1
    for param in $@
    do
        echo "Parameter #$count: $param"
        count=$[ $count + 1 ]
	done

#### 3. 处理带值的选项

    ./test -a test1 -b -c -d test2

有些选项会带上一个额外的参数值

    while [ -n "$1" ]
	do
		case "$1" in
		-a) echo "Found the -a option";;
		-b) param="$2"
			echo "Found the -b option, with parameter value $param"
			shift 2;;
		-c) echo "Found the -c option";;
		*) echo "$1 is not an option";;
		esac
		shift
	done

	count=1
	for param in "$@"
	do
		echo "Parameter #$count: $param"
		count=$[ $count + 1 ]
	done
	
但是不能处理多个选项合并在一起的情况

    ./test -ac

### 13.4.2 使用getopt命令

识别命令行参数

#### 1. 命令的格式

`getopt options optstring parameters`

optstring 定义了命令行有效的选项字母, 还定义了哪些选项字母需要参数

`getopt ab:cd -a -b test1 -cd test2 test3` 冒号表示有参数

上面的optstring定义了4个有效选项字母, a,b,c,d,b需要一个参数,会将-cd自动分成两个单独的选项,并插入双破折号来分开行中的额外参数.

如果指定了一个不再optstring中的选项,默认情况下会产生一条错误

    getopt ab:cd -a -b test1 -cde test2 test3
    getopt: invalid option --e
    -a -b test1 -c -d -- test2 test3

在命令后面加`-q`忽略这条信息

	getopt -q ab:cd -a -b test1 -cde test2 test3

#### 2. 在脚本中使用getopt

使用getopt生成格式化输入给脚本的命令行选项,替换已有的命令行选项和参数

`set`命令的选项之一'--', 会将命令行参数替换成set命令的命令行的值

    set -- `getopts -q ab:cd "$@"`

原始的命令行参数变量的值会被getopt命令的输出替换,而getopt已经格式化了命令行参数

    set -- `getopt -q ab:c "$@"`
	while [ -n "$1" ]
	do
		case "$1" in
		-a) echo "Found the -a option";;
		-b) param="$2"
			echo "Found the -b option, with parameter value $param";;
		-c) echo "Found the -c option";;
		--) shift
			break;;
		*) echo "$1 is not an option"
		esac
		shift
	done

	count=1
	for param in "$@"
	do
		echo "Parameter #$count: $param"
		count=$[ $count + 1 ]
	done

getopt不擅长处理带空格的参数值,它会将空格当成参数的分隔符,而不是根据双引号将二者当成一个参数.

### 13.4.2 使用更高级的getopts

`getopts optstring variable`

optstring 和getopt中的optstring类似. 要去掉错误消息,在optstring之前加一个冒号., getopts会将当前参数保存在命令行中定义的`variable`中

getopts命令会用到两个环境变量,如果选项需要一个参数值,`OPTARG`环境变量会保存这个值, `OPTIND`环境变量保存了参数列表中getopts正在处理的参数位置

    while getopts :ab:c opt #最前面的:是忽略错误消息,当前选项在opt中
	do
		case "$opt" in
		a) echo "Found the -a option";;
		b) echo "Found the -b option, with value $OPTARG";;
		c) echo "Found the -c option";;
		*) echo "Unknown option: $opt";;
		esac
	done

getopts会返回状态码

getopts会移除选项开头的`-`

getopts可以处理带空格的命令参数

getopts将命令行上未定义的选项同一输出成问号

在处理完选项后可以使用OPTIND和shift来移去已经处理过的选项

## 13.5 将选项标准化

当然可以自定义所有选项,但是有些字母选项已然约定俗成,遵守这样的规则可以更友好

## 13.6 获得用户的输入

`read`

### 13.6.1 基本的读取

`read`命令接受从标准输入或另一个文件描述符的输入,在收到输入后,read命令会将数据放进一个标准变量

    echo -n "Enter your name"
	read name #读入的值存在name中
	echo "Hello $name, welcome to my program."

`echo -n`移除了末尾的换行符,是的输出末尾不换行.

`read -p "Please enter your age: " age # 直接指定了提示符`

`read -p "Please enter your age: " first last #可以指定多个变量,如果输入的值多余变量值,那么多多的值会被分配到最后一个变量中.

如果不指定任何变量,那么得到的数据将被存储在**REPLY**环境变量中

### 13.6.2 超时

`-t`选项指定read命令等待输入的秒数,当计时器过期后,read命令会返回一个非零状态退出码

    if read -t 5 -p "Please enter your name: " name
	then
		echo "Hello $name, welcome to my script"
	else
		echo
		echo "Sorry. too slow!"
	fi

可以让read命令来对输入的字符进行计数,而非对输入过程进行计时,当输入的字符达到预设的字符数时,它会自动退出,将输入的数据赋给变量.

    read -n1 -p "Do you want to continue[Y/N]? " answer
    case $answer in
	Y | y) echo
		   echo "fine. continue on...";;
	N | n) echo
		   echo OK.goodbye
		   exit;;	
	esac
	echo "This is the end of the script"

n1表示输入一个字符read就结束

### 13.6.3 隐藏方式读取

`-s`选项阻止将read命令的数据显示在显示器上(实际上,数据会被显示,read将文本颜色设成跟背景颜色一样)

    read -s -p "Enter your password: " pass
	echo
	echo "Is your password really $pass? "

### 13.6.4 从文件中读取

用`cat`命令将文件传给`read`命令,`read`每次读一行

    count=1
    cat test | while read line
	do
		echo "Line $count: $line"
		count=$[ $count + 1 ]
	done
	echo "Finished processing the file"

		
---
layout: post
title: "初识sed和gawk chapte18 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第18章 初识sed和gawk

## 18.1 文本处理

命令行编辑器: sed和gawk

### 18.1.1 sed编辑器

流编辑器(stream editor), 在编辑器处理数据之前基于预先提供的一组规则来编辑数据流

    sed options script file
    
sed命令行选项

|选项    | 描述    |
|-------|--------|
|-e script | 在处理输入时,将script中指定的命令添加到运行的命令中|
|-f file |在处理输入时,将file中指定的命令添加到运行的命令中|
|-n | 不要为每个命令生成输出,等待print命令来输出

script参数指定了将作用在流数据上的单个命令,如果需要多个命令,必须用-e选项或者-f选项

#### 1. 命令行定义编辑器命令

默认情况下,sed编辑器会将制定的命令应用于STDIN输入流,因此可以直接使用流控制

    echo "This is a test" | sed 's/test/big test/'
    
`s/string/new string/` 替换

#### 2. 在命令行使用多个编辑器命令

`-e`选项

    sed -e 's/brown/green/; s/dog/cat/' date1
    
命令行之间用`;`,命令末尾和分号之间不能有空格

可以用次提示符分割命令

    sed -e '
    s/brown/green/
    s/fox/elephant/
    s/dog/cat/' date1
    
#### 3. 从文件中读取编辑器命令

`-f` 选项

	#script1
	s/brown/green/
	s/fox/elephone/
	s/dog/cat/
	
    sed -f script1 data1

### 18.1.2 gawk程序

awk程序的GNU版本

* 定义变量保存数据
* 使用算术和字符串操作来处理数据
* 使用结构化编程概念,比如if-then语句和循环语句,来为数据处理增加逻辑
* 提取数据文件中的数据元素并将他们按另一顺序或格式重新放置,从而生成格式化报告

#### 1. gawk命令格式

    gawk options program file
    
gawk选项

|选项          |描述                            |
|-------------|---------                      |
|-F fs        |指定行中分割数据字段的字段分隔符     |
|-f file      |指定读取程序的文件名               |
|-v var=value |定义gawk程序中的一个变量及其默认值  |
|-mf N        |指定要处理的数据文件中的最大字段数   |
|-mr N        |指定数据文件中的最大数据行数        |
|-W keyword   |指定gawk的兼容模式和警告等级       |

#### 2. 从命令行读取程序脚本

    gawk '{print "Hello John!"}
    
EOF(CTRL-D)

#### 3. 使用数据字段变量

每个数据字段都有一个变量

* $0代表整个文本行
* $1 代表第一个数据字段
* $2 代表第二个数据字段
* $n 代表第n个数据字段

`字段分隔符`来划分数据字段

默认字段分隔符是任意的空白字符

    #data3
    One line of test text
    Two lines of test text
    Three lines of test text
    
    gawk '{print $1}' data3
    One
    Two
    Three
    
`-F:`选项将分隔符换成`:`

#### 4. 在程序脚本中使用多个命令

    echo "My name is Rich" | gawk '{$4="Christine"; print $0}'
    
    gawk '{
    $4="testing"
    print $0 }'
    This is not a good test
    >This is not testing good test
    
#### 5. 在文件中读取程序

    #script2
    { print $1 "'s home directory is " $6}
    
    gawk -F: -f script2 /etc/passwd
    
    {
    text = "'s home directory is "
    print $1 text $6   #注意对text变量的引用不需要加$符号
    }
    gawk -F: -f script3 /etc/passwd
    
    
#### 6. 在处理数据前运行脚本

`BEGIN` 关键字,强制gawk在读取数据前执行BEGIN关键字后指定的程序脚本

    gawk 'BEGIN {print "Hello World!"}'
    
    #data4
    Line 1
    Line 2
    Line 3
    gawk 'BEGIN {print "The data4 File Contents: "} { print $0 }' data4
    
#### 7.在处理数据后运行脚本

`END`关键字

    #data4
    Line 1
    Line 2
    Line 3
    gawk 'BEGIN {print "The data4 File Contents: "} { print $0 } END { print "End of File"} ' data4

    #script4
    BEGIN {
    print "The latest list of users and shells"
    print " Userid            shell"
    print "---------         ------"
    FS=":"
    }
    {
    print $1 "    " $7
    }
    END {
    print "This concludes the listing"
    }
    
## 18.2 sed编辑器基础

### 18.2.1 更多的替换选项

s(substitute)

#### 1. 替换标记

    sed 's/test/trial/' data5
    
默认情况只替换每行的第一个test

`替换标记`

    s/pattern/replacement/flags
    
flags

* 数字, 表明新文本将替换第几处模式匹配的地方
* g, 新文本替换所有已有文本出现的地方
* p, 原来行的内容要打印出来
* w file, 将替换结果写到文件中

`-n`选项禁止sed输出,`p`标记会输出修改过的行,两者配合使用则只输出被substitute命令修改过的行

w标记会产生同样的输出,不过会输出保存到指定的文件,只有那些包含模式匹配的行才会保存在指定的输出文件中

#### 2. 替换字符

sed 处理中用`/`来分割pattern和replacement,如果pattern或replacement中有`/`(如目录),就需要有`\`来转义

    sed `s/\/bin\/bash/\/bin\/csh/' /etc/passwd
    
sed允许substitute命令中用其他字符来进行分割

    sed `s!/bin/bash!/bin/csh!` /etc/passwd
    
这里使用`!`来进行分割

### 18.2.2 使用地址

默认命令会作用于所有行, 如果只想命令作用于特定行或者某些行, 要使用**行寻址(line addressing)**

* 行的数字范围
* 用文本模式来过滤出某行

    [address]command
    或
    address {
    command1
    command2
    }
    
#### 1. 数字方式的行寻址

    sed '2s/dog/cat/' data1
    
    sed '2,3s/dog/cat/' data1 #使用范围,注意是逗号
    
    sed '2,$s/dog/cat/' data1 #到末尾
    
#### 2. 使用文本模式过滤器

    /pattern/command
    
使用文本模式来过滤出命令要作用的行

    sed '/pattern/s/string/replace/' file
    
pattern可以使用正则表达式(regular expression)

#### 3. 组合命令

    sed '2{
    s/fox/elephant/
    s/dog/cat/
    }'
    
多条命令

### 18.2.3 删除行

`delete`命令, 删除匹配的行


    sed '3d' data7  #删除第3行
    
    sed '/pattern/d' file
    
用两个文本模式删除某个范围内的行

    sed '/1/,/3/d' data6  #删除匹配模式/1/到匹配模式/3/之间的所有行,包括这两行
    
/1/打开行删除,/3/关闭行删除,下一个匹配/1/时,删除行模式又被打开,直到下一个/3/

### 18.2.3 插入和附加文本

* 插入(insert)命令`i`会在指定行前增加一个新行
* 追加(append)命令`a`会在指定行后增加一个新行

格式

    sed '[address]command\
    new line'
    
    echo "Test Line 2" | sed 'i\Test line 1'
    
    echo "Test line 2" | sed 'a\Test line 1'
    
    echo "Test line2"  | sed 'i\
    Test line 1'
    
指定行地址, 可以是数字行号或文本模式,但不能用地址区间

    sed '3i\
    This is an inserted line.' data3 #插入到第三行前
    
    sed '3i\
    This is an inserted line.' data3 #插入到第三行之后
    
插入多行,用`\`隔开

    sed '1i\
    This is one line of new text.\
    This is another line of new text.' data7
    
### 18.2.5 修改行

`change`命令

    sed '3c\
    This is a changed line of text.' data7
    

也可使用文本模式

    sed '/number 3/c\
    This is a changed line of text.' data7
    
修改匹配的所有行

可以使用地址区间,但是用一行文本来替换整个区间

### 18.2.6 转换命令

转换(transform, y)命令是唯一可以处理单个字符的sed编辑器命令

    [address]y/inchars/outchars/
    
inchars到outchars是一一映射

    sed 'y/123/789/' data7
    
以一整行为单位,无法只转换一行当中的一部分

### 18.2.7 回顾打印

* 小写p命令用来打印文本行
* 等号(=)命令用来打印行号
* l命令用来列出行号

#### 1. 打印行

    echo "this is a test" | sed 'p'
    
    sed -n '/number 3/p' data7 #打印包含/number 3/模式的行
    
    sed -n '2,3p' data7 #打印2,3行
    
    sed -n '/3/{
    p
    s/line/test/p
    }' data7
   	# 在修改之前查看
   	
#### 2. 打印行号

    sed '=' data7
    
    sed -n '/number4/{
    =
    p
    }' data7
    
#### 3. 列出行

列出命令(l)允许打印数据流中的文本和不可打印的ASCII字符,任何不可打印字符都用它们的八进制值加一个反斜杠或标准的C风格命令法

    This	line	contains 	tabs
    sed -n 'l' data9
    This\tline\tcontains\ttabs$
    
### 18.2.8 用sed和文件一起工作

#### 1. 向文件写入

`w`命令,向文件写入行

    [address]w filename
    
    sed '2,3w test' data7  #将data7的2,3行写入test文件
    
    sed -n '/IN/w INcustomers' data11 #将含有IN的行写入到INcustomers文件
    
#### 2. 从文件读取

`r`命令,从文件读取

    [address]r filename
    
不能读取一个区间,只能读取单独一个行号或者文本模式地址,sed会将文件中的文本插入到地址后

    sed '3r data12' data7
    
比较好的用途是结合删除命令来替换文件中的数据

    #letter
    Would the following people:
    LIST
    please report to the office.
    
    sed '/LIST/{
    r data11
    d
    }' letter
    
通过`r`命令来插入,在删除占位符


    
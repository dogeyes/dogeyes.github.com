---
layout: post
title: "gawk进阶 chapter21 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第21章 gawk进阶

## 21.1 使用变量

* 内建变量
* 自定义变量

### 21.1.1 内建变量

#### 1. 字段和数据行分隔符变量

数据字段变量, $1代表第一个,$2代表第二个…, $0代表整行

字段由字段分隔符划定,默认是空白字符,通过-F选项或者FS变量来更改

gawk数据字段和数据行变量

|变量     |描述    |
|--------|--------|
|FIELDIDTHS|由空格分隔开的定义了灭个数据字段确切宽度的一列数字|
|FS      |输入字段分隔符|
|RS      |输入数据行分隔符|
|OFS     |输出字段分隔符  |
|ORS     |输出数据行分隔符|

OFS默认是一个空格

print命令会自动将OFS变量的值放置在输出的每个字段间

    gawk 'BEGIN{FS=","; OFS="-"} {print $1,$2,$3}' data1
    
FIELDWIDTHS变量,不用字段分隔符来划分字段

    #data1b
    1005.3247596.37
    115-2.349194.00
    05810.1298100.1
    
    gawk 'BEGIN{FIELDWIDTHS="3 5 2 5"} {print $1,$2,$3,$4}' data1b
    
    FIELDWIDTHS一旦设定,就不能改变,这种方法不适合边长字段
    
RS和ORS定了如何处理数据流中的数据行,默认情况下,RS和ORS设为换行符

有时数据项按行分隔, 而数据组之间有空行分隔,这时把FS设为换行符,RS设为空字符串

#### 2. 数据变量

___
更多gawk内建变量
___

    gawk 'BEGIN{print ARGC.ARBV[1]}' data1
    >data1
    
ARGC变量表明命令行上有两个参数,gawk和data1参数,程序脚本并不算参数,ARGV数组从代表该命令的索引0开始

gawk变量中,变量名前不加美元符号

    gawk '
    BEGIN{
    print ENVIRON["HOME"]
    print ENVIRON["PATH"]
    }'
    
    gawk 'BEGIN{
    FS=":"
    OFS=":"}
    { print $1,$NF}' /etc/passwd
    
`NF`是最后一个数据字段的数字值,通过`$NF`来引用最后一个字段

`NR`是处理过的所有数据行总数

`FNR`是当前数据文件处理过的总的数据行总数

    #在同时处理多个文件的时候会有差别
    gawk 'BEGIN{FS=","}{print $1, "FNR"=FNR, "NR"=NR}' data1 data1
    
    data11 FNR=1 NR=1
    data21 FNR=2 NR=2
    data31 FNR=3 NR=3
    data11 FNR=1 NR=4
    data21 FNR=2 NR=5
    data31 FNR=3 NR=6
    
### 21.1.2 自定义变量

#### 1. 在脚本中给变量赋值

    gawk 'BEGIN{x=4; x=x * 2 + 3; print x}'
    
#### 2. 在命令行上给变量赋值

    #script1
    BEGIN{FS=","}
    {print $n}
    
    gawk -f script1 n=2 data1
    
    gawk -f script2 n=3 data1
    
但是这个值在BEGIN部分不可用

    #script2
    BEGIN{print "The starting value is",n; FS=","}
    {print $n}
    
    gawk -f script2 n=3 data1
    #第一行The starting value is#没有输出n
    
通过`-v`选项来解决这个问题

    gawk -v n=3 -f script2 data1
    
## 21.2 处理数组

关联数组

### 21.2.1 定义数组变量

    var[index]=element
    
    gawk 'BEGIN{
    capital["Illinois"] = "Springfield"
    print capital["Illinois"]
    }'
    
### 21.2.2 遍历数组变量

    for (var in array)
    {
      statements
    }
    
var是遍历索引而不是值

    gawk 'BEGIN{
    var["a"]=1
    var["g"]=2
    var["m"]=3
    var["u"]=4
    for(test in var)
    {
      print "Index:",test," - Value:", var[test]
    }
    }'
    
返回的索引值不是按顺序的

### 21.2.2 删除数组变量

    delete array[index]
    
## 21.3 使用模式

### 21.3.1 正则表达式

基本正则表达式(BRE) 扩展正则表达式(ERE)

    gawk 'BEGIN{FS=","} /11/{print $1}' data1
    
正则表达式会对数据中的所有数据字段进行匹配,包括分隔符

### 21.3.2 匹配操作符

`~`匹配操作符(matching operator)允许将正则表达式限定在数据行中的特性数据字段

    $1 ~ /^data/
    
    gawk 'BEGIN{FS=","} $2 ~ /^data2/{print $0}' data1
    
    gawk -F: '$1 ~ /rich/{print $1,$NF}' /etc/passwd
    
可以使用排除

    gawk -F: '$! !~ /rich/{print $1,$NF}' /etc/passwd
    
    #输出不匹配的行
    
### 21.3.3 数学表达式

可以在匹配模式中使用数学表达式

    gawk -F: '$4 == 0{print $1}' /etc/passwd
    #PID==0的行
    
* x == y
* x <= y
* x >= y
* x < y
* x > y

也可对文本数据使用表达式,但是要完全相同才算匹配

## 21.4 结构化命令

### 21.4.1 if语句

    if(condition)
        statement1
        
    if(condition) statement1
    
例子

    #data4
    10
    5
    13
    50
    34
    gawk '{if ($1 > 20) print $1}' data4
    50
    34
    
    gawk '{
    if($1 >  20)
    {
      x = $1 * 2;
      print x
    } #多条语句用{}
    }' data4
    
也可以加else语句

    gawk '{
    if($1 > 20)
    {
      x = $1 * 2
      print x
    }else
    {
      x = $1 / 2
      print x
    }}' data4
    
单行

    if(condition) statement1; else statement2
    
### 21.4.2 while语句

    while (condition)
    {
      statement
    }
    
    #data5
    130 120 135
    160 113 140
    145 170 215
    
    gawk '{
    total=0
    i=1
    while (i < 4)
    {
      total+= $i
      i++
    }
    avg = total / 3
    print "Average: ",avg
    }' data5
    #求每行平均
    
支持在while中使用continue和break

### 21.4.3 do-while 语句

    do
    {
        statements
    }while(condition)
    
### 21.4.4 for语句

    for(variable assignment; condition; iteration process)
    
## 21.5 格式化打印

`printf` 命令

     printf "fomat string", var1, var2, …
     
格式化指定符采用如下格式:

    %[modifier]control-letter
    
control-letter(控制字符),和c中的printf相似

modifier(修饰符),和c中也类似

## 21.6 内建函数

### 21.6.1 数学函数

* atan2(x,y)
* cos(x)
* exp(x)
* int(x)
* log(x)
* rand()
* sin(x)
* sqrt(x)
* srand(x)

* and(v1, v2) 按位与
* compl(val)  补运算
* lshift(val, count) 左移
* rshift(val, count) 右移
* or(v1, v2)  按位或
* xor(v1, v2) 按位抑或

### 21.6.2 字符串函数

gawk字符串函数

|函数      |描述              |
|---------|-----------------|
|asort(s [, d])|将数组s按数据元素排序,索引值会被替换成表示新的排序顺序的连续数字,另外,如果指定了d,则排序后的数组会存储在数组d中|
|asorti(s [, d])|将数组s按索引值排序,生成的数组会将索引值作为数据元素值,用连续数字索引来表名排序顺序,另外如果指定了d,排序后的数组会存储在数组d中|
|gensub(r, s, h [, t])|查找变量$0或目标字符串t(如果提供了的话)来匹配正则表达式r,如果h是以一个g或G开头的字符串,就用s替换掉匹配的文本,如果h是一个数字,它表示要替换掉第几处r匹配的地方|
|gsub(r, s [, t])| 查找变量$0或目标字符串t(如果提供了的话)来匹配正则表达式r,如果找到了就全部替换成s|
|index(s, t)| 返回t在s中的索引,如果没有找到就返回0|
|length([s])| 返回s的长度,如果没有指定就放回$0的字符串|
|match(s, r [, a])|返回字符串s中正则表达式r出现位置的索引,如果指定了数组a,它会存储s中匹配正则表达式的那部分|
|split(s, a[ ,r])|将s用FS字符或正则表达式r(如果指定了的话)分开放到数组a中,返回字段的总数|
|sprintf(format, variable)|用format和variables返回一个类似于printf输出的字符串|
|sub(r, s [, t])|在变量$0或目标字符串t中查找正则表达式r和匹配,如果找到了,就用字符串s替换掉第一处匹配|
|substr(s, i [, n])|返回s中从索引值i开始的n个字符串, 如果未提供n,则返回s的剩余部分|
|tolower(s)|将s中的所有字符换成小写|
|toupper(s)|将s中的所有字符换成大写|

### 21.6.3 时间函数

|函数|描述|
|---|----|
|mktime(datespec)|将一个按YYYY MM DD HH MM SS[DST]格式指定的日期转换为时间戳值|
|strftime(format [, timestamp])|将当前时间的时间戳或timestamp(如果提供了的话)转化成shell函数格式date()的格式化日期|
|systime()|返回当前时间的时间戳|

## 21.7 自定义函数

### 21.7.1 定义函数

    function name([variable])
    {
        statements
    }
    
可以在函数中使用外部变量,如$1,$2等字段值

### 21.7.2 使用自定义函数

定义函数必须出现在所有代码块之前(包括BEGIN代码块)

    gawk '
    function myprint()
    {
    	printf "%-16s - %s\n", $1, $4
    }
    BEGIN{FS="\n"; RS=""}
    {
        myprint()
    }' data2
    
### 21.7.3 创建函数库

    #funclib
    gawk '
    function myprint()
    {
    	printf "%-16s - %s\n", $1, $4
    } 
    function myrand(limit)
    {
        return int(limit * rand())
    }
    function printthird()
    {
        print $3
    }
    
    gawk -f funclib -f script4 data2
    #多个-f选项指定多个文件
    

    
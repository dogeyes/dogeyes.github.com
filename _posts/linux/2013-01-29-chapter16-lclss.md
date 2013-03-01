---
layout: post
title: "创建函数 chapte16 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第16章 创建函数

## 16.1 基本的脚本函数

函数(function) 

### 16.1.1 创建函数

    function name {
        commands
    }
    
    name() {
        commands
    }
    
### 16.1.2 使用函数

跟使用其他shell命令一样

函数要在定义后才能使用,而定义只要在使用之前就行

定义了两个相同的函数,第一个函数将被覆盖

## 16.2 返回值

bash shell 把函数当成小型脚本,运行结束时会返回一个退出状态码

### 16.2.1 默认退出状态码

默认状态,是最后一条命令返回的退出状态码.

在函数执行结束厚,可以用`$?`变量来决定函数的确定函数的退出状态码

    func1() {
      echo "trying to display a not-existent file"
      ls -l badfile
    }
    
    echo "testing the function"
    
    func1
    
    echo "The exit status is: $?"
    
只能确定最后一条的退出状态码,不能确定是否所有命令都运行正确

### 16.2.2 使用return命令

return可以指定一个退组状态码

    function dbl {
      read -p "Enter a value: " value
      echo "doubling the value"
      return $[ $value * 2 ]
    }
    
    dbl
    echo "The new value is $?"
    
* 要立即取返回值,否则可能被覆盖
* 返回值要在0-255之间

### 16.2.3 使用函数输出

    result=`dbl`  类似于脚本输出的保存
    
    function dbl {
        read -p "Enter a value: " value
        echo $[ $value * 2 ]
    }
    
    result=`dbl`
    echo "The new value is $result"
    
这个技术函数最后一行要使用echo

## 16.3 在函数中使用变量

### 16.3.1 向函数传递参数

函数可以使用标准的参数环境变量来代表命令行上传给函数的参数(函数名在$0中,参数在$1,$2…),亦可使用特殊变量$#来判断传给函数的参数数目(和传给脚本的参数相似)

    function addem{
      if [ $# -eq 0 ] || [ $# -gt 2 ]
      then
        echo -1
      elif
      then
        echo $[ $1 + $1 ]
      else
        echo $[ $1 + $2 ]
      fi
    }
    
    echo -n "Adding 10 and 15: "
    value=`addem 10 15`
    echo $value
    echo -n "Let's try adding just one number: "
    value=`addem 10`
    echo $value
    echo -n "Now trying adding no numbers: "
    value=`addem`
    echo $value
    echo -n "Finally, try adding three numbers: "
    value=`addem 10 15 20`
    echo $value
    
shell脚本的空格很是麻烦啊

因为函数使用特殊的环境变量作为自己的参数值,因此函数不能直接从脚本的命令行获取脚本的参数值

要通过传递来实现

    function fun7{
      echo $[ $1 * $2 ]
    }   
    
    if [ $# -eq 2 ]
    then
    #    value=`fun7` 错误
      value=`fun7 $1 $2`
      echo "The result is $value"
    else
      echo "Usage: badtest1 a b "
      
      
### 16.3.2 在函数中处理变量

作用域

* 全局变量
* 局部变量

#### 1. 全局变量

全局有效,可以在基本主体部分定义,也可在函数中定义

默认情况下定义的变量都是全局变量,即在主体部分定义的变量,可以在函数在用使用和修改

    function func1 {
      temp=$[ $value + 5 ]
      result=$[ $temp + 2 ]
    }
    
    temp=4
    value=6
    
    func1
    echo "The result is $result"
    if [ $temp -gt $value ]
    then
      echo "temp is larger"
    else
      echo "temp is smaller"
      
temp可以在函数func1中使用和改变,result可以在主体部分使用,只会引起混乱

#### 2. 局部变量

函数中的局部变量,`local`关键字

    local temp  局部变量的定义
    
    function func1 {
      local temp=$[ $value + 5 ]
      result=$[ $temp + 2 ]
    }
    
    temp=4
    value=6
    
    func1
    echo "The result is $result"
    if [ $temp -gt $value ]
    then
      echo "temp is larger"
    else
      echo "temp is smaller"

## 16.4 属组变量和函数

### 16.4.1 向函数传数组参数

直接用一个变量传递数组,只有数组的第一个值被传送

先将数组分解成单个值将这些值作为函数参数使用,在函数内部将所有参数重组到新的数组变量中

    function testit {
        local newarray
        newarray=(`echo "$@"`)
        echo "The new array value is:  ${newarray[*]}"
    }
    
    myarray=(1 2 3 4 5)
    echo "The original array is ${myarray[*]}"
    testit ${myarray[*]}
    
### 16.4.2 从函数返回数组

    function arraybdlr {
      local origarray
      local newarray
      local elements
      local i
      
      origarray=(`echo "$@"`)
      newarray=(`echo "$@"`)
      elements=$[ $# - 1 ]
      for (( i = 0; i <= $elements; i++))
      {
        newarray[$i]=$[ ${origarray[$i]} * 2 ]
      }
      echo ${newarray[*]}
    }
    
    myarray=(1 2 3 4 5)
    echo "The original array is: ${myarray[*]}"
    arg1=`echo ${myarray[*]}`
    result=(`arrayblr $arg1`)
    echo "The new array is: ${result[*]}"
    
## 16.5 函数递归

自成体系(self-containment)

递归调用 

    function factorial {
      if[ $1 -eq 1 ]
      then
        echo 1
      else
        local temp=$[ $1 - 1 ]
        local result=`factorial temp`
        echo $[ $result * $1 ]
      fi
    }
    
    read -p "Enter value: " value
    
    result=`factorial $value`
    
    echo "The factorial of $value is: $result"
    
## 16.6 创建库

函数库

`source` 命令, 快捷别名`.`点操作符

    #myfuncs 函数库
    function addem {
      echo $[ $1 + $2 ]
    }
    function multem {
      ehco $[ $1 * $2 ]
    }
    function divem {
      echo $[ $1 / $2 ]
    }
    
    #使用函数库
    . ./myfuncs
    
    value1=10
    value2=5
    
    result1=`addem $value1 $value2`
    result2=`multem $value1 $value2`
    result3=`divem $value1 $value2`
    
    echo $result1
    echo $result2
    echo $result3
    

## 16.7 在命令行上使用函数

### 16.7.1 在命令行上创建函数

可以直接在命令行上定义函数

1. 一行内定义整个函数


    function divem { echo $[ $1 / $2 ]; }

在每个命令后加`;`

2. 多行定义函数,不需要在每行后面加`;`

如果定义的函数和内建函数名相同,内建函数会被覆盖

### 16.7.2 在.bashrc文件中定义函数

命令行上定义函数,在shell退出以后函数就消失了

#### 1. 直接定义函数

在`.bashrc`文件末尾加上定义即可

#### 2. 读取函数文件

`source`或者点操作符来加载函数文件


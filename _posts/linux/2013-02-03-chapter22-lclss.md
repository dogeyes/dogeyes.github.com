---
layout: post
title: "使用其他shell chapter22 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第22章 使用其他shell

## 22.1 什么是dash shell

大多是发行版将/bin/sh链接到/bin/bash

但是Ubuntu将/bin/sh链接到/bin/dash

## 22.2 dash shell 的特性

### 22.2.1 dash命令行参数

___
dash命令行参数
___

### 22.2.2 dash环境变量

#### 1. 默认环境变量

___
dash shell环境变量
___

* OLDPWD 上一个工作目录

dash 也用`set`命令来显示环境变量

#### 2. 位置参数

和bash类似

#### 3. 用户自定义的环境变量

和bash类似

不支持可变数组

### 22.2.3 dash内建命令

___
dash内建命令
___

## 22.3 dash脚本编程

bash脚本通常不能在dash中运行

### 22.3.1 创建dash脚本

 第一行
 
     #!/bin/dash
     
### 22.3.2 不能使用的功能

bash主义(bashism)

#### 1. 使用算术运算

bash中,算术运算

* expr
* 方括号
* 双圆括号

dash支持expr和双圆括号,**不支持方括号**

#### 2. test命令

bash的test支持`==`,而dash不支持`==`只支持`=`

#### 3. echo语句选项

bash中echo通过`-e`选项来输出转义符号, 而dash中直接就能输出转义字符,因此没有`-e`选项

#### 4. function命令

bash支持两种方式定义函数

    function name{
        statements
    }
    
    name() {
        statementes
    } 
    
    
dash只支持第二种方式定义函数

## 22.4 zsh shell

zsh的独特的功能

* 改进shell选项处理
* shell兼容性模式
* 可加载模块  命令模块(command module)

## 22.5 zsh shell的组成

### 22.5.1 shell选项

___
zsh shell命令行参数
___

`-o` 选项允许设置shell选项来定义shell的功能

选项类别

* 更改目录
* 补全
* 扩展和扩展匹配
* 历史记录
* 初始化
* 输入输出
* 作业控制
* 提示
* 脚本和函数
* shell模拟
* shell状态
* zle
* 选项别名

#### 1. shell状态选项

* 交互模式(-i , interactive)
* 登陆模式(-l, login)
* 特权模式(-p, privileged)
* 限制模式(-r, restricted)
* shin_stdin模式(-s)
* single_command模式(-t)

#### 2. shell模拟选项

提供类似于C shell或Korn shell的运行

#### 3. 初始化选项

* all_export
* global_export
* global_rcs
* rcs

#### 4. 脚本和函数选项

定制shell脚本环境

### 22.5.2 内建命令

#### 1. 核心内建命令

____
zsh核心命令
____


#### 2. 附加模块

#### 3. 查看,添加和删除模块

`zmodload`命令

默认显示当前加载模块

`zmodload 模块名` 加载新模块

`zmodload -u 模块名` 删除模块

## 22.6 zsh脚本编程

### 22.6.1 数学运算

#### 1. 进行计算

* let命令
* 双圆括号命令

    let value=" 4 * 5.1 / 3.2 "
    
支持格式化输出printf

    printf "%6.3f\n" $value
    
双圆括号

    value=$(( 4 * 5.1 ))
    ((value = 4 * 5.1 ))

#### 2. 数学函数

    zsh/mathfunc模块
    
### 22.6.2 结构化命令

* if-then-else
* for
* while
* until
* select
* case

此外还有`repeat命令

    repeat param
    do
        commands
    done
    #param是一个数或一个值为数的表达式
    
### 22.6.3 函数

支持function命令或圆括号定义函数名来创建自定义函数

`fpath`

`autoload`命令

`zcompile`命令






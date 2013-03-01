---
layout: post
title: "使用数据库 chapter23 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第23章 使用数据库

## 23.1 MySQL数据库

### 23.1.1 安装MySQL

参考第8章

### 23.1.2 MySQL客户端界面

mysql命令行界面

#### 1. 连接到服务器

___
mysql命令行参数
___

`-u` 指定登陆用户名,无该选项用Linux登陆名进行尝试

`-p` 为用户账户提示输入命令

#### 2. mysql命令

两类命令

* 特殊的mysql命令
* 标准SQL语句

___
mysql命令
___

简写命令或完整命令

    \s #status 服务器状态信息
    
MySQL支持所有标准SQL(Structured Query Language, 结构化查询语言)命令

如

`SHOW`命令, 提取关于MySQL的信息

    SHOW DATABASES;
    #显示数据库
    USE mysql;
    #连接到单个数据库mysql
        
注意命令以`;`结尾

    SHOW TABLES;
    #显示表
    
mysql支持大写或者小写指定SQL命令,但是通用方式是大写字母

### 23.1.3 创建MySQL数据库对象

* 存储应用数据的唯一数据库;
* 从脚本中访问数据库的唯一用户账户
* 组织数据的一个或多个数据表

#### 1. 创建数据库

创建一个新的数据库

    CREATE DATABASE name;
    
必须有创建数据库的权限

    SHOW DATABASES;
    #查看
    USE test;
    #使用
    
#### 2. 创建用户账户

`GRANT` SQL语句

    GRANT SELECT,INSERT,DELETE,UPDATE ON test.* TO test IDENTIFIED by 'test'
    
第一部分定义了用户账户对那些数据库有哪些权限(查询(select权限),插入,删除以及更新)

test.*项定义了权限作用的数据库和表,通过下面格式指定

    database.table
    
test.*表示权限在test数据库的所有表上

最后指定权限作用的用户账户,如果账户不存在,会直接创建账户

identified by部分为新用户账户设定默认密码

    mysql test -u test -p
    测试test数据库的test用户
    
## 23.2 PostgreSQL 数据库

### 23.2.1 安装PostgreSQl

PostgreSQL的系统管理员账户称为postgres,而不是root

PostgreSQL也支持在内部维护自己的用户数据库,但是大多数都会利用现有的Linux系统用户账户来认证PostgreSQL用户

系统上必须有一个叫做postgres的用户账户

### 23.2.2 PostgreSQL命令行界面

`psql`

#### 1. 连接服务器

____
psql命令行参数
____

    sudo -u postgres psql
    #非postgres用户登陆数据库的postgres账户
    
#### 2. psql命令

* 标准SQL语句
* PostgreSQL元命令

* \l   列出所有数据库
* \c   连接到数据库
* \dt  列出数据库中的表
* \du  列出PostgreSQL用户
* \z   列出表的权限
* \?   列出所有可能的元命令
* \h   列出所有可能的SQL命令
* \q   退出数据库


### 23.2.3 创建PostgreSQL数据库对象

#### 1. 创建数据库对象

用postgres用户创建

    CREATE DATABASE test;
    
模式(schema),数据库可以有多个模式,每个模式包含多个表,这样允许你根据特定应用或用户将数据库进一步细分

默认每个数据库都有一个模式,成为public

#### 2. 创建用户账户

PostgreSQL中的用户账户成为登陆角色(Login Role)

* 创建一个跟PostpreSQL登陆角色对应的特殊Linux账户来运行所有的shell脚本
* 为每个需要运行shell脚本来访问数据库的linux用户账户创建PostgreSQL账户

    CREATE ROLE rich login;
    
组角色(group role)


## 23.3 使用数据库

### 23.3.1 创建数据表

关系数据库(relational database)

在关系数据库中,数据由`数据字段` `数据行` `表` 组成

数据字段是单独的一条信息,如员工姓或工资

数据行是相关数据字段的集合,比如员工ID号,姓,名,地址和工资

表含有保存相关数据的所有数据行, 比如一个Employees的表来保存每个员工的数据行

CREATE TABLE; 新建表

    CREATE TABLE employees (
    empid int not null,   #定义非空
    lastname varchar(30),
    firstname varchar(30),
    salary float,
    primary key (empid)); #定义唯一性?
    
创建新表前确保你正在正确的数据库中,还要确保你用管理员用户账户

数据类型

* char     定长字符串值
* Varchar  变长字符串值
* int      整数值
* float    浮点值
* Boolean  布尔类型true/false值
* Date     YYYY-MM-DD格式的日期值
* Time     HH:mm:ss格式的时间值
* Timestamp 日期和时间的组合值
* Text     长字符串值
* BLOB     大的二进制值,比如图片或视频剪辑


empid还指定了一个**数据约束(data constraint)**, 数据约束限制输入的什么类型数据可以创建一个有效的数据行, not null 指明每个数据行都必须有一个唯一的empid值

在PostgreSQL为表一级分配权限

    GRANT SELECT,INSERT,DELETE,UPDATE ON public public.employees TO rich;
    
### 23.3.2 插入和删除数据

    INSERT INTO table VALUES (…)
    
    INSERT INTO employees VALUES (1, ' Blum' , ' Rich', 25000.00)
    
如果插入两条empid相同的数据会报错

删除数据

    DELETE FROM table;
    
小心,这个命令删除表中所有数据

    DELETE FROM employees WHERE empid = 2
    
### 23.3.3 查询数据

    SELECT datafields FROM table
    
datefields参数使用逗号分开的你希望查询的数据字段名称, 如果要提取所有字段,就用星号通配符

    SELECT * FROM employees
    
可以加修饰符来定义数据库服务器如何返回查询要找的数据

* WHERE    显示符合特定条件的数据子集
* ORDER BY 以指定顺序显示数据行
* LIMIT    只显示数据行的一个子集

    SELECT * FROM employees WHERE salary > 40000
    
## 23.4 在脚本中使用数据库


### 23.4.1 连接到数据库

#### 1. 查找客户端程序

`which mysql` 查找mysql安装的位置

    MYSQL=`which mysql`
    
#### 2. 登录到服务器

    MYSQL test -u test -ptest
    #直接输入密码,登陆
    
但是这样任何能够访问脚本的人都能看到数据库的账号和密码

使用`$HOME/.my.cnf文件来读取特殊的启动命令和设置

在这个文件里面设置默认密码,加入

    [client]
    password = test
    #将文件权限换成400
    
### 23.4.2 向服务器发送命令

* 发送单个命令并退出
* 发送多个命令

mysql 的 `-e`参数

    MYSQL=`which mysql`
    $MYSQL test -u test -e 'select * from employees'
    
psql使用-C参数

    PSQL=`which psql`
    $PSQL test -c 'select * from employees"
    
要发送多条SQL命令使用文件重定向(第14章),在shell脚本中重定向行,必须定义一个结束字符串(end of file string),结束字符串指定了重定向数据的开始和结尾

    MYSQL = `which mysql`
    $MYSQL test -u test << EOF
    show tables;
    select * from employees where salary > 40000;
    EOF
    
    PSQL=`which psql`
    $PSQL test << EOF
    \dt;
    select * from employees where salary > 40000;
    EOF
    
### 23.4.3 格式化数据

#### 1. 将输出赋给变量

    dbs=`$MYSQL test -u test -Bse 'show databases'`
    for db in $dbs
    do
      echo $db
    done
    
#### 2. 使用格式化标签

`-H`选项以HTML格式显示结果选项

    psql test -H -C 'select * from  employees where empid = 1'
    
mysql还支持扩展标记语言(XML), 使用`-X`选项


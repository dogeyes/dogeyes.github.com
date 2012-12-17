---
layout: post
title: "linux command line"
description: ""
category: linux
tags: [linux,command]
---
{% include JB/setup %}

# 第一部分linux命令行

## 第一章初始Linux shell

## 1.1 什么是Linux

1. linux内核
2. GNU工具逐渐
3. 图形化桌面环境
4. 应用软件

（擦类，图怎么办？手动画吗？）
***
linux系统图
***


内核的基本功能

1. 系统内存管理
2. 软件程序管理
3. 硬件设备管理
4. 文件系统管理

#### 系统内存管理

`物理内存`, `虚拟内存`

`交换空间(swap space)`, `页面(page)`,`换出(swapping out)`,`换入(swapping in)`

`/proc/meminfo`可以查看系统上虚拟内存当前状态

一般情况下每个进程都有独立的内存页面,但是也可以创建一些共享页面,多个进程可以在同一块公用内存区域进行读取和写入操作.内核负责维护和管理这块公共内存区域并控制每个进程访问共享区域.

`ipcs -m`  可以查看系统上当前的共享内存页面.

#### 软件程序管理

Linux操作系统中的程序是**进程**.

进程-1前台-2后台

内核创建第一个进程`init进程`来启动系统上所有其他进程.

内核启动,先加载init进程.内核启动其他任何进程,都会在虚拟内存中给新的进程分配一块专有的区域来存储进程用到的数据和代码.

有些linux发行版使用一个表来管理系统开机是要自动启动的进程.这个表一般在`/etc/inittab`.

有些在`/etc/init.d`目录中,将开机时启动或停止某个应用的脚本放在这个目录下,这些脚本通过`/etc/rcX.d`目录下的入口启动,这里的`X`代表运行级(run level).

Linux 操作系统的init系统采用了运行级.运行级决定了init进程运行`/etc/inittab`或`/etc/rcX.d`目录中定义好的某些特定类型的进程. Linux有5个运行级.

运行级1-单用户模式,基本系统进程启动,同时启动唯一一个控制台终端进程, 紧急文件系统维护.

运行级3-标准模式,大多数应用软件会启动.

运行级5-会运行X Window系统,允许用户通过图形化桌面窗口登陆系统.

可以通过调整运行级来控制整个系统的功能.通过将运行级从3调整到5,系统从基于控制台的系统变成图形化X Window系统.

`ps ax`命令查看当前Linux上的进程.

*进程号*(Process ID, PID), init的进程号是1,每个进程的进程号都唯一,(S睡眠,SW睡眠和等待,R运行),名字外的方括号表示进程被换出.


#### 硬件设备管理

驱动程序代码(driver code)

两种方法来插入设备驱动代码:

1. 编译进内核的设备驱动代码
2. 可插入内核的设备驱动模块

设备文件

1. 字符型设备文件,每次只能处理一个字符,大多数调制解调器和终端
2. 块设备文件, 每次能处理大块数据的设备,硬盘
3. 网络设备文件, 采用数据包发送和接受数据的设备,网卡和一个特殊的回环设备.回环设备可以使用通用网络编程协议同自己通信.

Linux为系统上的每个设备都创建一种特殊的文件,`节点`.与设备的通信都是通过设备节点完成的.每个节点有一个唯一的数值对,供Linxu内核标记它.数值对包括一个主设备号和一个次设备号.类似的设备被划分在同样的主设备号下.次设备号标识同一主设备号下的特殊设备.

`/dev`下是设备文件.文件的信息的第一列表明了设备类型,`b` 块设备, `c`字符设备.

#### 文件系统管理

<table>
	<tr>
		<td>ext3</td>
		<td>第三扩展文件系统,支持日志功能</td>
	</tr>
	<tr>
		<td>ext4</td>
		<td>第四扩展文件系统,支持高级日志功能</td>
	</tr>
	<tr>
		<td>iso9660</td>
		<td>ISO 9660文件系统(CD-ROM)</td>
	</tr>
	<tr>
		<td>minix</td>
		<td>minix文件系统</td>
	<tr>
	<tr>
		<td>msdos</td>
		<td>微软FAT16文件系统</td>
	</tr>
	<tr>
		<td>ntfs</td>
		<td>支持Microsoft NT文件系统</td>
	</tr>
</table>

Linux内核采用虚拟文件系统(Virtual File System, VFS)作为和每个文件系统交互的接口.这为linux内核同任何类型文件系统通信提供了一个标准接口,当每个文件系统被挂载和使用时,VFS将信息都缓存在内存中.


### 1.1.2 GNU工具链

GNU GNU is not Unix

#### 1. 核心GNU工具链

为Linux系统提供的一组核心工具被称为**coreutils**(core utilities)软件包.

1. 用以处理文件的工具
2. 用以操作文本的工具
3. 用以管理进程的工具

#### 2. shell

shell是个交互工具,为用户提供了启动程序,管理文件系统上的文件以及管理管理运行在Linux系统上进程的途径.

shell的核心是命令行提示符.

shell脚本

<table>
 </tr>
  <td>bash</td>
  <td>标准shell</td>
 </tr>
 <tr>
  <td>ash</td>
  <td>轻量级shell,与bash兼容</td>
 </tr>
 <tr>
  <td>korn</td>
  <td>与Bourne shell兼容,但是支持一些高级编程特性</td>
 </tr>
 <tr>
  <td>tcsh</td>
  <td>将C语言中的一些元素引入到shell中</td>
 </tr>
 <tr>
  <td>zsh</td>
  <td>将bash,tcsh和korn的特性引入同时提供高级编程特性,共享历史文件和主体化提示符的高级shell</td>
 </tr>
</table>

### 1.1.3 Linux 桌面环境

#### 1 X Window系统

X Window软件是图形显示的核心元素, 直接和PC上的显卡以及显示器一起工作的底层软件.

XFree86

X.org

要在X Window系统软件之上建立桌面环境

#### 2 KDE桌面

KDE(K Desktop Environment, K 桌面环境)

1. KDE菜单
2. 程序快捷方式
3. 任务栏
4. 小应用程序

#### 3 GNOME桌面

GNOME(The GNU Network Object Model Environment, GNU网络对象模拟环境)

#### 其他桌面

* fluxbox
* xfce
* JWM
* fvwm
* fvwm95

***

### 1.2 Linux 发行版本

1. 完整的核心Linux发行版.
2. 专业发行版.
3. LiveCD测试发行版.

### 1.2.1 核心Linux发行版

包含*内核*,一个或多个*图像化桌面环境*以及*linux应用*

1. Slackware
2. Red Hat
3. Fedora
4. Gentoo
5. Mandriva
6. openSuSE
7. Debian

### 1.2.2 专业Linux发行版

基于某个主流发行版,但仅包含主流发行版中的一小部分用于某种特定用途的程序.

1. Xandros
2. SimplyMEPIS
3. Ubuntu
4. PCLinusOS
5. Mint
6. dyne:bolic
7. Puppy Linux

### 1.2.3 Linux LiveCD

1. Knoppix
2. SimplyMEPIS
3. PCLinuxOS
4. Ubuntu
5. Slax
6. Puppy Linux

***

# 第2章 走进shell

## 2.1 终端模拟

命令行界面(CLI, Command Line Interface)

哑终端(dumb terminal)

Linux控制台

终端模拟包

哑终端-图形功能和键盘

### 2.1.1 图形功能

#### 1. 字符集

*字符集*是一组二进制命令,Linux可以将他们发给显示器来显示字符.

1. ASCII
2. ISO-8859-1 (Latin-1)
3. ISO-8859-2
4. ISO-8859-6
5. ISO-8859-7
6. ISO-8859-8
7. ISO-10646 (Unicode), 越来越流行,包含上面的

#### 2. 控制码

终端控制显示器和键盘上的特殊功能,比如光标的位置

`回车(光标返回到行首)`, `换行(光标放到下一水平行)`, `水平制表符(光标移动指定数目的空格)`,`方向键(上,下,左,右)`, `翻页符(上翻,下翻)`
还有清空整个屏幕,铃声等

控制码可以用来控制哑终端的通信功能,`XON`,`XOFF` 开启, 停止到终端的数据传输

#### 3. 块模式图形

ANSI字符集包含的代码不但允许显示器显示文本,也允许显示器显示基本的图形符号,比如框,线,块等

#### 4. 矢量图形

#### 5. 显示缓冲

滚动区域(scroll region)

替代屏幕(alternative screen)

#### 6. 色彩

* 加粗字符
* 下划线字符
* 图像反转(黑底白字)
* 闪烁
* 组合

### 2.1.2 键盘

特殊键

* 中断(Break)
* 滚动锁定(Scroll Lock)
* 重复(Repeat)
* 返回(Return)
* 删除(Delete)
* 方向键(Arrow key)
* 功能键(Function Key)

## 2.2 terminfo数据库

终端模拟包可以模拟不同类型的终端,通过一个环境变量和terminfo数据库文件来实现.

terminfo 数据库是一组文件, 表示各种可用在Linux系统上的终端的特性,每种终端类型的terminfo数据作为一个单独的文件存储在terminfo数据库目录`/usr/share/terminfo`,`/etc/terminfo`,`/lib/terminfo`等等

terminfo是二进制文件,是编译文本文件的结果,这个文本文件含有定义了屏幕功能的代码字,以及在终端上实现这个功能的控制码

`infocmp`命令将二进制条目转换成文本, 如`infocmp vt100`

terminfo 功能代码

Linux shell使用`TERM`环境变量来定义对特定会话使用terminfo数据库中的哪个终端模拟设置.如TERM设定为vt100时, shell知道使用vt100 terminfo数据库条目关联的控制码来向终端模拟器发送控制码.

`echo $TERM`

## 2.3 Linux控制台

现代Linux,几个*虚拟控制台*.运行在Linux系统内存总的一个终端会话,大部分会启动7个或者更多,`Ctrl+Alt+Fn`来进入第n个控制台,其中一个或几个为X Window图形化桌面保留

## 2.4 xterm终端

xterm包提供了一个基本的VT102/VT220终端模拟CLI和一个图形化Tektronix 4014环境

### 2.4.1 命令行参数

加号将某个功能恢复为默认设置,减号将功能设为非默认值

***
xterm命令行参数
***

启动xterm时`-help`参数来确定xterm实现了哪些参数

### 2.4.2 xterm主菜单

一般按`Ctrl 鼠标左键`换出

#### 1. X事件命令

xterm如何和X Window 显示交互的功能

1. Toolbar(工具条) 同tb命令行参数
2. Secure Keyboard(安全键盘) --将键盘限定在特定的xterm窗口中
3. Allow SendEvents(允许发送事件) --允许这个xterm窗口接受其他X Window应用产生的X Window事件
4. Redraw Window(重绘窗口)

#### 2. 输出捕捉

xterm包允许捕捉显示在窗口总的数据

1. Log to File(记录到文件)
2. Print Window(打印窗口)
3. Redirect to Printer(重定向到打印机)

#### 3. 键盘设置

1. 8-bit Controls(8位控制)
2. Back Arrow Key(后退方向键)
3. Alt/Numlock Modifiers(Alt/Numlock修饰符)
4. Alt Sends Escape(Alt 发送转义字符)
5. Meta Sends Escape(Meta发送转义字符)
6. Delete is DEL(删除键是DEL功能)
7. Old Function Keys旧功能键
8. Termcap Function Keys(Termcap功能键)
9. Sun Function Keys
10. VT220 Keyboard

设置键盘偏好通常取决于特定的应用和工作环境,还包括相当数量的个人偏好

### 2.4.3 VT选项

一般按`Ctrl 鼠标中键`唤出

***
图2-5错误
***

#### 1.VT功能

#### 2. VT命令

#### 3. 当前屏幕命令

### 2.4.4 VT字体菜单

`Ctrl 鼠标右键`唤出 

#### 1. 设置字体

#### 2. 显示字体

#### 3. 指定字体

## 2.5 Konsole终端

KDE桌面项目创建的终端模拟包,不仅集成了xterm功能还有一些Windows应用中才有的高级功能

### 2.5.1 命令行参数

`konsole parameters` 手动启动konsole

Konsole命令行参数

### 2.5.2 标签式窗口会话

貌似这个比较高级啊.

### 2.5.3 配置文件

Konsole提供了配置文件(Profile)的强大方法来保存和重用标签会话的设置. 第一次启动Konsole时, 标签会话设置会从默认的配置文件Shell中提取, 这些设置包括使用什么Shell, 什么配色方案等等选项,修改了当前标签会话后, 可以将这些修改保存为一个新的配置文件.

配置文件还能用来自动化普通任务,比如登陆到另外一个系统,可以定义许多配置文件并在每个打开的标签会话中使用不同的配置文件.可以用工具栏中的选项来新建配置,更改配置,切换配置.

### 2.5.4 菜单栏

## 2.6 GNOME Terminal

### 2.6.1 命令行参数

### 2.6.2 标签

### 2.6.3 菜单栏










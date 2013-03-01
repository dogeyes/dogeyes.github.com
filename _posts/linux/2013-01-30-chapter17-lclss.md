---
layout: post
title: "图形化桌面上的脚本编程 chapte17 LCLSS"
description: ""
category: LCLSS
tags: [LCLSS, linux, 学习, command line]
---
{% include JB/setup %}

# 第17章 图形化桌面上的脚本编程

## 17.1 创建文本菜单

### 17.1.1 创建菜单布局

`clear` 命令用当前终端会话的terminfo数据来清理 出现在屏幕上的文本,接着用echo来显示菜单元素

`echo`中显示制表符,换行符等,使用`-e`选项

    echo -e "1.\tDisplay disk space"
    
这样对排版来说大有好处

    clear
    echo
    echo -e "\t\t\tSys Admin Menu\n"
    echo -e "\t1. Display disk space"
    echo -e "\t2. Display logged on users"
    echo -e "\t3. Display memory usage"
    echo -e "\t0. Exit menu\n\n"
    echo -en "\t\tEnter option: "
    read -n 1 option
    
### 17.1.2 创建菜单函数

**桩函数(stub function)**,用来占位

    function diskspace {
      clear
      echo "This is where the diskspace commands will go"
    }

将菜单布局本身作为一个函数创建
   
    function menu {
    	clear
    	echo
   		echo -e "\t\t\tSys Admin Menu\n"
    	echo -e "\t1. Display disk space"
    	echo -e "\t2. Display logged on users"
    	echo -e "\t3. Display memory usage"
    	echo -e "\t0. Exit menu\n\n"
    	echo -en "\t\tEnter option: "
    	read -n 1 option
  	}

### 17.1.3 添加菜单逻辑

`case`命令

    menu
    case $option in
    0)
        break ;;
    1)
        diskspace ;;
    2) 
        whoseon ;;
    3)
        memusage ;;
    *)
        clear
        echo "Sorry, wrong selection" ;;
    esac
    
### 17.1.4 整合shell脚本菜单

    function diskspace {
        clear
        df -k
    }
    function whoseon {
        clear
        who
    }
    function memusage {
        clear
        cat /proc/meminfo
    }
    function menu {
    	clear
    	echo
   		echo -e "\t\t\tSys Admin Menu\n"
    	echo -e "\t1. Display disk space"
    	echo -e "\t2. Display logged on users"
    	echo -e "\t3. Display memory usage"
    	echo -e "\t0. Exit menu\n\n"
    	echo -en "\t\tEnter option: "
    	read -n 1 option
  	}
	
	while [ 1 ]
	do
	    menu
    	case $option in
    	0)
        	break ;;
    	1)
        	diskspace ;;
    	2) 
        	whoseon ;;
    	3)
        	memusage ;;
    	*)
        	clear
        	echo "Sorry, wrong selection" ;;
    	esac
    	echo -en "\n\n\t\tHit and key to continue"
    	read -n 1 line 
	done	
	clear
 
### 17.1.5 使用select命令

`select`命令允许从单个命令行创建菜单,然后再提取输入的答案并自动处理

    select variable in list
    do
        commands
    done
    
list参数是构成菜单的空格分割的文本选项列表

select命令会在列表中将每个选项作为一个编好号的选项显示,然后为选项显示一个特殊的由PS3环境变量定义的提示符
    
    function diskspace {
        clear
        df -k
    }
    function whoseon {
        clear
        who
    }
    function memusage {
        clear
        cat /proc/meminfo
    }
    
    PS3="Enter option: "
    select option in "Display disk space" "Display logged on users" "Display memory usage" "Exit program"
    do
        case $option in
        "Exit program")
            break;;
        "Display disk space")
            diskspace;;
        "Display logged on users")
            whoseon;;
        "Display memory usage")
            memusage;;
        *)
            clear
            echo "Sorry, wrong selection";;
        esac
    done
    clear
    
## 17.2 使用窗口

`dialog`包,能将文本环境总用ANSI转义控制字符来创建标准的窗口对话框

### 17.3 dialog包

部件(widget)

____
dialog部件 p361
____

`dialog --widget parameters`

parameters定义了部件的大小以及部件需要的文本

每个dialog部件提供了两种格式的输出

* 使用STDERR
* 使用退出状态码

退出状态码决定了用户选择的按钮,可以用`#?`来查看具体选择了哪个按钮

如果部件返回了任何数据,比如菜单选择, 那么dialog命令会将数据发送到STDERR,可以重定向之

    dialog --inputbox "Enter your age:" 10 20 2>age.txt

#### 1. msgbox部件

    dialog --msgbox text height width
    
`--title` 顶部标题

    dialog --title testing --msgbox "This is a test" 10 20
    
#### 2. yesno部件

    dialog --title "Please answer"  --yesno "Is this thing on?" 10 20
    
    echo $?
    
#### 3.inputbox部件

会将输入发送给STDERR

    dialog --inputbox "Enter your age" 10 20 2>age.txt
    echo $?
    
#### 4. textbox部件

    dialog --textbox /etc/passwd 15 45
    
#### 5. menu部件

    dialog --menu "Sys Admin Menu " 20 30 10 1 "Display disk space" 2 "Display users" 3 "Display memory usage" 4 "Exit" 2> test.txt
    
#### 6. fselect部件

    dialog --title "Select a file" --fselect $HOME/ 10 50 2>file.txt
    
### 17.2.2 dialog选项

___
dialog命令选项
___

### 17.2.3 在脚本中使用dialog命令

* 如果有Cancel或No按钮,检查dialog命令的退出状态码
* 重定向STDERR来获得输出值

一个脚本

    temp=`mktemp -t test.XXXXXX`
    temp=`mktemp -t test2.XXXXXX`
    
    function diskspace {
        df -k > $temp
        dialog --textbox $temp 20 60
    }
    function whoseon {
        who > $temp
        dialog --textbox $temp 20 50
    }
    function memusage {
        cat /proc/meminfo
        dialog --textbox $temp 20 50
    }
    function menu {
    	clear
    	echo
    	echo -e "\t\t\tSys Admin Menu\n"
    	echo -e "\t1. Display disk space"
    	echo -e "\t2. Display logged on users"
    	echo -e "\t3. Display memory usage"
    	echo -e "\t0. Exit menu\n\n"
    	echo -en "\t\tEnter option: "
    	read -n 1 option
  	}
	
	while [ 1 ]
	do
	    dialog --menu "Sys Admin Menu " 20 30 10 1 "Display disk space" 2 "Display users" 3 "Display memory usage" 4 "Exit" 2> temp2
        
        if [ $? -eq 1 ]
        then
            break
        fi
        
        selection=`cat $temp2`
      	case $selection in
    	0)
        	break ;;
    	1)
        	diskspace ;;
    	2) 
        	whoseon ;;
    	3)
        	memusage ;;
    	*)
        	clear
        	dialog --msgbox "Sorry, invalid selection" 10 30 ;;
    	esac
    	done
    	rm -f $temp 2> /dev/null
    	rm -f $temp2 2> /dev/null	

## 17.3 使用图形

kdialog 和 zenity包, 各自为KDE和GNOME桌面提供了图形化窗口部件

### 17.3.1 KDE环境

kdialog包

#### 1. kdialog部件
和dialog包类似

    kdialog display-options window-options arguments
    
`window-options`选项允许指定使用哪种类型的窗口部件

___
kdialog窗口选项
___

    kdialog --checklist "Item I need" 1 "Toothbrush" on 2 "Toothpaste" off 3 "Hair brush" on 4 "Deodorant" off 5 "lippers" off
    
默认发送到STDOUT

#### 2. 使用kdialog

kdialog使用STDOUT输出,而dialog用STDERR输出

使用和dialog包几乎完全一样

### 17.3.2 GNOME环境

* gdialog
* zenity

#### 1. zenity部件

___
zenity窗口部件
___

zenity命令行程序于dialog工作方式有些不同, 许多部件类型都用命令行上的其他选项定义,而不是将它们包括在选项参数里

将结果输出到STDOUT

#### 2. 在脚本总使用zenity

    zenity --text-info --title "Logged in users" --filename=$temp --width 500 --height 500
    


    
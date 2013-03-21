---
layout: post
title: "sublime text & matlab"
description: ""
category: sublime text
tags: [sublime text, 学习]
---
{% include JB/setup %}

# matlab in sublime text

# matlab 命令行运行

`matlab -nosplash -nodesktop -r filename` 可以运行脚本,脚本不带`.m`后缀

`-nosplash` 关闭启动画面

`-nodesktop` 关闭GUI

`-r` 运行脚本

# 在sublime text下搭建matlab开发环境

1. 新建Build System: Tool->Build System -> New Build System; 系统新建JSON文件
2. 编辑JSON文件
    {
        "cmd": ["C:/Program Files/MATLAB/R2009b/bin/matlab", "-nosplash", "-nodesktop", "-r", "$file_base_name"],
        "selector": "source.m"
    }
3. 保存JSON
4. `.m`文件可以build

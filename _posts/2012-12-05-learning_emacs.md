---
layout: post
title: "learning_emacs"
description: ""
category: learning
tags: [emacs, 学习]
---
{% include JB/setup %}

##Emacs
###Hour1 Introduction

####Key

`Shift`,`Control(Ctrl)`,`Meta(Alt)`

* `C-F1` 同时按`Ctrl`和`F1`

* `S-F1` 同时按`Shift`和`F1`

* `M-F1` 同时按`Meta`和`F1`

* `S-C-M-F1` 或 `C-M-S-F1` 按的顺序不重要

####Features

* Working with Many Files in Different Windows at the Same Time

* Editing Files on Different Hosts

* Customizable Keyboards and Functions

* Lots of Additional Third-Party Extensions

* Undo and Recovery

* Editing Modes

* Making the Text More Readable Using Colors

* Spell-Checking

* Search and Search-and-Replace Capabilities

* Compiling and Debugging Programs from Within Emacs

* Powerful Macros

* Folding and Hiding Text

* Reading/Composing Main and News

* Extra Help Using the Info System

####The Keyboard Quick Reference Card

	(global-set-key [(control f6)] 'sams-toggle-truncate)
将`C-F6`与`sams-toggle-truncate`函数绑定

####A Note on Configuring Emacs
通过在`.emacs`文件中插入*Lisp*代码可以来配置Emacs

《Teach Yourself Emacs in 24Hours》一书的配置方式
配置文件 [下载](http://www.cs.virginia.edu/~wh5a/personal/Emacs/ "配置文件")

1. 在`home`下新建文件夹`Emacs`

2. 将`sams-lib.el`拷贝到`Emacs`文件夹

3. 在`.emacs`文件中插入代码
	
<pre><code>(setq load-path (cons "~/Emacs" load-path))</code>
<code>(require 'sams-lib)</code></pre>

也可以加入`refcard.el`文件，并在`.emacs`中加入

	(load "refcard")


###Hour2 Using Emacs in Microsoft Windows

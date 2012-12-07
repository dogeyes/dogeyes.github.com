---
layout: post
title: "markdown tutorial"
description: ""
category: markdown 
tags: [学习, markdown, jekyll]
---
{% include JB/setup %}

##markdown

###兼容HTML

可以在markdown文档中直接添加HTML标签

要制约的是一些HTML区块元素,比如`<div>`,`<table>`,`<pre>`,`<p>`等标签,必须在前后加上空行与其他内容隔开,开始标签和结尾标签不能用制表符或空格来缩进

例如,在markdown文件中加上一段HTML表格:

	markdown文档
	
	<table>
		<tr>
			<td>Foo</td>
			<td>Bar</td>
		</tr>
		<tr>
			<td>Tic</td>
			<td>Puu</td>
		</tr>
	</table>

	markdown文档

效果如下

markdown文档
	
<table>
	<tr>
		<td>Foo</td>
		<td>Bar</td>
	</tr>
	<tr>
		<td>Tic</td>
		<td>Puu</td>
	</tr>
</table>

markdown文档

在HTML区块标签中的markdown格式语法不会被处理,但是markdown区块中的HTML是有效的

HTML的区段(行内)标签如`<span>`,`<cite>`,`<del>`可以在markdown的段落,列表或是标题内随意使用.依照个人习惯,甚至可以不用markdown格式,而直接使用HTML标签来格式化.比如可以使用HTML的`<a>`或者`<img>`标签.

	<del>test del<del>

<del>test del<del>

- - -

###特殊字符的自动转换

在HTML中`<`和`&`要特殊处理. `<`用于起始标签,`&`用于标记HTML实体,如果只是想显示这些字符就要使用`&lt;`和`&amp;`

比如`AT&T`要用`AT&amp;T`,比较复杂的的如连接

`http://images.google.com/images?num=30&q=larry+bird`

必须要转换成

`http://images.google.com/images?num=308&amp;q=larry+bird`

才能放到链接标签`href`属性里,这样是很容易出错的

markdown会自动转换,当`&`是HTML字符实体的一部分时,它会保留原状,否则会被转换成`&amp;`

若要在文档里输入©,则要

	&copy;

markdown保留`&`符号,而

	`AT&T`

markdown会把它转换成:

	`AT&amp;T`

事实上是书写更自然了.

类似的状况会发生在`<`符号上,因为markdown兼容HTML,如果把`<`符号作为HTML标签的界定符,那么markdown不会转换他,但如果是

	4 < 5

markdown会把它转换成

	4 &lt; 5

在code范围内,无论是行内还是区块内,`<`和`&`都会转换成HTML实体,这样在markdown内写HTML的代码尤为方便,因为在HTML代码内写HTML代码,需要把所有的`<`和`&`手动转换为HTML实体.

- - -

###区块元素

####段落和换行

一个markdown段落是由一个或者多个连续的文本行组成的,前后要有一个以上的空行(空行是看起来是空的行,比如只包含空格和制表符,也被视为空行).普通的段落不应该用空格或者制表符来进行缩进(会形成code块的)

markdown允许段落内的强迫换行(插入换行符),如果要以来markdown来插入`<br />标签的话,在插入处先输入两个以上的空格然后回车.

    ab  <return>ab

ab  
ab

而普通的

    ab<return>ab

ab
ab

虽然换行比较麻烦,但是简单的将每个换行都转换成`<br />`在markdown中并不合适,markdown中的email式的区块引用和多段落的列表在使用换行来排版的时候,不但更好用而且方便阅读.

####标题

markdown支持两种标题语法,类Setext和类atx形式

类Setext使用底线的形式,`=`(最高阶标题)和`-`(第二阶标题),任何数量的`=`和`-`都可以,例如

    This is an H1
    =============
    
    This is an H2
    --------------

类Atx形式则是在行首插入1到6个`#`,对应1到6阶

    # 这是H1

    ## 这是H2

    ###### 这是H6

可以闭合类atx标题,只是为了美观

    # 这是H1 #
    
    ## 这是 H2 #
 
    ### 这是 H3 #

####区块的引用

markdown标记区块引用使用类似email中的`>`引用方式,在每行前面加上`>`:

	>这是一个区块引用.
	>
	>这是同一个区块引用.

>这是一个区块引用.
>
>这是同一个区块引用.

可以只在整个段落前面加上`>`,而不必每行前面加`>`

支持嵌套引用,根据层次加上不同数量的`>`:

	>第一层引用
	>
	>>第二层引用
	>>
	>>第二层引用
	>
	>第一层引用

>第一层引用
>
>>第二层引用
>>
>>第二层引用
>
>第一层引用

引用区块内可以使用markdown语法,包括标题,列表,代码区块等

	>## 标题
	>
	>1. 表项1
	>2. 表项2
	>
	>代码例子
	>     return shell_exec("echo $input  | $markdown_script");

>## 标题
>
>1. 表项1
>2. 表项2
>
>代码例子
>     return shell_exec("echo $input  | $markdown_script");

####列表

markdown支持有序列表和无序列表

无序列表使用`*`,`+`或者`-`作为列表标记:

	* Red
	* Green
	* Blue

* Red
* Greed
* Blue

有序列表用数字加英文句点

	1. Bird
	2. McHale
	3. Parish

1. Bird
2. McHale
3. Parish

重要的是有序列表前面的数字不影响实际生成的HTML
	
	1. Bird
	2. McHale
	1. Parish

和前面生成的是一样的

1. Bird
2. McHale
1. Parish

列表标记通常放在最左边,但是其实可以缩进,最多3个空格,项目标记后面则一定要接至少一个空格或制表符(否则错误,显示非列表)

如果列表项目间用空行分开,输出HTML时就会将项目内容用`<p>`标签包起来

列表项目可以包含多个段落,但是每个项目下的段落都必须缩进4个空格或是一个制表符

	1. 列表项目1

	   列表项目1的第二个段落

	2. 列表项目2

1. 列表项目1
    
    列表项目1的第二个段落

2. 列表项目2

列表项目内放进引用`>`要缩进

	1. 含引用的列表项
	
	   >引用

1. 含引用的列表项

   >引用

如果要放代码的话就要缩进*两次*,也就是8个空格或者两个制表符

	* 含有代码的列表

		代码

* 含有代码的列表
	
		代码

列表中可能会有下面的内容,行首出现了`数字.空白`,这是要加反斜杠转义

	1986. What a great season

1. 1986. What a great season

要改成

	1986\. What a great season

1. 1986\. What a great season

####代码区块

代码一般都已经排好版了,因此希望照原来的样子去显现,markdown会用`<pre>`和`<code>`标签把代码区块包起来.

代码区块在首部缩进4个空格或是1个制表符

	普通段落
	
		代码区块

普通段落

	代码区块

markdown会转换成
	
	<p>普通段落</p>

	<pre><code>代码区块
	</pre></code>

每一阶的缩进(4个空格或者是1个制表符),都会被移除,例如:

	code example
	
		tell application "Foo"
		     beep
		end tell

会被转化成

	<p>code example</p>

	<pre><code>tell application "Foo"
	      beep
	end tell
	</code></pre>

显示的时候是代码本身的缩进

代码区块中的`&`,`<`和`>`会被转化成HTML实体,这样插入代码只要在前面加上缩进就可以了

####分隔线

下面的都可以建立分隔线

	* * *
	
	***
	
	******
	
	_ _ _

	------------------

_ _ _ 

###区段元素

####链接

markdown支持两种形式的链接语法: 行内式和参考式.

不管那一种都用[链接文字]来标记

*行内式*:方括号后面接圆括号并插入网址即可,网址后可加"Title"

	这是[链接](http://google.com.cn/ "Google") 到google

会产生

	<p>这是<a href="http://google.com.cn/" title="Google">链接</a> 到google</p>

这是[链接](http://google.com.cn/ "Google")到google

如果是链接到相同的主机资源,可以使用相对路径

	See my [About](/about/) page for details.

*参考式*:在链接文字的括号后面在接一个方括号,第二个方括号内填入用以表示链接的标记,两个方括号之间可以有一个空格:

	这是[链接][id],参考式

接着在文件的任意处把标记的链接内容定义出来(这个貌似在同一个链接多吃使用的时候蛮有用的)

	[id]: http://example.com/ "OptionalTitle Here"

链接内容的定义形式为:

	[方括号]:空格 链接地址 "title"

链接表示符可以有字母,数字,空白,标点,但是不区分大小写

隐式链接标记

	[Google][]

等同于

	[Google](http://google.com)

也可以

	[a b][] 

接着在任意地方定义

	[a b]: http://ab.com/

[a b][]

[a b]: http://ab.com/

####强调

markdown使用`*`和`_`作为标记强调的符号,被`*`或`_`包围的字词会被转成`<em>`标签包围,用两个`*`或者`_`包起来会被转成`<string>`

	*单星*

	_单下划线_

	**双星**

	__双下划线__


*单星*

_单下划线_

**双星**

__双下划线__


如果`*`或`_`两边都有空白的话,会被当成普通的符号

要在文字前后插入`*`或者`_`,可以使用反斜杠转义

	\*非重点\*

\*非重点\*


####代码

段内代码可以用`` ` ``来标记

	使用`printf()`函数

使用`printf()`函数

会产生

	<p>使用<code>printf()</code>函数</p>

代码区段要插入`` ` ``, 要用多个`` ` ``号来开启和结束代码区段

     ``这里有一个反引号(`)``

代码段起始和结束段都可以放入一个空白,这样可以在区段中插入反引号

	代码段为单引号 `` ` ``

	代码段开始结束有单引号 `` `foo` ``

`` ` ``

`` `foo` ``

####图片

markdown使用和链接相似的方法来插入图片,也分为*行内式*和*参考式*:

行内式
	![Alt text](/path/to/img.jpg)

	![Alt text](/path/to/img.jpg "Optional title")

![头像](http://ww4.sinaimg.cn/mw690/726f37bejw1dziwtsc6y7j.jpg)

参考式

	![Alt text][id]

	[id]: url/to/image "Optional title attribute"

但是markdown无法指定图片宽高,如果要设定就得使用`<img>`标签

	<img src="/url/to/image" alt="Optional title" height="50" width="50" />

<img src="http://ww4.sinaimg.cn/mw690/726f37bejw1dziwtsc6y7j.jpg" alt="头像" height="50" width="50" />

- - - 
###其他

####自动链接

markdown支持以较短的自动链接的形式来处理网址和电子邮箱,只要用方括号包起来,markdown会自动把它转换为链接,网址的链接文字和链接地址一样

	<http://example.com/>

会转化成

	<a href="http://example.com/">http://example.com</a>

邮件地址链接也比较类似,只是markdown会先做一个编码转换的过程,把文字字符转换成16进制位码的HTML实体,这种格式可以糊弄一些不好的邮件手机机器人.

	<address@example.com>

会转成

	<p><a href="&#x6d;&#x61;&#x69;&#x6c;&#116;&#x6f;&#58;&#x61;&#100;&#100;&#x72;&#x65;&#115;&#115;&#x40;&#101;&#120;&#x61;&#109;&#x70;&#x6c;&#101;&#x2e;&#99;&#x6f;&#109;">&#x61;&#100;&#x64;&#114;&#x65;&#115;&#115;&#x40;&#x65;&#x78;&#97;&#109;&#112;&#108;&#101;&#46;&#99;&#111;&#x6d;</a></p>

####反斜杠

markdown可以用反斜杠来插入一些在语法中有其他意义的符号

	\ 反斜杠
	
	` 反引号
	
	* 星号
	
	_ 底线

	{} 花括号

	[] 方括号

	() 括弧

	# 井号
	
	+ 加号

	- 减号

	. 英文句点

	! 惊叹号



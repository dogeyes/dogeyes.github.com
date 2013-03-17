---
layout: post
title: "C7 Expressions"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# Chapter 7 Expressions

just about everything is an expression

the if and case statements both return the value of the last expression executed.

{% highlight ruby %}
song_type = if song.mp3_type == MP3::Jazz              if song.written < Date.new(1935, 1, 1)                Song::TradJazz              else                Song::Jazz              end            else              Song::Other            end
{% endhighlight %}

## Operator Expressions

In Ruby, many operators are actually implemented as method calls. 

`a*b+c` is like `(a.*(b)).+©`

you can always redefine basic arithmetic 

{% highlight ruby %}
class Fixnum  alias old_plus +  # alias
  def +(other)    old_plus(other).succ  endend
1 + 2 -> 4
a = 3
a += 4 -> 8
a + a + a -> 26{% endhighlight %}
{% highlight ruby %}
class Song  def [](from_time, to_time)    result = Song.new(self.title + " [extract]",                      self.artist,                      to_time - from_time)    result.set_start_time(from_time)    result 
  endend  # get part of the song{% endhighlight %}
## Miscellaneous Expressions
### Command Expansion
If you enclose a string in **backquotes** (sometimes called backticks), or use the delimited form prefixed by `%x`, it will (by default) be executed as a command by your underlying operating system. 

{% highlight ruby %}
`date`
`ls`.split[34]
%x{echo "Hello there"}
{% endhighlight %}

You can use expression expansion and all the usual escape sequences in the command string.

{% highlight ruby %}
for i in 0..3  status = `dbmanager status id=#{i}` 
  # ...end
{% endhighlight %}

The exit status of the command is available in the global variable `$?`

### Redefining Backquotes

the string in back- quotes would “by default” be executed as a command. In fact, the string is passed to the method called `` Kernel.` `` 

can be overrided

{% highlight ruby %}
alias old_backquote `def `(cmd)  result = old_backquote(cmd)  if $? != 0    fail "Command #{cmd} failed: #$?"
  end  result
end    print `date`    print `data`
{% endhighlight %}

## Assignment


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

{% highlight ruby %}
a = b = 1 + 2 + 3
a -> 6
b -> 6
a = (b = 1 + 2) + 3
a -> 6
b -> 3
File.open(name = gets.chomp)
{% endhighlight %}

1. assigns an object reference to a variable or constant. hardwired into the language.
2. assignment involves having an object attribute or element reference on the left side, implemented by calling methods, can be overrided

{% highlight ruby %}
class Amplifier  def volume=(new_volume)    self.left_channel = self.right_channel = new_volume
  endend

{% endhighlight %}

**notice** writable attributes have a hidden gotcha,Normally, methods within a class can invoke other methods in the same class and its superclasses in functional form (that is, with an implicit receiver of self). However, this doesn’t work with attribute writers.Ruby sees the assignment and decides that the name on the left must be a local variable, not a method call to an attribute writer.

{% highlight ruby %}
class BrokenAmplifier
  attr_accessor :left_channel, :right_channel
  def volume=(vol)
    left_channel = self.right_channel = vol
  end
end

ba = BrokenAmplifier.new
ba.left_channel = ba.right_channel = 99
ba.volume = 5
ba.left_channel -> 99
ba.right_channel -> 5
{% endhighlight %}


In older Ruby versions, the result of the assignment was the value returned by the attribute-setting method. In Ruby 1.8, *the value of the assignment is always the value of the parameter*; the return value of the method is discarded.

{% highlight ruby %}
class Test
  def val=(val)
    @val = val
    return 99
  end
end
t = Test.new
a = t.val = 2
a -> 2
{% endhighlight %}

### Parallel Assignment

{% highlight ruby %}
a,b = b,a # exchange a b

x = 0 -> 0
a,b,c = x, (x += 1), (x += 1) -> [0,1,2]
{% endhighlight %}

* If an assignment contains more lvalues than rvalues, the excess lvalues are set to nil. 
* If a multiple assignment contains more rvalues than lvalues, the extra rvalues are ignored. 
* If a multiple assignment contains more rvalues than lvalues, the extra rvalues are ignored. 

 
{% highlight ruby %}
a = [1,2,3,4]
b , c = a   -> b == 1, c == 2
b, *c = a   -> b == 1, c == [2,3,4]
b, c = 99, a -> b = 99, c = [1,2,3,4]
b, *c = 99, a -> b = 99, c = [[1,2,3,4]]
b, c = 99, *a -> b = 99, a = 1
b, *c = 99, *a -> b = 99, a = [1,2,3,4]
{% endhighlight %}

### Nested Assignments

{% highlight ruby %}
b,(c,d),e = 1,2,3,4   -> b == 1, c == 2, d == nil, e == 3
b,(c,d),e = [1,2,3,4]   -> b == 1, c == 2, d == nil, e == 3
b,(c,d),e = 1,[2,3],4   -> b == 1, c == 2, d == 3, e == 4
b,(c,d),e = 1,[2,3,4],5   -> b == 1, c == 2, d == 3, e == 5
b,(c,*d),e = 1,[2,3,4],5   -> b == 1, c == 2, d == [3,4], e == 5
{% endhighlight %}

### Other Forms of Assignment

`a = a + 2` is the same as `a+=2`

{% highlight ruby %}
class Bowdlerize  def initialize(string)    @value = string.gsub(/[aeiou]/, '*')
  end  def +(other)    Bowdlerize.new(self.to_s + other.to_s)  end  def to_s    @value
  endend
a = Bowdlerize.new("damn ")  -> d*mna += "shame" -> d*mn sh*m*
{% endhighlight %}

there is not `++` and `--`, use `+=` and `-=`

## Conditional Execution

### Boolean Expressions

only `nil` and constant `false` is false. `0` and **zero-length string** is true

### Defined?, And, Or, and Not

`and` and `&&`

**shortcircuit**

`or` and `||`

`not` and `!`

defined? operator returns `nil` if its argument is not defined; otherwise it returns a description of that argument.

If the argument is yield, defined? returns the string “yield” if a code block is associated with the current context.

{% highlight ruby %}
defined? 1        ->  "expression"defined? dummy    ->  nildefined? printf   ->  "method"defined? String   ->  "constant"defined? $_       ->  "global-variable"defined? Math::PI ->  "constant"defined? a = 1    ->  "assignment"defined? 42.abs   ->   "method"
{% endhighlight %}

* ==    Test for equal value
* ===   Used to compare the each of the items with the target in the when clause of a case statement.
* <=>  General comparison operator. Returns −1, 0, or +1, depending on whether its receiver is less than, equal to, or greater than its argument.
* =~  Regular expression pattern match.
* eql?  True if the receiver and argument have both the same type and equal values. 1 == 1.0 returns true, but 1.eql?(1.0) is false.
* equal?  True if the receiver and argument have the same object ID.
* <,<=,>=,>  Comparison operators for less than, less than or equal, greater than or equal, and greater than.

`!=` and `!~` if equivalent to `!( == )` and `!( ~= )`

You can use a Ruby **range** as a boolean expression. A range such as exp1..exp2 will evaluate as false until exp1 becomes true. The range will then evaluate as true until exp2 becomes true.

### The Value of Logical Expressions

The operators and, or, && and || actually return the first of their arguments that determine the truth or falsity of the condition.

{% highlight ruby %}
nil and true -> nil
false and true -> false
99 and false  -> false
99 and nil   -> nil
99 and "cat" -> "cat"

false or nil  -> nil
nil or false -> false
99 or false  -> 99
{% endhighlight %}

{% highlight ruby %}
words[key] ||= []
words[key] << word

 # is the same as
words[key] = words[key] || []
 # if words[key] is nil, words[key] become an array
 # else nothing change
 
(words[key] ||= []) << word
{% endhighlight %}

## If and Unless Expressions





---
layout: post
title: "C6 More about Methods"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# Chapter 6 More about Methods

## Defining a Method

* Methods that act as queries are often named with a trailing `?`, such as instance_of?
* Methods that are “dangerous”, or modify the receiver, may be named with a trailing `!`
* methods that can be assigned to end with an equals sign (=).

?, !, and = are the only “weird” characters allowed as method name suffixes.

{% highlight ruby %}
def my_new_method(arg1, arg2, arg3)  # Code for the method would go hereend
def my_other_new_method  # Code for the method would go hereend
{% endhighlight %}

{% highlight ruby %}
def cool_dude(arg1="Miles", arg2="Coltrane", arg3="Roach")
  "#{arg1}, #{arg2}, #{arg3}."end
cool_dudecool_dude("Bart")cool_dude("Bart", "Elwood") cool_dude("Bart", "Elwood", "Linus")
{% endhighlight %}

If you define a method inside another method, the inner method gets defined when the outer method executes.

The return value of a method is the value of the last expression executed or the result of an explicit return expression.

### Variable-Length Argument Lists

{% highlight ruby %}
def varargs(arg1, *rest)  # *￼  "Got #{arg1} and #{rest.join(', ')}"end
varargs("one")
varargs("one","two")
varargs("one","two","three")
{% endhighlight %}

### Methods and Blocks

{% highlight ruby %}
def take_block(p1)  if block_given?    yield(p1)  else    p1
  endend
take_block("no block") -> "no block"
take_block("no block") {|s| s.sub(/no /, '')} -> ""block""
{% endhighlight %}


if the last parameter in a method definition is prefixed with an **ampersand**, any associated block is converted to a `Proc` object, and that object is assigned to the parameter.

{% highlight ruby %}
class TaxCalculator
  def initialize(name, &block)
    @name, @block = name, block
  end
  def get_tax(amount)
    "#{@name} on #{amount} = #{@block.call(amount) }"
  end
end

tc = TaxCalculator.new("Sales tax") {|amt| amt * 0.075}
tc.get_tax(100)
tc.get_tax(250)
{% endhighlight %}

### Calling a Method

{% highlight ruby %}
connection.download_MP3("jitterbug") {|p| show_progress(p) }

File.size("testfile")
Math.sin(Math::PI/4)
{% endhighlight %}

If you omit the receiver, it defaults to self, the current object.

{% highlight ruby %}
self.class → Object
self.frozen? → false
frozen? → false
self.id → 967900
id → 967900
{% endhighlight %}

If no ambiguity exists, you can omit the parentheses around the argument list when calling a method.

{% highlight ruby %}
a = obj.hash    # Same asa = obj.hash()  # this.obj.some_method "Arg1", arg2, arg3 # Same thing as obj.some_method("Arg1", arg2, arg3) # with parentheses.
{% endhighlight %}

### Method Return Values

Every called method returns a value.

the value of the last statement

return statement

### Expanding Arrays in Method Calls

When you call a method, you can explode an array,  so that each of its members is taken as a separate parameter. 

**asterisk**

{% highlight ruby %}
 def five(a, b, c, d, e)  "I was passed #{a} #{b} #{c} #{d} #{e}"end
five(1,2,3,4,5)   -> "I was passed 1 2 3 4 5"
five(1,2,3, *['a','b']) -> "I was passed 1 2 3 a b"
five(*[10..14].to_a) -> "I was passed 10 11 12 13 14"
{% endhighlight %}

### Making Blocks More Dynamic

{% highlight ruby %}
list_bones("aardvark") do |bone|
  # ...end
{% endhighlight %}


{% highlight ruby %}
print "(t)imes or (p)lus: "times = getsprint "number: "number = Integer(gets)if times =~ /^t/  puts((1..10).collect {|n| n * number }.join(", "))else  puts((1..10).collect {|n| n+number }.join(", "))end
{% endhighlight %}

{% highlight ruby %}
print "(t)imes or (p)lus: "times = getsprint "number: "number = Integer(gets)if times =~ /^t/  calc = lambda {|n| n*number }else  calc = lambda {|n| n+number }endputs((1..10).collect(&calc).join(", "))  # &
{% endhighlight %}

### Collecting Hash Arguments

{% highlight ruby %}
class SongList  def create_search(name, params)    # ... 
  endendlist.create_search("short jazz songs",                   {                    'genre' => "jazz",
					 'duration_less_than' => 270
					 })
{% endhighlight %}

the same as

{% highlight ruby %}
list.create_search('short jazz songs',                   'genre' => 'jazz',                   'duration_less_than' => 270)
list.create_search('short jazz songs',                   :genre => :jazz,                   :duration_less_than => 270)                  
{% endhighlight %}


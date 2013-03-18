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

{% highlight ruby%}
if song.artist == "Gillespie" then
  handle = "Dizzy"
elsif song.artist == "Parker" then  # elsif
  handle = "Bird"
else
  handle = "unknown"
end
{% endhighlight %}

{% highlight ruby%}
if song.artist == "Gillespie"
  handle = "Dizzy"
elsif song.artist == "Parker"
  handle = "Bird"
else
  handle = "unknown"
end
{% endhighlight %}

{% highlight ruby%}
if song.artist == "Gillespie" then  handle = "Dizzy"
elsif song.artist == "Parker" then  handle = "Bird"
else  handle = "unknown"
end
{% endhighlight %}

{% highlight ruby%}
if song.artist == "Gillespie" :  handle = "Dizzy"
elsif song.artist == "Parker" :  handle = "Bird"
else  handle = "unknown"
end
{% endhighlight %}

if is anexpression,nota statement—itreturnsa value.

{% highlight ruby %}
handle = if song.artist == "Gillespie" then
           "Dizzy"
         elsif song.artist == "Parker" then
           "Bird"
         else
           "unknown"
         end
{% endhighlight %}

Ruby also has a negated form of the if statement.

{% highlight ruby %}
unless song.duration > 180
  cost = 0.25
else
  cost = 0.35
end
{% endhighlight %}

{% highlight ruby %}
cost = song.duration > 180 ? 0.35 : 0.25
{% endhighlight %}

### If and Unless Modifiers

{% highlight ruby %}
mon, day, year = $1, $2, $3 if date =~ /(\d\d)-(\d\d)-(\d\d)/
puts "a = #{a}" if debug
print total unless total.zero?
{% endhighlight %}

For an if modifier, the preceding expression will be evaluated only if the condition is
true. unless works the other way around.

{% highlight ruby %}
File.foreach("/etc/fstab") do |line|
  next if line =~ /^#/              # Skip comments
  parse(line) unless line =~ /^$/   # Don't parse empty lines
end
{% endhighlight %}

## Case Expressions

{% highlight ruby %}
leap = case
       when year % 400 == 0 then true
       when year % 100 == 0 then false
       else year % 4 == 0
       end
# ruby 1.9 not support :?
{% endhighlight %}

{% highlight ruby %}
leap = case
       when year % 400 == 0: true
       when year % 100 == 0: false
       else year % 4 == 0
       end
# ruby 1.9 not support :?
{% endhighlight %}

{% highlight ruby %}
case input_line
when "debug"
  dump_debug_info
  dump_symbols
when /p\s+(\w+)/
  dump_variable($1)
when "quit", "exit"
  exit
else
  print "Illegal command: #{input_line}"
end
{% endhighlight %}

{% highlight ruby %}
kind = case year
       when 1850..1889 then "Blues"
       when 1890..1909 then "Ragtime"
       when 1910..1929 then "New Orleans Jazz"
       when 1930..1939 then "Swing"
       when 1940..1950 then "Bebop"
       else "Jazz"
       end
{% endhighlight %}

{% highlight ruby %}
kind = case year
       when 1850..1889: "Blues"
       when 1890..1909: "Ragtime"
       when 1910..1929: "New Orleans Jazz"
       when 1930..1939: "Swing"
       when 1940..1950: "Bebop"
       else
       "Jazz"
       end
{% endhighlight %}

This test is done using `comparison === target`.

regular expressions define === as a simple pattern match

Ruby classes are instances of class Class, which defines === to test if the argument is an instance of the class or one of its superclasses.

{% highlight ruby %}
case shape
when Square, Rectangle
  # ...
when Circle
  # ...
when Triangle
  # ...
else
  # ...
end
{% endhighlight %}

## Loops

{% highlight ruby %}
while line = gets
  # ...
end
{% endhighlight %}

{% highlight ruby %}
until play_list.duration > 60
  play_list.add(song_list.pop)
end
{% endhighlight %}

{% highlight ruby %}
a = 1
a *= 2
while a < 100
a -= 10 until a < 100
a 98
{% endhighlight %}

{%  highlight ruby %}
file = File.open("ordinal")
while line = file.gets
  puts(line) if line =~ /third/ .. line =~ /fifth/
end
{% endhighlight %}

` $.` contains the current input line number

{% highlight ruby %}
File.foreach("ordinal") do |line|
  if (($. == 1) || line =~ /eig/) .. (($. == 3) || line =~ /nin/)
    print line
  end
end
{% endhighlight %}



{% highlight ruby %}
print "Hello\n" while false # print nothing
#execute at least one time
begin
  print "Goodbye\n"
end while false
{% endhighlight %}

## Iterators

{% highlight ruby %}
3.times do
  print "Ho! "
end
{% endhighlight %}

{% highlight ruby %}
0.upto(9) do |x|
  print x, " "
end

0.step(12, 3) {|x| print x, " " }

[ 1, 1, 2, 3, 5 ].each {|val| print val, " " }
{% endhighlight %}

And once a class supports each, the additional methods in the Enumerable module become available.

{% highlight ruby %}
File.open("ordinal").grep(/d$/) do |line|
  puts line
end
{% endhighlight %}

{% highlight ruby %}
loop do
  # block
end
# loop forerver
{% endhighlight %}

### For.. In

{% highlight ruby %}
for song in songlist
  song.play
end

# the same as

songlist.each do |song|
  song.play
end

#The only difference between the for loop and the each form is the scope of local variables that are defined in the body.
{% endhighlight %}

{% highlight ruby %}
for i in ['fee', 'fi', 'fo', 'fum']
  print i, " "
end
for i in 1..3
  print i, " "
end
for i in File.open("ordinal").find_all {|line| line =~ /d$/}
  print i.chomp, " "
end
{% endhighlight %}

As long as your class defines a sensible each method,you can use a for loop to traverse its objects.

{% highlight ruby %}
class Periods
  def each
    yield "Classical"
    yield "Jazz"
    yield "Rock"
  end
end
periods = Periods.new
for genre in periods
  print genre, " "
end
{% endhighlight %}

### Break, Redo, and Next

**break** terminates the immediately enclosing loop

**redo** repeats the loop from the start, but without reevaluating the conditionor fetchingthe next element

**next** skips to the endof the loop, effectively starting the next iteration.

{% highlight ruby %}
while line = gets
  next if line =~ /^\s*#/ # skip comments
  break if line =~ /ÊND/ # stop at end
  # substitute stuff in backticks and try again
  redo if line.gsub!(/`(.*?)`/) { eval($1) }
  # process line ...
end
{% endhighlight %}

{% highlight ruby %}
i=0
loop do
  i += 1
  next if i < 3
  print i
  break if i > 4
end
{% endhighlight %}

As of Ruby 1.8, break and next can be given arguments. When used in conventional loops, it probably makes sense only to do this with break (as any value given to next is effectively lost). If a conventional loop doesn’t execute a break, its value is nil.

### Retry

`retry` restarts any kind of iterator loop.

{% highlight ruby %}
for i in 1..100
  print "Now at #{i}. Restart? "
  retry if gets =~ /^y/i
end
# this is not supported after ruby 1.9
# retry can be used only in "rescue"clauses
{% endhighlight %}

{% highlight ruby %}
def do_until(cond)
  break if cond
  yield
  retry
end
i = 0
do_until(i > 10) do
  print i, " "
  i += 1
end
# not supported either
{% endhighlight %}

## Variable Scope, Loops, and Blocks

The while, until, and for loops are built into the language and do not introduce new scope; previously existing locals can be used in the loop, and any new locals created will be available afterward.

**notice**

The blocks used by iterators (such as loop and each) are a little different. Normally, the local variables created in these blocks are not accessible outside the block.

However, if at the time the block executes a local variable already exists with the same name as that of a variable in the block, the existing local variable will be used in the block. Its value will therefore be available after the block finishes.

{% highlight ruby %}
[1,2,3].each do |x|
  y = x + 1
end
[x, y]

#x is not defined error

x = nil
y = nil

[1,2,3].each do |x|
  y = x + 1
end
[x, y]   -> [3,4]
# in fact, things changes in ruby 1.9. it looks like x is still nil,but y is 4, it's quite strange, |x| may define a new variable
{% endhighlight %}

{% highlight ruby %}
if false
  a = 1  #even it is not excuted, things differ
end
3.times {|i| a = i}

a -> 2
{% endhighlight %}

Keep your methods and blocks short. The fewer variables, the smaller the chance that they’ll clobber each other. It’s also easier to eyeball the code and check that you don’t have conflicting names.

Use different naming schemes for local variables and block parameters.For example, you probably don’t want a local variable called “i,” but that might be perfectly acceptable as a block parameter.


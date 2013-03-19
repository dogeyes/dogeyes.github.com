---
layout: post
title: "C10 Basic Input and Output"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# Chapter 10 Basic Input and Output

## What Is an IO Object?

Ruby defines a single base class, `IO`, to handle input and output.

`File`, `BasicSocket` are the subclass of IO

An IO object is a bidirectionalchannel between a Ruby program and some external resource. 

### Opening and Closing Files

{% highlight ruby %}
file = File.new("testfile", "r")
# ... process the file
file.close
# r, w, rw
{% endhighlight %}

`File.open`

{% highlight ruby %}
File.open("testfile", "r") do |file|
  # ... process the file
end  #with a block, file will be closed out the block
{% endhighlight %}

if an exception is raised while processing the file, file.close may not happen,but with File.open and a block, file will be closed auto as if the File.open is like this

{% highlight ruby %}
class File
  def File.open(*args)
    result = f = File.new(*args)
    if block_given?
      begin
        result = yield f
      ensure
        f.close
      end
    end
    return result
  end
end
{% endhighlight %}

### Reading and Writing Files

{% highlight ruby %}
# copy.rb
while line = gets
  puts line
end

ruby copy.rb  # read from stdin
ruby copy.rb testfile # read from testfile
{% endhighlight %}

{% highlight ruby %}
File.open("testfile") do |file|
  while line = file.gets
    puts line
  end
end
{% endhighlight %}

### Iterators for Reading

` IO#each_byte` invokes a block with the next 8-bit byte from the IO object

{%  highlight ruby %}
File.open("testfile") do |file|
  file.each_byte {|ch| putc ch; print "." }
end
{% endhighlight %}

`IO#each_line` calls the block with each line from the file.

{% highlight ruby %}
File.open("testfile") do |file|
  file.each_line {|line| puts "Got #{line.dump}" }
end

File.open("testfile") do |file|
  file.each_line("e") {|line| puts "Got #{ line.dump }" }
end
#change the separator to "e"
{% endhighlight %}

`IO.foreach`  This method takes the name of an I/O source, opens it for reading, calls the iterator once for every line in the file, and then closes the file automatically.

{% highlight ruby %}
IO.foreach("testfile") {|line| puts line }
{% endhighlight %}

{% highlight ruby %}
str = IO.read("testfile") # read the entire file in a string
str.length -> 66
str[0, 30] -> "This is line one\nThis is line "
{% endhighlight %}

{% highlight ruby %}
arr = IO.readlines("testfile") # read the file into a array
arr.length -> 4
arr[0] -> "This is line one\n"
{% endhighlight %}

### Writing to Files

` IO#print` `IO#sysread`, `IO#syswrite`

`Array#pack`

{% highlight ruby %}
str1 = "\001\002\003" -> "\001\002\003"
str2 = "" 
str2 << 1 << 2 << 3 -> "\001\002\003"
[ 1, 2, 3 ].pack("c*") -> "\001\002\003"
{% endhighlight %}

### But I Miss My C++ iostream

you can append an object to an output IO stream.

{% highlight ruby %}
endl = "\n"
STDOUT << 99 << " red balloons" << endl
{% endhighlight %}

### Doing I/O with Strings

` StringIO`

{% highlight ruby %}
require "stringio"
ip = StringIO.new("now is\nthe time\nto learn\nRuby!")
op = StringIO.new("", "w")

ip.each_line do |line|
  op.puts line.reverse
end
op.string 
{% endhighlight %}

### Talking to Networks

`socket` `TCP` `UDP` `SOCKS`

{% highlight ruby %}
require "socket"
client = TCPSocket.open('127.0.0.1','finger')
client.send("mysql\n", 0)
puts client.readlines
client.close
{% endhighlight %}

`lib/net` -> `FTP` `HTTP` `POP` `SMTP` `telnet`

{% highlight ruby %}
require 'net/http'
h = Net::HTTP.new('www.pragmaticprogrammer.com', 80)
response = h.get('/index.html', nil)
if response.message == "OK"
  puts response.body.scan(/<img src="(.*?)"/m).uniq
end
{% endhighlight %}

`open-uri`

{% highlight ruby %}
requrie 'open-uri'
open('http://www.pragmaticprogrammer.com') do |f|
  puts f.read.scan(/<img src="(.*?)"/m).uniq
end
{% endhighlight %}
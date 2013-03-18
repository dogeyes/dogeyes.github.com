---
layout: post
title: "C8 Exceptions Catch and Throw"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# Chapter 8 Exceptions, Catch, and Throw

## The Exception Class

If you create your own, you may want to make it a subclass of `StandardError` or one of its children.

## Handling Exceptions

{% highlight ruby %}
#without exception
op_file = File.open(openfile_name, "w")
while data = socket.read(512)
  op_file.write(data)
end

#with exception
op_file = File.open(opfile_name, "w")
begin
  # Exceptions raised by this code will
  # be caught by the following rescue clasuse
  while data = socket.read(512)
    op_file.write(data)
  end
rescue SystemCallError # trap SystemCallErroe
  $stderr.print "IO failed: " + $!  #$! store the exception object
  op_file.close
  File.delete(opfile_name)
  raise  #raise exception ($!)
end
{% endhighlight %}

{% highlight ruby %}
begin 
  #block
rescue Exception
  #block
end
{% endhighlight %}

{% highlight ruby %}
begin
  eval string
rescue SyntaxError, NameError => boom # store exception in boom
  print "String doesn't compile: " + boom
rescue StandError => bang
  print "Error running script: " + bang
end
{% endhighlight %}

the processing is pretty similar to that used by the case statement.

`parameter===$!`

If you write a rescue clause with no parameter list, the parameter defaults to `StandardError`.

## System Errors

System errors are raised when a call to the operating system returns an error code.

On POSIX systems, these errors have names such as EAGAIN and EPERM.

`man errno`

Ruby takes these errors and wraps them each in a specific exception object. Each is a subclass of SystemCallError, and each is defined in a module called Errno.

you’ll find exceptions with class names such as Errno::EAGAIN, Errno::EIO, and Errno::EPERM.Errno exception objects each have a class constant called Errno that contains the system error code.

### Tidying Up

`ensure` goes after the last rescue clause and contains
a chunk of code that will always be executed as the block terminates.

{% highlight ruby %}
f = File.open("testfile")
begin 
  # .. process
rescue
  # .. handle error
ensure
  f.close unless f.nil? #it while be execulted whatever
end
{% endhighlight %}

`else` goes after the rescue clauses and before any ensure.

`else` clause is executed only if no exceptions are raised by the main body of code.

{% highlight ruby %}
f = File.open("testfile")
begin
  # .. process
rescue
  # .. handle error
else
  puts "Congratulations-- no errors!"
ensure
  f.close unless f.nil?
end
{% endhighlight %}

### Play It Again

`retry`

{% highlight ruby %}
@esmtp = true
begin
  # First try an extended login. If it fails because the
  # server doesn't support it, fall back to a normal login
  if @esmtp then
    @command.ehlo(helodom)
  else
    @command.helo(helodom)
  end
  rescue ProtocolError
    if @esmtp then
      @esmtp = false
      retry
    else
      raise
    end
end
{% endhighlight %}

## Raising Exceptions

`Kernel.raise`

{% highlight ruby %}
raise   #reraises the current exception (or RuntimeError)raise "bad mp3 encoding" # RuntimeError, setting its message to the given stringraise InterfaceException, "Keyboard failure", caller 
 #he first argument to create an exception and then sets the associated message to the second argument and the stack trace to the third argument. 
{% endhighlight %}

{% highlight ruby %}
raiseraise "Missing name" if name.nil?if i >= names.size  raise IndexError, "#{i} >= size (#{names.size})"endraise ArgumentError, "Name too big", callerraise ArgumentError, "Name too big", caller[1..-1]
{% endhighlight %}

### Adding Information to Exceptions

{% highlight ruby %}
class RetryException < RuntimeError  attr :ok_to_retry  def initialize(ok_to_retry)    @ok_to_retry = ok_to_retry  endend
def read_data(socket)  data = socket.read(512)  if data.nil?    raise RetryException.new(true), "transient read error"
  end  # .. normal processingend
begin  stuff = read_data(socket)    # .. process stuff  rescue RetryException => detail
    retry if detail.ok_to_retry
  raiseend
{% endhighlight %}

## Catch and Throw

While the exception mechanism of raise and rescue is great for abandoning execution when things go wrong, it’s sometimes nice to be able to jump out of some deeply nested construct during normal processing. This is where `catch` and `throw` come in handy.

{% highlight ruby %}
catch (:done)  do  while line = gets    throw :done unless fields = line.split(/\t/)    songlist.add(Song.new(*fields))  end  songlist.playend
{% endhighlight %}

catch defines a block that is labeled with the given name,The block is executed normally until a throw is encountered. When Ruby encounters a throw, it zips back up the call stack looking for a catch block with a matching symbol. When it finds it, Ruby unwinds the stack to that point and terminates the block.

{% highlight ruby %}
def prompt_and_get(prompt)  print prompt  res = readline.chomp  throw :quit_requested if res == "!"
  resendcatch :quit_requested do  name = prompt_and_get("Name: ")  age = prompt_and_get("Age: ")
  sex = prompt_and_get("Sex: ")
  # ..  # process informationend
{% endhighlight %}

the throw does not have to appear within the static scope of the catch.
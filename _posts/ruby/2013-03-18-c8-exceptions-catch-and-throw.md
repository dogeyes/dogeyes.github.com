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


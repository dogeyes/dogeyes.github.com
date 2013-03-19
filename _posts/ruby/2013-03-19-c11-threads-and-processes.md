---
layout: post
title: "C11 Threads and Processes"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# Chapter 11 Threads and Processes

## Multithreading

Ruby threads are implemented within the Ruby interpreter, they don’t rely on the operating system. At the same time, you don’t get certain benefits from having native threads. 

### Creating Ruby Threads

{% highlight ruby %}
require 'net/http'pages = %w( www.rubycentral.com slashdot.org www.google.com )
threads = []for page_to_fetch in pages  threads << Thread.new(page_to_fetch) do |url| # notice    h = Net::HTTP.new(url, 80)    puts "Fetching: #{url}"    resp = h.get('/', nil )    puts "Got #{url}: #{resp.message}"  end
endthreads.each {|thr| thr.join }
{% endhighlight %}

### Manipulating Threads

`Thread#value`

`Thread.current` the current thread

`Thread.list` a list of all threads

`Thread#status`

`Thread#alive?`

`Thread#priority=`

### Thread Variables

A thread can normally access any variables that are in scope when the thread is created. Variables local to the block containing the thread code are local to the thread and are not shared.

But what if you need per-thread variables that can be accessed by other threads— including the main thread? Class Thread features a special facility that allows thread- local variables to be created and accessed by name. 

You simply treat the thread object as if it were a Hash, writing to elements using [ ]= and reading them back using [ ]. 

{% highlight ruby %}
count = 0threads = []10.times do |i|  threads[i] = Thread.new
  do sleep(rand(0.1))
    Thread.current["mycount"] = count count += 1  end
endthreads.each {|t| t.join; print t["mycount"], ", " } puts "count = #{count}"
{% endhighlight %}

## Threads and Exceptions

`abort_on_exception flag`  `debug flag`

If abort_on_exception is false and the debug flag is not enabled (the default condition), an unhandled exception simply kills the current thread

{% highlight ruby %}
threads = []4.times do |number|  threads << Thread.new(number) do |i|
    raise "Boom!" if i == 2    print "#{i}\n"  end
endthreads.each {|t| t.join }
{% endhighlight %}

{% highlight ruby %}
threads = []4.times do |number|  threads << Thread.new(number) do |i|
    raise "Boom!" if i == 2    print "#{i}\n"  end
endthreads.each do |t|  begin    t.join  rescue  RuntimeError => e    puts "Failed: #{e.message}"  endend
{% endhighlight %}

However, set abort_on_exception to true, or use -d to turn on the debug flag, and an unhandled exception kills all running threads.

{% highlight ruby %}
Thread.abort_on_exception = true
threads = []4.times do |number|  threads << Thread.new(number) do |i|
    raise "Boom!" if i == 2    print "#{i}\n"  end
endthreads.each {|t| t.join }
{% endhighlight %}

## Controlling the Thread Scheduler

building timing dependencies into a multithreaded application is generally considered to be bad form

`Thread.stop` stops the current thread

`Thread#run` arranges for a par- ticular thread to be run

`Thread.pass` deschedules the current thread, allowing others to run

`Thread#join` and `Thread#value` suspend the calling thread until a given thread finishes.

{% highlight ruby %}
class Chaser  attr_reader :count  def initialize(name)    @name = name    @count = 0 end    def chase(other)      while @count < 5        while @count - other.count > 1          Thread.pass        end        @count += 1        print "#@name: #{count}\n"      end
    endendc1 = Chaser.new("A")c2 = Chaser.new("B")threads = [  Thread.new { Thread.stop; c1.chase(c2) }, 
  Thread.new { Thread.stop; c2.chase(c1) }]
start_index = rand(2)threads[start_index].runthreads[1 - start_index].runthreads.each {|t| t.join }
{% endhighlight %}

`race conditions`

## Mutual Exclusion

`Thread.critical=`, the scheduler will not schedule any existing thread to run.

`Mutex_m`

`Sync`

`Queue`

### Monitors

{% highlight ruby %}
class Counter  attr_reader :count  def initialize    @count = 0    super
    end    def tick      @count += 1    end
endc = Counter.newt1 = Thread.new { 10000.times { c.tick } }
t2 = Thread.new { 10000.times { c.tick } }t1.joint2.joinc.count # may not be 20000
{% endhighlight %}

`@count += 1` may be break

{% highlight ruby %}
require 'monitor'class Counter < Monitor # subclass of Monitor  attr_reader :count  def initialize    @count = 0    super
  end  def tick    synchronize do # synchronize      @count += 1    end  end
endc = Counter.newt1 = Thread.new { 10000.times { c.tick } }
t2 = Thread.new { 10000.times { c.tick } }t1.join;
t2.join
c.count -> 20000
{% endhighlight %}

Only one thread can be executing code within a synchronize block for a particular monitor object at any one time

{% highlight ruby %}
require 'monitor'class Counter  include MonitorMixin
  ...end
{% endhighlight %}

external monitor

{% highlight ruby %}
class Counter  attr_reader :count  def initialize    @count = 0
  end  def tick    @count += 1  end
endc = Counter.newlock = Monitor.newt1 = Thread.new { 10000.times { lock.synchronize { c.tick } } }
t2 = Thread.new { 10000.times { lock.synchronize { c.tick } } }
t1.join; t2.joinc.count -> 20000
{% endhighlight %}

{% highlight ruby %}
require 'monitor'class Counter  # as before...endc = Counter.newc.extend(MonitorMixin) # this is interestingt1 = Thread.new { 10000.times { c.synchronize { c.tick } } }
t2 = Thread.new { 10000.times { c.synchronize { c.tick } } }t1.join; t2.join
c.count -> 20000
{% endhighlight %}

if some other code calls tick but doesn’t realize that synchronization is required, we’re back in the same mess we started with.

### Queues

The Queue class, located in the thread library, implements a thread-safe queuing mechanism. Multiple threads can add and remove objects from the queue, and each addition and removal is guaranteed to be atomic.

### Condition Variables

{% highlight ruby %}
require 'monitor'  playlist = []  playlist.extend(MonitorMixin)  # Player thread  Thread.new do    record = nil    loop do      playlist.synchronize do # here is a bug      sleep 0.1 while playlist.empty?
      record = playlist.shift     end     play(record)   endend # Customer request thread thread 
Thread.new do  loop do    req = get_customer_request
    playlist.synchronize do      playlist << req    end  end
end
{% endhighlight %}

`condition variables`

A condition variable is a controlled way of communicating an event (or a condition) between two threads.

One thread can wait on the condition, and the other can signal it. 

{% highlight ruby %}
require 'monitor'SONGS = [    'Blue Suede Shoes',    'Take Five',    'Bye Bye Love',    'Rock Around The Clock',    'Ruby Tuesday'  ]
START_TIME = Time.nowdef timestamp  (Time.now - START_TIME).to_iend # Wait for up to two minutes between customer requestsdef get_customer_request  sleep(120 * rand)  song = SONGS.shift  puts "#{timestamp}: Requesting #{song}" if song
  songend # Songs take between two and three minutesdef play(song)  puts "#{timestamp}: Playing #{song}"
  sleep(120 + 60*rand)endok_to_shutdown = false  # and here's our original codeplaylist = []playlist.extend(MonitorMixin)
plays_pending = playlist.new_cond  # condition variable # Customer request thread
customer = Thread.new do  loop do    req = get_customer_request
    break unless req 
    playlist.synchronize do      playlist << req      plays_pending.signal    end  end
end # Player threadplayer = Thread.new do  loop do     song = nil playlist.synchronize do      break if ok_to_shutdown && playlist.empty?
      plays_pending.wait_while { playlist.empty? }
      song = playlist.shift     end     break unless song     play(song)    end
endcustomer.joinok_to_shutdown = trueplayer.join
{% endhighlight %}

## Running Multiple Processes

### Spawning New Processes

{% highlight ruby %}
system("tar xzf test.tgz") -> trueresult = `date`result -> Thu Aug 26 22:36:55 CDT 2004\n"
{% endhighlight %}

`Kernel.system` executes the given command in a subprocess; it returns true if the command was found and executed properly and false otherwise. In case of failure, you’ll find the subprocess’s exit code in the global variable `$?`.

`IO.popen` make sending subprocess data and possibly getting some back possible

Write to the IO object, and the subprocess can read it on standard input. Whatever the subprocess writes is available in the Ruby program by reading from the IO object.

{% highlight ruby %}
pig = IO.popen("/usr/local/bin/pig", "w+") pig.puts "ice cream after they go to bed"
pig.close_write # without this line, program may stopputs pig.gets
{% endhighlight %}

popen has one more twist. If the command you pass it is a single minus sign ( `–` ), popen will fork a new Ruby interpreter. 

Both this and the original interpreter will continue running by returning from the popen. 

The original process will receive an `IO` object back, and the child will receive `nil`.

This works only on operating systems that support the fork(2) call (and for now this excludes Windows).

{% highlight ruby %}
pipe = IO.popen("-", "w+")
if pipe
  pipe.puts "Get a job!"
  STDERR.puts "Child says '#{pipe.gets.chomp}'"
else
  STDERR.puts "Dad says '#{gets.chomp}'"
  puts "OK"
end
{% endhighlight %} 

`Kernel.fork` `Kernel.exec` `IO.pipe`

The file-naming convention of many IO methods and Kernel.open will also spawn subprocesses if you put a `| ` as the first character of the filename 

### Independent Children

{% highlight ruby %}
exec("sort testfile > output.txt") if fork.nil?
 # run sort in son
 # The sort is now running in a child process # carry on processing in the main program # ... dum di dum ... # then wait for the sort to finishProcess.wait
{% endhighlight %}


If you’d rather be notified when a child exits (instead of just waiting around), you can set up a signal handler using `Kernel.trap`

{% highlight ruby %}
trap("CLD") do # trap signal  pid = Process.wait  puts "Child pid #{pid}: terminated"endexec("sort testfile > output.txt") if fork.nil?
 # do other stuff...
{% endhighlight %}

### Blocks and Subprocesses

`IO.popen` works with a block in pretty much the same way as `File.open` does.

{% highlight ruby %}
IO.popen("date") {|f| puts "Date is #{f.gets}" }
{% endhighlight %}

If you associate a block with Kernel.fork, the code in the block will be run in a Ruby subprocess, and the parent will continue after the block.
{% highlight ruby %}
fork do  puts "In child, pid = #$$"  exit 99endpid = Process.waitputs "Child terminated, pid = #{pid}, status = #{$?.exitstatus}"{% endhighlight %}
`$?` is a global variable that contains information on the termination of a subprocess.

---
layout: post
title: "C4 Containers Blocks and Iterators"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# chapter4 Containers, Blocks and Iterators

## Containers

* Array
* Hash

### Arrays

{% highlight ruby %}
a = [ 3.14159, "pie", 99]
a.class -> Array
a.length -> 3

b = Array.new
b.class -> Array

a = [ 1, 3, 5, 7, 9 ]
a[-1] → 9
a[-2] → 7
a[-99] → nil

#[start, count]
a[1, 3] → [3, 5, 7]
a[3, 1] → [7]
a[-3, 2] → [5, 7]

#ranges [start..end]
a[1..3] → [3, 5, 7]
a[1...3] → [3, 5]
a[3..3] → [7]
a[-3..-1] → [5, 7, 9]
{% endhighlight %}

{% highlight ruby %}
a = [ 1, 3, 5, 7, 9 ] → [1, 3, 5, 7, 9]
a[1] = ’bat’ → [1, "bat", 5, 7, 9]
a[-3] = ’cat’ → [1, "bat", "cat", 7, 9]
a[3] = [ 9, 8 ] → [1, "bat", "cat", [9, 8], 9]
a[6] = 99 → [1, "bat", "cat", [9, 8], 9, nil, 99]
{% endhighlight %}

{% highlight ruby %}
a = [ 1, 3, 5, 7, 9 ] → [1, 3, 5, 7, 9]
a[2, 2] = ’cat’ → [1, 3, "cat", 9]
a[2, 0] = ’dog’ → [1, 3, "dog", "cat", 9]
a[1, 1] = [ 9, 8, 7 ] → [1, 9, 8, 7, "dog", "cat", 9]
a[0..3] = [] → ["dog", "cat", 9]
a[5..6] = 99, 98 → ["dog", "cat", 9, nil, nil, 99, 98]
{% endhighlight %}

Arrays have a large number of other useful methods. Using these, you can treat arrays as stacks, sets, queues, dequeues, and fifos

### Hashes

{% highlight ruby %}
h = { 'dog' => 'canine', 'cat' => 'feline', 'donkey' => 'asinine' }
h.length → 3
h['dog'] → "canine"
h['cow'] = 'bovine'
h[12] = 'dodecine'
h['cat'] = 99
h → {"cow"=>"bovine", "cat"=>99, 12=>"dodecine", "donkey"=>"asinine", "dog"=>"canine"}
{% endhighlight %}

## Implementing a SongList Container

* append(song) -> list
* delete_first -> song
* delete_last  -> song
* [index]      -> song
* with_title(title) -> song

{% highlight ruby %}
class SongList
  def initialize
    @songs = Array.new
  end
end

class SongList
  def append(song)
    @songs.push(song)
    self
  end
end

class SongList
  def delete_first
    @songs.shift
  end
  def delete_last
    @songs.pop
  end
end

class SongList
  def [](index)
    @songs[index]
  end
end
{% endhighlight %}

**TestUnit**

{% highlight ruby %}
require 'test/unit'class TestSongList < Test::Unit::TestCase  def test_delete    list = SongList.new    s1 = Song.new('title1', 'artist1', 1)
    s2 = Song.new('title2', 'artist2', 2)
    s3 = Song.new('title3', 'artist3', 3)
    s4 = Song.new('title4', 'artist4', 4)    list.append(s1).append(s2).append(s3).append(s4)    assert_equal(s1, list[0])    assert_equal(s3, list[2])    assert_nil(list[9])    assert_equal(s1, list.delete_first)    assert_equal(s2, list.delete_first)    assert_equal(s4, list.delete_last)    assert_equal(s3, list.delete_last)    assert_nil(list.delete_last)  end
end
{% endhighlight %}

## Blocks and Iterators

{% highlight ruby %}
class SongList
  def with_title(title)
    for i in 0..@songs.length
      return @songs[i] if title == @songs[i].name
    end
    return nil
  end
end
{% endhighlight %}

{% highlight ruby %}
class SongList
  def with_title(title)
    @songs.find{ |song| title == song.name }
  end
end
{% endhighlight %}

The method find is an iterator—a method that invokes a block of code repeatedly.

### Implementing Iterators

a Ruby block is a way of grouping statements, but not in the conventional way.

1. a block may appear only in the source adjacent to a method call
2. the code in the block is not executed at the time it is encountered.
3. Within the method, the block may be invoked, almost as if it were a method itself, using the **yield** statement. 

{% highlight ruby %}
def three_times
  yield  yield  yield
end
three_times { puts "Hello" }
{% endhighlight %}

{% highlight ruby %}
def fib_up_to(max)  i1, i2 = 1, 1  while i1 <= max    yield i1    i1, i2 = i2, i1+i2  # parallel assignment  end # parallel assignment (i1 = 1 and i2 = 1)endfib_up_to(1000) {|f| print f, " " }
{% endhighlight %}

{% highlight ruby %}
a = [1, 2]b = 'cat'a.each {|b| c = b * a[1] } 
a → [1,2]
b → 2   # b has changed? It's may not right
defined?(c) → nil
{% endhighlight %}

{% highlight ruby %}
class Array
  def find
    for i in 0..size
      value = self[i]
      return value if yield(value)
    end
    return nil
  end
end
{% endhighlight %}

{% highlight ruby %}
[1,3,5,7,9].each {|i| pust i}
["H","A","L"].collect {|x| x.succ} -> ["I","B","M"]

f = File.open("testfile")f.each do |line| # block can use do end to defineputs line endf.close
[1,3,5,7].inject(0) {|sum, element| sum + element } -> 16
[1,3,5,7].inject(1) {|product, element| product*element} -> 105{% endhighlight %}
### Internal and External Iterators
In the Ruby approach, the iterator is internal to the collection—it’s simply a method, identical to any other, that happens to call yield whenever it generates a new value. The thing that uses the iterator is just a block of code associated with this method.
In other languages, collections don’t contain their own iterators. Instead, they generate external helper objects that carry the iterator state. 

Generator library, which implements external iterators in Ruby for just such occasions.

### Blocks for Transactions

You can use blocks to define a chunk of code that must be run under some kind of transactional control. 

{% highlight ruby %}
class File  def File.open_and_process(*args)    f = File.open(*args)    yield f    f.close()
  end
end

File.open_and_process("testfile", "r") do |file|  while line = file.gets    puts line  end
end

class File  def File.my_open(*args)    result = file = File.new(*args)
    # If there's a block, pass in the file and close
    # the file when it returns    if block_given?   # check if a block given      result = yield file      file.close     end     return result  endend
{% endhighlight %}

### Blocks Can Be Closures

two buttons start and end

{% highlight ruby %}
start_button = Button.new("Start")pause_button = Button.new("Pause")
{% endhighlight %}

The obvious way of adding functionality to these buttons is to create subclasses of Button and have each subclass implement its own button_pressed method.

{% highlight ruby %}
class StartButton < Button  def initialize
    super("Start")  # invoke Button's initialize  end  def button_pressed    # do start actions...  end
endstart_button = StartButton.new
{% endhighlight %}

two problems

1. this will lead to a large number of subclasses.
2. the actions performed when a button is pressed are expressed at the wrong level; they are not a feature of the button but are a feature of the jukebox that uses the buttons. 

using blocks

{% highlight ruby %}
songlist = SongList.newclass JukeboxButton < Button  def initialize(label, &action) super(label)    @action = action  end  def button_pressed    @action.call(self)  end
endstart_button = JukeboxButton.new("Start") { songlist.start }
pause_button = JukeboxButton.new("Pause") { songlist.pause }
{% endhighlight %}

If the last parameter in a method definition is prefixed with an ampersand (such as **&action**), Ruby looks for a code block whenever that method is called. That code block is converted to an object of class Proc and assigned to the parameter. 

So what exactly do we have when we create a Proc object? The interesting thing is that it’s more than just a chunk of code. Associated with a block (and hence a Proc object) is all the context in which the block was defined: the value of self and the methods, variables, and constants in scope. Part of the magic of Ruby is that the block can still use all this original scope information even if the environment in which it was defined would otherwise have disappeared. In other languages, this facility is called a **closure**.

{% highlight ruby %}
def n_times(thing)  return lambda {|n| thing * n }  #method lambda, converts a block to a Proc object endp1 = n_times(23)p1.call(3) → 69p1.call(4) → 92p2 = n_times("Hello ")p2.call(3) → "Hello Hello Hello "
{% endhighlight %}

functional programming?


### Containers Everywhere

**Containers**, **blocks**, and **iterators** are core concepts in Ruby. The more you write in Ruby, the more you’ll find yourself moving away from conventional looping constructs. Instead, you’ll write classes that support iteration over their contents. And you’ll find that this code is compact, easy to read, and a joy to maintain.


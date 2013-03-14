---
layout: post
title: "C3 Classes Objects and Variables"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# chapter3 Classes, Objects, and Variables

{% highlight ruby %}
class Song
  def initialize(name, artist, duration)
    @name = name
    @artist = artist
    @duration = duration
  end
end
{% endhighlight %}

**initialize** is a special method in Ruby programs.When you call Song.new to create a new Song object, Ruby allocates some memory to hold an uninitialized object and thencallsthatobject’s initialize method,passing in any parameters that were passed to new. This gives you a chance to write code that sets up your object’s state.

Instance variables are accessible to all the methods in an object (aren't accessible outside the object?) , and each object has its own copy of its instance variables.

In Ruby, an instance variable is simply a name preceded by an “at” sign (`@`).


{% highlight ruby %}
song = Song.new("Bicylops", "Fleck", 260)
song.inspect
{% endhighlight %}

`.inspect` show the object's ID and instance variables

`song.to_s`, it sends to any object it wants to render as a string.

we can override `to_s` in our class

In Ruby, classes are never closed: you can always add methods to an existing class.

{% highlight ruby %}
class Song
  def to_s
    "Song: #@name--#@artist (#@duration)"
  end
end
song = Song.new("Bicylops", "Fleck", 260)
song.to_s → "Song: Bicylops--Fleck (260)"
{% endhighlight %}

## Inheritance and Messages

{% highlight ruby %}
class KaraokeSong < Song
  def initialize(name, artist, duration, lyrics)
    super(name, artist, duration)
    @lyrics = lyrics
  end
end
{% endhighlight %}

`< Song` a KaraokeSong is a subclass of Song

{% highlight ruby %}
#override to_s bad way
class KaraokeSong
  # ...
  def to_s
    "KS: #@name--#@artist (#@duration) [#@lyrics]"
  end
end
song = KaraokeSong.new("My Way", "Sinatra", 225, "And now, the...")
song.to_s → "KS: My Way--Sinatra (225) [And now, the...]"
{% endhighlight %}

the subclass directly accesses the instance variables of its ancestors.

When you invoke super with no arguments, Ruby sends a message to the parent of the current object, asking it to invoke a method of the same name as the method invoking super. It passes this method the parameters that were passed to the originally invoked method.

{% highlight ruby %}
class KaraokeSong < Song
  # Format ourselves as a string by appending
  # our lyrics to our parent's #to_s value.
  def to_s
    super + " [#@lyrics]" # super call the parent's to_s
  end
end
song = KaraokeSong.new("My Way", "Sinatra", 225, "And now, the...")
song.to_s → "Song: My Way--Sinatra (225) [And now, the...]"
{% endhighlight %}

If you don’t specify a parent when defining a class, Ruby supplies class `Object` as a default. This means that all objects have Object as an ancestor and that Object’s instance methods are available to every object in Ruby.

## Objects and Attributes

**attributes**, access and manipulate an object's instance variables

**Inheritance** and **Mixins**

{% highlight ruby %}
class Song
  def name
    @name
  end
  def artist
    @artist
  end
  def duration
    @duration
  end
end
song = Song.new("Bicylops", "Fleck", 260)
song.artist -> "Fleck"
song.name -> "Bicylops"
song.duration -> 260
{% endhighlight %}

shortcut

{% highlight ruby %}
class Song
  attr_reader :name, :artist, :duration
end
song = Song.new("Bicylops", "Fleck", 260)
song.artist -> "Fleck"
song.name -> "Bicylops"
song.duration -> 260
{% endhighlight %}

The construct :artist is an expression that returns a Symbol object corresponding to artist.

### Writable Attributes

{% highlight ruby %}
class Song
  def duration=(new_duration) # method name end with '='
    @duration = new_duration
  end
end
song = Song.new("Bicylops", "Fleck", 260)
song.duration -> 260
song.duration = 257 # set attribute with updated value
song.duration -> 257
{% endhighlight %}

shortcut

{% highlight ruby %}
class Song
  attr_writer :duration
end
song = Song.new("Bicylops", "Fleck", 260)
song.duration = 257
{% endhighlight %}

### Virtual Attributes

{% highlight ruby %}
class Song
  def duration_in_minutes
    @duration/60.0 # force floating point
  end
  def duration_in_minutes=(new_duration)
    @duration = (new_duration*60).to_i
  end
end
song = Song.new("Bicylops", "Fleck", 260)
song.duration_in_minutes -> 4.33333
song.duration_in_minutes = 4.2
song.duration -> 252
{% endhighlight %}

duration_in_minutes is a virtual instance variable.To the outside world, duration_in_minutes seems to be an attribute like any other. Internally, though, it has no corresponding instance variable.

### Attributes, Instance Variables, and Methods

An attribute is just a method.

The internal state is held in instance variables. The external state is exposed through methods we’re call- ing attributes. 

## Class Variables and Class Methods

all the classes we’ve created have contained instance variables and instance methods: variables that are associated with a particular instance of the class, and methods that work on those variables. Sometimes classes themselves need to have their own states. This is where **class variables** come in.

### Class Variables

A class variable is shared among all objects of a class,and it is also accessible to the class methods that we’ll describe later. 

Class variable names start with two “at” signs, such as `@@count`.

class variables must be initialized before they are used. Often this initialization is just a simple assignment in the body of the class definition.

{% highlight ruby %}
class Song@@plays = 0def initialize(name, artist, duration)  @name     = name  @artist   = artist  @duration = duration  @plays    = 0end
def play  @plays += 1 # same as @plays = @plays + 1 
  @@plays += 1  "This song: #@plays plays. Total #@@plays plays."  end 
end
{% endhighlight %}

### Class Methods

new is a class method

class methods are defined by placing the class name and a period in front of the method name

{% highlight ruby %}
class Example  def instance_method       # instance method  end  def Example.class_method  # class method  endend{% endhighlight %}
{% highlight ruby %}
class SongList  MAX_TIME = 5*60 # 5 minutes  def SongList.is_too_long(song)
    return song.duration > MAX_TIME  end
endsong1 = Song.new("Bicylops", "Fleck", 260) SongList.is_too_long(song1) → falsesong2 = Song.new("The Calling", "Santana", 468) SongList.is_too_long(song2) → true{% endhighlight %}
other way to define class method
{% highlight ruby %}
class Demo
  def Demo.meth1     # ...   end  def self.meth2    # ...  end
  class <<self    def meth3      # ...
    end  end
end
{% endhighlight %}

## Singletons and Other Constructors

{% highlight ruby %}
class MyLogger  private_class_method :new  #make new method private  @@logger = nil  def MyLogger.create    @@logger = new unless @@logger    @@logger
  endend{% endhighlight %}
## Access Control
three levels of protection
1. Public methods
2. Protected methods
3. Private methods

If a method is **protected**, it may be called by any instance of the defining class or its subclasses. 

If a method is **private**, it may be called only within the context of the calling object—it is never possible to access another object’s private methods directly, even if the object is of the same class as the caller.

Access control is determined **dynamically**, as the program runs, not statically. 

### Specifying Access Control

You specify access levels to methods within class or module definitions using one or more of the three **functions** `public`, `protected`, and `private`. 

{% highlight ruby %}
class MyClass    def method1  # default is 'public'      #...    end  protected      # subsequent methods will be 'protected'    def method2  # will be 'protected'      #...    end 
  private        # subsequent methods will be 'private'      def method3  # will be 'private'        #...      end 
  public         # subsequent methods will be 'public'      def method4 # and this will be 'public'        #...
      end
end{% endhighlight %}

Alternative

{% highlight ruby %}
class MyClass      def method1      end      # ... and so on      public    :method1, :method4      protected :method2      private   :method3end
{% endhighlight %}

Protected access is used when objects need to access the internal state of other objects of the same class. 

## Variables

{% highlight ruby %}
person = "Tim"
person.id -> 936870
person.class -> String
person -> Tim
{% endhighlight %}

A variable is simply a reference to an object. 

{% highlight ruby %}
person1 = "Tim"
person2 = person2

person1[0] = 'J'

person1 -> Jim
person2 -> Jim  # changed
{% endhighlight %}


{% highlight ruby %}
person1 = "Tim"
person2 = person1.dup # make a clone
person1[0] = "J"
person1 → "Jim"
person2 → "Tim"
{% endhighlight %}

You can also prevent anyone from changing a particular object by freezing it

{% highlight ruby %}
person1 = "Tim"person2 = person1person1.freeze # prevent modifications to the object person2[0] = "J", can't modify frozen string (TypeError)
{% endhighlight %}


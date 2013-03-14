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
{% endhighlihgt %}

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


---
layout: post
title: "C5 Standard Types"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# chapter5 Standard Types

## Numbers

**Fixnum** and **BigNum**

{% highlight ruby %}
num = 816.times do  puts "#{num.class}: #{num}"  num *= num
end
{% endhighlight %}

* 0 for octal
* 0d for decimal (the default)
* 0x for hex
* 0b for binary

{% highlight ruby %}
3.times { print "X " }
1.upto(5) {|i| print i, " " }
99.downto(95) {|i| print i, " " }
50.step(80, 5) {|i| print i, " " }
{% endhighlight %}

input was read as strings, not numbers

{% highlight ruby %}
v1, v2 = line.split # v1,v2 are string not numbers
Integer(v1) + Integer(v2)   #a number
v1 + v2                     #a string
{% endhighlight %}

## Strings

escape

'escape using "\\"' → escape using "\"
'That\'s right' → That's right

`#{ expr }`

{% highlight ruby %}
"Seconds/day: #{24*60*60}" -> Seconds/day: 86400
"This is line #$."         -> This is line 3 # #$

puts  "now is #{ def the(a)                     'the ' + a                 ￼end                 the('time')                } for all good coders..."
{% endhighlight %}

%q, %Q, and here documents

{% highlight ruby %}
%q/general single-quoted string/
%Q!general double-quoted string!

string = <<END_OF_STRING
  The body of the string
  is the input lines up to
  one ending with the same
  text that followed the '<<'
END_OF_STRING

print <<-STRING1, <<-STRING2       Concat       STRING1          enate          STRING2

 #indent the terminator
{% endhighlight %}

### Working with Strings

    /jazz/j00132.mp3 | 3:45 | Fats Waller | Ain't Misbehavin' 
    /jazz/j00319.mp3 | 2:58 | Louis Armstrong | Wonderful World
    /bgrass/bg0732.mp3| 4:09 | Strength in Numbers | Texas Red    ::::

{% highlight ruby %}
class WordIndex  def initialize    @index = {}  end  def add_to_index(obj, *phrases)
    phrases.each do |phrase|      phrase.scan(/\w[-\w']+/) do |word|
        word.downcase!        @index[word] = [] if @index[word].nil?
        @index[word].push(obj)
      end
    end  end  def lookup(word)    @index[word.downcase]  endend

class SongList  def initialize    @songs = Array.new    @index = WordIndex.new  end  def append(song)    @songs.push(song)    @index.add_to_index(song, song.name, song.artist) self  end  def lookup(word)    @index.lookup(word)  endend

songs = SongList.new  song_file.each do |line|    file, length, name, title = line.chomp.split(/\s*\|\s*/)
    name.squeeze!(" ")    mins, secs = length.scan(/\d+/)
    songs.append(Song.new(title, name, mins.to_i*60+secs.to_i))    end    puts songs.lookup("Fats")    puts songs.lookup("ain't")    puts songs.lookup("RED")    puts songs.lookup("WoRlD")
{% endhighlight %}

Note the exclamation mark at the end of the first downcase! method name. As with the squeeze! method we used previously, this is an indication that the method will modify the receiver in place, in this case converting the string to lowercase.

## Ranges

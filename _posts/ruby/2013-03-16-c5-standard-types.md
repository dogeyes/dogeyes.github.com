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

* sequences
* conditions
* intervals

### Ranges as Sequences

{% highlight ruby %}
1..10 -> 1,2,3,4,5,6,7,8,9,10 #include end
1…10 -> 1,2,3,4,5,6,7,8,9   # exclude end
{% endhighlight %}

ranges are not represented internally as lists, range object containing references to two Fixnum objects, you can convert a range to a list using the to_a method

{% highlight ruby %}
(1..10).to_a → [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
('bar'..'bat').to_a → ["bar", "bas", "bat"]

digits = 0..9
digits.include?(5)    -> true
digits.min            -> 0
digits.max            -> 9digits.reject {|i| i < 5 } -> [5,6,7,8,9]
digits.each {|digit| dial(digit) } -> 0..9
{% endhighlight %}

spaceship operator, <=> compares two values, returning −1, 0, or +1 depending on whether the first is less than, equal to, or greater than the second.

{% highlight ruby %}
class VU
  include Comparable # this should be mixing  attr :volume  def initialize(volume) # 0..9
    @volume = volume  end  def inspect    '#' * @volume  end  
  # Support for ranges <=> and succ  def <=>(other)    self.volume <=> other.volume  end  def succ    raise(IndexError, "Volume too big") if @volume >= 9
    VU.new(@volume.succ)  end
end

medium_volume = VU.new(4)..VU.new(7)medium_volume.to_a → [####, #####, ######, #######] medium_volume.include?(VU.new(3)) → false
{% endhighlight %}

### Ranges as Conditions

{% highlight ruby %}
while line = gets
  puts line if line =~ /start/ .. line =~ /end/
end
{% endhighlight %}

the same as, but no longer supported, and no error is raised

{% highlight ruby %}
while gets
  printf if /start/ .. /end/
end
{% endhighlight %}

### Ranges as Intervals

{% highlight ruby %}
(1..10) === 5   -> true
(1..10) === 15  -> false
(1..10) === 3.1415 -> true
('a'..'j') === 'c'  -> true
('a'..'j') === 'z'  -> false
{% endhighlight %}

## Regular Expressions

{% highlight ruby %}
a = Regexp.new('^\s*[a-z]')
b = /^\s*[a-z]/
c = %r{^\s*[a-z]}
{% endhighlight %}

Regexp#match(string)

=~ positive match

!~ negative match

{% highlight ruby %}
name = "Fats Waller"
name =~ /a/  -> 1
name =~ /z/  -> nil
/a/ =~ name -> 1
{% endhighlight %}

* $` preceded the match
* $& matched by the pattern
* $' after the match

{% highlight ruby %}
def show_regexp(a, re)
  if a =~ re
    "#{$`}<<#{$&}>>#{$'}"
  else
    "no match"
  end
end

show_regexp('very interesting', /t/)   ->  in<<t>>eresting
show_regexp('Fats Waller', /a/) -> F<<a>>ts Waller
show_regexp('Fats Waller', /ll/) -> ats Wa<<ll>>er
show_regexp('Fats Waller', /z/) -> no match
{% endhighlight %}

`$~` is a MatchData object 

$1, $2 and so on, hold the values of parts of the match.

### Patterns

Within a pattern, all characters except ., |, (, ), [, ], {, }, +, \, ^, $, *, and ? match themselves.

use \ to escape

### Anchors

* `^` for beginning of a line
* `$` for end of a line
* `\A` for beginning of a string
* `\z` for end of a string without `\n`
* `\Z` for end of a string with `\n`
* `\b` match word boundaries
* `\B` match no word boundaries

{% highlight ruby %}
show_regexp("this is\nthe time", /^the/) → this is\n<<the>> time
show_regexp("this is\nthe time", /is$/) → this <<is>>\nthe time
show_regexp("this is\nthe time", /\Athis/) → <<this>> is\nthe time
show_regexp("this is\nthe time", /\Athe/) → no match

show_regexp("this is\nthe time", /\bis/) → this <<is>>\nthe time
show_regexp("this is\nthe time", /\Bis/) → th<<is>> is\nthe time
{% endhighlight %} 

### Character Classes

A character class is a set of characters between brackets: [characters] matches any single character between the brackets.

* [aeiou]
* [,.:;!?]
* [[:digit:]]
* [[:space:]]

Put a `^` immediately after the opening bracket to negate a character class: [^a-z] matches any character that isn’t a lowercase alphabetic.

`.` for any character

* \d digit character
* \D not digit character
* \s whitespace character
* \S not whitespace character
* \w Word character
* \W not word character

### Repetition

* r* zero or more
* r+ one or more
* r? zero or one
* r{m,n} at least "m" and at most "n"
* r{m,} at least "m"
* r{m}  exactly "m"

These patterns are called `greedy`, because by default they will match as much of the string as they can. You can alter this behavior, and have them match the minimum, by adding a `question mark suffix`.

{% highlight ruby %}
show_regexp(a, /\s.*\s/) → The<< moon is made of >>cheese
show_regexp(a, /\s.*?\s/) → The<< moon >>is made of cheese
{% endhighlight %}


### Alternation

`|` matches either the regular expression that precedes it or the regular expression that follows it.

/d|e/

### Grouping

`()`

/(an)*/

Within the pattern, the sequence `\1` refers to the match of the first group, `\2` the second group, and so on. Outside the pattern, the special variables `$1`, `$2`, and so on, serve the same purpose.

{% highlight ruby %}
"12:50am" =~ /(\d\d):(\d\d)(..)/ -> 0
"Hour is #$1, minute #$2"        -> "Hour is 12, minute 50"
"12:50am" =~ /((\d\d):(\d\d))(..)/ -> 0
"Time is #$1"                      -> "Time is 12:50"
"Hour is #$2, minute #$3"          -> Hour is 12, minute 50"
"AM/PM is #$4"                     -> "AM/PM is am"
{% endhighlight %}

{% highlight ruby %}
 # match duplicated lettershow_regexp('He said "Hello"', /(\w)\1/) → He said "He<<ll>>o"
 # match duplicated substrings show_regexp('Mississippi', /(\w+)\1/) -> M<<ississ>>ippi
show_regexp('He said "Hello"', /(["']).*?\1/) -> He said <<"Hello">>
show_regexp("He said 'Hello'", /(["']).*?\1/) -> He said <<'Hello'>>
{% endhighlight %}

### Pattern-Based Substitution


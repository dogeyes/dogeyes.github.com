---
layout: post
title: "C1 Getting Started"
description: ""
category: programming ruby
tags: [programming ruby, 学习]
---
{% include JB/setup %}

# chapter1 Getting Started

## Running Ruby

{% highlight ruby %}
% ruby
puts "Hello, world!"
^D
{% endhighlight %}

not very convenient

### Interactive Ruby

`irb` Interactive Ruby

</br>

{% highlight ruby %}
load "code/rdoc/fib_example.rb"
{% endhighlight %}

try example code that's already in a file

### Ruby Programs

`ruby myprog.rb`

</br>

Unix “shebang” notation

{%highlight ruby %}
#!/usr/local/bin/ruby -w
puts "Hello, world!"
{% endhighlight %}


## Ruby Documentation: RDoc and ri

`ri ClassName`

`ri -c` `(ri -l)`? a list of classed with ri documentation

`ri GC` information for GC class

`ri enable` information for GC.enable

`ri GC.start`

`ri --help`




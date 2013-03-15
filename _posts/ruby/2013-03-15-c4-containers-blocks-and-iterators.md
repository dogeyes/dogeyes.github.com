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


---
layout: post
title:  "Adding terminal colors to Ruby strings"
date:   2015-02-17 16:52:00
categories: Ruby
---

One of the very interesting parts about Ruby is the philosphy that all classes are open to extension. Kind of like Objective C's [categories][categories], we can add methods to existing classes, even inbuilt ones, and have them be available to all code after that.

So then, if we are writing a command line utility, and we would like some colors to show up in our console output, we can make use of Ruby's class extension facilities to add coloring methods to ruby's `String` class itself. Here's how:

{% highlight ruby %}
class String
  def colorize(color_code)
    "\e[#{color_code}m#{self}\e[0m"
  end

  def red
    colorize(31)
  end

  def green
    colorize(32)
  end

  def bold
    "\033[1m#{self}\033[22m"
  end
end
{% endhighlight %}

Once Ruby reads that code, strings then magically develop capability to colorize themselves.

{% highlight ruby %}
"Hello, colorful world".green.bold
{% endhighlight %}

That will produce bold green colored output.

[categories]:	https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html

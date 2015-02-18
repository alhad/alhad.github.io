---
layout: post
title:  "First post"
date:   2015-02-17 15:44:39
categories: Java
---
This is a place for my technical musings, tips I find interesting, or amazing technological discoveries :)

To begin then, here is the Standard Binary search in Java (in
`java.util.Arrays`)

{% highlight java %}
public static int binarySearch(int[] a, int key) {
   int low = 0;
   int high = a.length - 1;

   whole (low <= high) {
      int mid = (low + high) / 2;
      int midVal = a[mid];

      if (midVal < key)
         low = mid + 1;
      else if (midVal >  key)
         high = mid - 1;
      else
         return mid;
   }
   return -(low + 1);
}
{% endhighlight %}

Question: There is a bug in it. What is it?

It is in the calculation of `mid`. The addition of `low` and `high` could produce a number that exceeds `MAX_INT`. In that case the array derefence will produce `ArrayIndexOutOfBoundsException` in Java (which is so much nicer than what C/C++ would do :) ). So then, how do we fix it? 

Here's one fix:
{% highlight java %}
int mid = low + ((high - low) / 2);
{% endhighlight %}

This was a bug in Java's binary search that was fixed very recently (~2006)

---
layout: post
title:  "C++ Function objects"
date:   2016-09-12 5:38:00 PM
categories: C++
---

I have often landed up in a situation where I really need to pass a C++ method in place of a C style callback, with the object on which this callback method is to be called being implicit. So I started wondering how can I do that. Initially `std::bind` seemed promising. After all, that is the purpose isn't it? We want to bind one of the parameters to the function (`this`) to a specific object. Well no, can't do that. `this` is implicit in method calls.

Next swipe at the problem: how about `mem_fn()`. That is a function adaptor that produces a function object that can be called as a nonmember function. But that requires you to pass the object on which the callback is to be called as a parameter to the callback. But I don't want to mess with the existing C style interface that defines the callback. Then what?

Function objects to the rescue. In C++, a function object is essentially "anything that can be called as a function". That means anything that responds to the "function call operator" i.e. `operator()`. That can be a struct or a class that implements `operator()`. Perfect! That is what I need. Here's how to do it:

{% highlight c++ %}

// Function that expects a C style callback
void do_something_useful(int some_data, void(*tell_me_when_done)(int));

// Let's make a C style callback-able function object

class MyClass {
//...
  void Actual_Callback_Method(int data) {
    // Handle the callback
  }
//...
};

auto my_obj = std::make_shared<My_Class>();

struct Callback_Fn {
  std::shared_ptr<MyClass> *obj_;
  Callback_Fn(std::shared_ptr<MyClass> obj) : obj_(obj) {}
  void operator()(int data) const { obj_->Actual_Callback_Method(data); }
};


int input; // Some input to give the C style function
do_something_useful(input, Callback_Fn(my_obj))

{% endhighlight %}

Since `Callback_Fn` has an `operator()` that takes an `int` and returns `void`, that matches the function pointer signature of `void(*tell_me_when_done)(int)`. Thus when the C style API calls its callback, that will go to the function object's `operator()`, which will then forward it to our object's callback method. The function object, since its an object after all, can maintain state, and in this case we have chosen to give it our created object. 

Thus we can provide C++ methods where a C style callback is to be expected. 

One thing to note: It may be tempting to use the function objects when you want to do "currying" i.e. making certain arguments already set and keep only a few variables. Idea being that the default values are member variables of the function objects. Well, don't do that. That is what `std::bind` is for. Use `std::bind` with place holders (_1, _2 and so on) to bind default values to function arguments and do currying.
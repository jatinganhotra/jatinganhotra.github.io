---
layout: post
title: "Comparison is always false due to limited range of data type"
date: 2013-07-21 21:17
author: Jatin Ganhotra (@jatinganhotra)
comments: true
categories: [C, C++, Coding Tips]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

This is a small and crisp post over this warning message that appeared quite strange at first. On a closer look though, the reason behind it was pretty clear and straight-forward.

> warning: comparison is always false due to limited range of data type

I was just playing around with `string.find()` function, when I got this warning message.
Here is a simple piece of code which, when run on a 64-bit architecture, will produce the above warning.

``` c
    string subject = "A[1]";
    string search = "1";
    unsigned location = subject.find(search);    // location is unsigned
    if( location == std::string::npos)
        cout << " true";
    else
        cout << " false";
```

The catch here is that `std::string::npos` returns `size_t`, which represents the unsigned native integer size on current architecture.  
So, on a 32-bit architecture:  
 - both `unsigned` and `size_t` would have the same size,  
However, on a 64-bit machine:  
 - the `unsigned` would be size - 4 and `size_t` would be size - 8.  
Hence, the comparison would be always false when run on a 64-bit machine.  

So, the proper fix for the above code is to make location variable of type __size_t__.  

For more information on `size_t`, refer to my blog post on [Integer Limits and Types in C/C++](http://jatinganhotra.com/blog/2012/08/25/integer-limits-and-types-in-c-cpp/) , which explains the sizes of basic C++ types.  

_Note:_ If you're seeing the same warning message, but not around the `string.find()` function, try __StackOverflow__ for help. The warning message is generic and can occur at places where a comparison is being done between types of different sizes.

---
layout: post
title: "Forward Class Declaration in C++"
date: 2012-11-25 21:00
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [Forward Class Declaration, C++, Coding Tips]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

"In computer programming, a [forward declaration]("http://en.wikipedia.org/wiki/Forward_declaration) is a declaration of an identifier (denoting an entity such as a type, a variable, or a function) for which the programmer has not yet given a complete definition."

In C++, you should forward declare classes instead of including headers. Don't use an #include when a forward declaration would suffice.
When you include a header file you introduce a dependency that will cause your code to be recompiled whenever the header file changes. If your header file includes other header files, any change to those files will cause any code that includes your header to be recompiled. Therefore, you should prefer to minimize includes, particularly includes of header files in other header files.

You can significantly reduce the number of header files you need to include in your own header files by using forward declarations. For example, if your header file uses the File class in ways that do not require access to the declaration of the File class, your header file can just forward declare class File; instead of having to #include "file.h".

This will in turn speed a little bit the compilation.

**When can I use the forward class declaration?**  
Put yourself in the compiler's position: when you forward declare a type, all the compiler know is that this type exists; it knows nothing about its size, members or methods. This is why it's called an _incomplete type_. Therefore, you cannot use the _incomplete_ type at places where the compiler would need to know the layout of the type.

Assuming the following forward declaration
`class Forward;`

**You can use forward declarations in all the following cases, where**  
{% gist 4144783 %}

**You can't use forward declarations in all the following cases, where**  
{% gist 4144778 %}

---
layout: post
title: "Integer Limits and Types In C/C++"
date: 2012-08-25 10:20
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [C, C++, Coding Tips, Integer Limits]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

Unlike [Java](http://java.com/en/) or [C#](http://en.wikipedia.org/wiki/C_Sharp_(programming_language), [primitive data types](http://en.wikipedia.org/wiki/Primitive_data_type) in C++ can vary in size depending on the platform. For example, int is not guaranteed to be a 32-bit integer. The size of basic C++ types depends on

* ### **The Architecture** (16 bits, 32 bits, 64 bits) and

You can expect [std::size_t](http://en.cppreference.com/w/cpp/types/size_t) to always represent the unsigned native integer size on current architecture. i.e. 16-bit, 32-bit or 64-bit. e.g  
On a x64-bit machine, with _x86_64_ hardware-platform and processor,
<code>sizeof(std::size_t) == sizeof(unsigned int) == 8 </code>  
where as, on a 32-bit machine, with _i386_ hardware-platform and _i686_ processor, 
<code>sizeof(std::size_t) == sizeof(unsigned int) == 4 </code>  
But as far as all the other built-in types go, it really depends on the compiler.

* ### **The Compiler**

The C++ standard does not specify the size of integral types in bytes, but it specifies minimum ranges they must be able to hold. You can infer minimum size in bits from the required range and the value of `CHAR_BIT` macro in <[climits](http://en.cppreference.com/w/cpp/header/climits)>, that defines the number of bits in a byte (in all but the most obscure platforms it's 8).  
_An important point_ to note for `char` is that its size is always 1 byte, or `CHAR_BIT` bits (hence the name). char is always a byte , but it's not always an octet. _A byte is the smallest addressable unit of memory (in most definitions), an octet is 8-bit unit of memory._
i.e `sizeof(char) == 1` for all implementations, but `CHAR_BIT` defines the size of a byte for a platform and it's not always 8 bit. There are platforms with 16-bit and 32-bit bytes, hence char will take up more than 8 bits, but it is still a byte.

A C++ (or C) implementation can define the size of a type in bytes `sizeof(type)` to any value, as long as

  1. the expression `sizeof(type) * CHAR_BIT` evaluates to the number of bits enough to contain required ranges, and
  2. the ordering of type is still valid (e.g. `sizeof(int) <= sizeof(long)`).  

The actual implementation-specific ranges can be found in <[limits.h](http://pubs.opengroup.org/onlinepubs/009695399/basedefs/limits.h.html)> header in C, or <[climits](http://en.cppreference.com/w/cpp/header/climits)> in C++ (or even better, templated [std::numeric_limits](http://en.cppreference.com/w/cpp/types/numeric_limits) in <[limits](http://en.cppreference.com/w/cpp/header/limits)> header).
Minimum ranges [required by the standard](http://www.open-std.org/JTC1/SC22/WG14/www/docs/n1256.pdf) (page 22) can be found using this C++ snippet:  
  
[GIST - C++ snippet - Listing of MIN/MAX values, attributes of Integral Types](https://gist.github.com/4368027).

### **Portability**  
If you are concerned with portability and want guaranteed size, or you want the name of the type reflects the size, 

* You can use the header [BOOST cstdint](http://www.boost.org/doc/libs/1%5F40%5F0/libs/integer/cstdint.htm). It's usually better than inventing your own wheel(which incurs the maintainence and testing), or
* You can use the header <[cinttypes](http://gcc.gnu.org/onlinedocs/libstdc++/libstdc++-html-USERS-4.2/cinttypes-source.html)>, where the following macros are available: `int8_t, int16_t, int32_t, uint8_t, uint16_t, uint32_t` etc.  
For more information on this, refer [Wiki- Inttypes.h](http://en.wikipedia.org/wiki/Inttypes.h#inttypes.h).  

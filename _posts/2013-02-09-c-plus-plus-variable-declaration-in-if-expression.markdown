---
layout: post
title: "C++ - Variable Declaration in 'if' expression"
date: 2013-02-09 09:16
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [C, C++, Coding Tips, Variable Declaration]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

Recently, I encountered a strange compiler error at work. I was trying something similar to this:
{% codeblock lang:objc %}
if( (int var1 = func1() ) && ( int var2 = func2() ) )
    { // Use var1 and var2 here - Doesn't compile }
{% endcodeblock %}
but, it didn't compile. So, I played my Jedi debugging tricks to see
what exactly went wrong?  
To my surprise, 
{% codeblock lang:objc %}
if( ( int var2 = func2() ) )  { //... } // Doesn't compile, note the extra () 
if( int var2 = func2() )      { //... } // Compiles just fine !!!
{% endcodeblock %}
Now, it was well clear that `()` was the pith of the error.  
To strengthen my understanding, I tried some more cases with `()`:
{% codeblock lang:objc %}
// Variable declaration inside (), both cases don't compile
bool x = (int d = 4);             
cout <<  (int c = 4);        
{% endcodeblock %}
The results were affirmative. So, I went to check the C++ Standard.  
The syntax specification in 6.4/1 gives the following for selection condition:  
{% codeblock %}
selection-statement:
if ( condition ) statement
    condition: 
        expression
        type-specifier-seq declarator = assignment-expression

// The declarator shall not specify a function or an array.
// The type-specifier-seq shall not contain typedef and shall not declare a new class or enumeration.
{% endcodeblock %}
The condition in an if or while statement can be either an expression, or a single variable declaration (with initialisation).  

The failing examples are neither valid statements, nor valid declarations, since a declaration can't form part of an expression. The syntax specification says a single declaration, with no parentheses or other adornments and they don't fit the "either expression or declaration" rule for the condition.

So, to sum it up, the **rules for variable declaration in a selection-statement** are:

1. can only have 1 variable declared per expression, 
2. the variable declaration must occur first in the expression and
3. can't have parenthesis around declaration ( as per the syntax specification)

**Q**. _Why the variable declaration must occur first, and why can we have only 1 variable declared?_  
**A:** Short-circuit evaluation of logic and the declared variable(s) scope. Consider this case:

{% codeblock lang:objc %}
if ( 0 && (int a = val)) { //... }    // what should "a" be initialized to? 
{% endcodeblock %}
The `&&` operator is a _short-circuit logical AND_. As the first part 0 is false, then the second part `(int a = val)` shouldn't be evaluated because the final answer will be false. If it isn't evaluated, then what to do with code later on that uses a? Should we initialize the variable and leave it undefined?  

For these reasons, the variable declaration must be first in the expression and the rules for variable declaration in a selection-statement stated above hold.

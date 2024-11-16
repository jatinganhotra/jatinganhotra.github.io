---
layout: post
title: "Concurrent and Sequential statements in Verilog"
date: 2013-02-24 22:32
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [Verilog]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

This is a brief post for beginners to Verilog language, coming from a C/Java background.
Verilog  differs from a conventional programming language in the sense that the execution of statements is not strictly sequential. Different code blocks are executed concurrently as opposed to the sequential execution of most programming languages. So, a beginner might get  perplexed, as to what is concurrent and what is not !!

###Concurrent Statements:  
All statements in Verilog are concurrent (unless they are inside a sequential block as discussed later). Concurrent means that the operations described in each line take place in parallel.
The commonly used concurrent constructs are gate instantiation and the continuous assignment statement.  

Note that no net should be assigned a value more than once with concurrent statements.e.g  
`assign sum = a + b + c;`  
can be re-written as  
`assign sum = a + b;`  
`assign sum = sum + c;`  
but, although equivalent sequentially, it’s incorrect as both statements are concurrent, so the value of sum is indeterminate.


###Sequential Statements:  
Sequential statements are similar to statements in a normal programming language. A sequence of statements is regarded as describing operations that take place one after the other instead of at the same time.

* All sequential Verilog statements must be inside a procedural block. 
* Sequential statements are placed inside a begin/end block and executed in sequential order within the block. However, the blocks themselves are executed concurrently.
* Verilog statements outside any process block are interpreted as concurrent statements and different procedural blocks execute concurrently.

The commonly used sequential constructs are:  
* _Initial_ and _always_ procedural blocks: _Initial_ blocks start execution at time zero and execute only once. Always blocks loop to execute over and over again, and as the name suggests, they execute always.  
* _if, case_ structures, _for and while loops_: Always appear inside an always block.  
Note that multiple assignment of values is allowed inside a procedural block. 

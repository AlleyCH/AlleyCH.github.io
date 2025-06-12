---
title: "Demystifying The Codegen Phase Part 1"
date: 
categories:
  - 
tags:
  - Vala
---

# Intro

I wanna start off and say I'm really glad that my last blog was helpful to many wanting to understand Vala's compiler. I hope this blog will also be informative and helpful.

A little bit more about the compiler

I wanna talk a little about the basics of the compiler again, but this time, catering to the codegen phase. The phase that I'm actually working on, but has the least information about it in the Vala Docs.

In my past blog, I briefly mentioned the directories [codegen] and [ccode] being part of the codegen phase. This blog will be going more into depth about it  

The codegen phase is where Valac turns your code into C, so than that C code can also get compiled, usually by gcc or another C compiler you installed. When dealing with this phase it's really beneficial to know and understand at least a little bit of C.

Using the command 'valac -c yourfilename.vala' you'd be able to see the C code that Valac generates. If you were to look into it, you'd see a bunch of temp variables. It can be a little overwhelming to see if you don't really know C. 


'''
*Put the corresponding Vala code.

'''
* explain code

''' 
* put C codeeeeeee to prove point. 
'''

* explain code C

*** Explain how the directories are working together to gen the c code


I'm happy to say that I am making a lot of progress with the JSON module I mentioned last blog. *** explain how this ties into the code examples above....

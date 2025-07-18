---
title: "YAML research"
date: 2025-07-17T12:30:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Intro

Midterms are this week for GSoC, which means I'm halfway through my time in GSoC. It's been an incredible experience so far, and I know it's gonna continue to be great.

# API vs ABI

What is the difference between an application programming interface versus an application binary interface? In the beginning, this question tripped me out and confused me because I wasn't familiar with ABIs. Understanding what an ABI has helped me decide which libraries are should consider using in the codegen phase. When talking about Vala, Vala is designed to use a C ABI. First, let's understand what they are separately and then compare them.

## API

Personally, I think Understanding API's is more popular and well known. An API is usually, at a high level, is defined by two software components or computers communicating with each other using a set of definitions and protocols. This defination is pretty vague and expansive. but when dealing with code level api's I like to understand it by APIs are existing entites in the source code that have functions, constants, structures, etc. You can think of it as when you write source code, you access the library through an API. For example, when you write ```print('hello world')``` in Python, ```print()``` is a part of Python’s standard library API. In that sense, APIs feel a lot like the Facade Design Pattern—they give you a simplified, organized way to access more complex functionality behind the scenes.

## ABI

ABI, on the other hand, is very similar, but instead of the compiler time, they are excuted during run time. Which means when your program is done compiling and the machine is actually running your excutable. It's low-level on how compiled code interacts, particularly in the context of operating systems and libraries. It has protocals and standards for how the OS handles your program, such as storage, memory, hardware, and about how your compiled binary works with other compiled components. 


# YAML

I've started to look into YAML and XML (mainly YAML). I've looked into many different libraries dealing with YAML, some libraries such as [pluie-yaml][pluie-yaml], [libyaml-glib][libyaml-glib], [glib-yaml][glib-yaml], and [libyaml][libyaml]. Now, to my understanding and research, there are no well-maintained YAML libraries that integrate GObject or GLib.

## Pluie yaml

I did mention [pluie-yaml][pluie-yaml], but this library isn't a C library. It's a shared Vala library. The codegen can use pure Vala libraries however, this libray is not maintained. The last active activity was 7 years ago.

## Libyaml glib 

[libyaml-glib][libyaml-glib] is a GLib binding of libyaml, plus a GObject builder that understands YAML. And just like pluie, it's not a C library. It's written in Vala, however, again it's not well maintained, with activity even longer 9 years ago.

## Glib yaml

[glib-yaml][glib-yaml] is a GLib-based YAML parser written in C. It again just like the other libraries doesn't pass the maintenance check since it's been years of no update or commit going back to 13 years ago. It's also only a parser, and it doesn't serialize or emit YAML, so if it were well maintained, I'd still need to emit YAML either manually or find a library that does so. 

## Libyaml

In conclusion, [libyaml][libyaml] is the C library that I will be using for parsing and emitting YAML. Vala already has a VAPI file binding it, [yaml-0.1.vapi][yaml-0.1.vapi]. However, there is no GObject or GLib integration, unlike json-glib, but that should be fine.


[.vapi]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/vapi?ref_type=heads
[json-glib]: https://gitlab.gnome.org/GNOME/json-glib/-/tree/main?ref_type=heads
[pluie-yaml]: https://github.com/pluie-org/lib-yaml
[yaml-glib]: https://github.com/rainwoodman/libyaml-glib
[libyaml]: https://github.com/yaml/libyaml/tree/master
[glib-yaml]: https://github.com/jimmuhk/glib-yaml
[yaml-0.1.vapi]: https://gitlab.gnome.org/GNOME/vala-extra-vapis/-/blob/master/yaml-0.1.vapi?ref_type=heads

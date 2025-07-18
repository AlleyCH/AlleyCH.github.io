---
title: "YAML research"
date: 2025-07-18T24:05:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Intro

Hi everyone, sorry for the late post. Midterms are this week for GSoC, which means I'm halfway through GSoC. It's been an incredible experience so far, and I know it's gonna continue to be great.

# API vs. ABI

What is the difference between an application programming interface versus an application binary interface? In the beginning, this question tripped me out and confused me, because I wasn't familiar with ABIs. Understanding what an ABI is has helped me decide which libraries I should consider using in the codegen phase. When talking about Vala, Vala is designed to use a C ABI. First, let's understand what they are separately and then compare them.

## API

Personally, I think Understanding API's is more popular and well known. An API is usually, at a high level, defined by two software components or computers communicating with each other using a set of definitions and protocols. This definition I always thought was pretty vague and expansive. When dealing with code-level APIs, I like to understand it as APIs are existing entities in the user code (source code) that have functions, constants, structures, etc. You can think of it as when you write code, you access libraries through an API. For example, when you write ```print('hello world')``` in Python, ```print()``` is a part of Python’s standard library API.

## ABI

ABI, on the other hand, is very similar, but instead of the compiler time, they are executed during runtime. Runtime means when your program is done compiling (going through the lexical, syntax, semantic analysis, etc) and the machine is actually running your executable. Its goals are very low-level and entails how compiled code should interact, particularly in the context of operating systems and libraries. It has protocols and standards for how the OS handles your program, such as storage, memory, hardware, and about how your compiled binary works with other compiled components. 

# YAML Libraries

I've started to look into YAML and XML (mainly YAML). I've looked into many different libraries dealing with YAML, some such as [pluie-yaml][pluie-yaml], [libyaml-glib][libyaml-glib], [glib-yaml][glib-yaml], and [libyaml][libyaml]. To my understanding and research, there are no well-maintained YAML libraries that integrate GObject or GLib. The goal is to find a well-maintained library that I can use in the codegen. 

## Pluie yaml

I mentioned [pluie-yaml][pluie-yaml], but this library isn't a C library like [json-glib][json-glib], it's a shared Vala library. The good thing is that the codegen can use pure Vala libraries because Vala libraries have a C ABI; however, the bad part is that this library is not well-maintained. The last activity was 7 years ago.

## Libyaml glib 

[Libyaml-glib][libyaml-glib] is a GLib binding of libyaml, plus a GObject builder that understands YAML. Just like pluie, it's not a C library. It's written in Vala. And just like pluie, it's not well-maintained, with the last activity even stretching to longer, 9 years ago.

## Glib yaml

[Glib-yaml][glib-yaml] is a GLib-based YAML parser written in C. It again, just like the other libraries, doesn't pass the maintenance check since it's been years of no updates or commits in the repo. Going all the way back to 13 years ago. It's also only a parser, and it doesn't serialize or emit YAML, so even if it were well-maintained, I'd still need to emit YAML either manually or find another library that does so. 

## Libyaml

In conclusion, [libyaml][libyaml] is the C library that I will be using for parsing and emitting YAML. It has a C ABI, and it's the most well-maintained out of all of the other libraries. Vala already has a VAPI file binding it, [yaml-0.1.vapi][yaml-0.1.vapi]. However, there is no GObject or GLib integration, unlike json-glib, but that should be fine.


[.vapi]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/vapi?ref_type=heads
[json-glib]: https://gitlab.gnome.org/GNOME/json-glib/-/tree/main?ref_type=heads
[pluie-yaml]: https://github.com/pluie-org/lib-yaml
[libyaml-glib]: https://github.com/rainwoodman/libyaml-glib
[libyaml]: https://github.com/yaml/libyaml/tree/master
[glib-yaml]: https://github.com/jimmuhk/glib-yaml
[yaml-0.1.vapi]: https://gitlab.gnome.org/GNOME/vala-extra-vapis/-/blob/master/yaml-0.1.vapi?ref_type=heads

---
title: "Challanges"
date: 2025-07-31T3:30:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Debugging and My Challenges

For the past two weeks, I've been debugging the [json module][valajsonmodule.vala]. I hooked up the JSON module into the codebase hierarchy by modifying [valagsignalmodule.vala][valagsignalmodule.vala] to extend the JSON module, which, before extended the [GObject module][valagobjectmodule.vala]. Running the test case called [json.vala][json.vala], crashes the program.
<br>

In the beginning, I was having quite the difficulty trying to use ```gdb``` and ```coredumpctl``` to investigate the crash. I kept doing:

```
./autogen.sh --enable-debug
make 
 ```
/
 ```
./configure --enable-debug
make
 ```
Then I'd run commands like:

 ```
coredumpctl gdb
coredumpctl info
 ```
It simply wasn't working when I built it this way with the following coredumpctl commands. It wasn't showing the debug symbols that I needed to be able to see the functions that were causing the program to crash. When I built Vala using GNOME Builder's build button, it also didn't work.
<br>

Lorenz, my mentor, helped me a lot on this issue. How we were able to fix this was first, I needed to build Vala by doing 

 ```
./configure --enable-debug
make
 ```

Then I needed to run the test in the 'build terminal' in GNOME Builder ```compiler/valac --pkg json-glib-1.0 tests/annotations/json.vala ```
<br>

Then, in a regular terminal, I ran:

 ```
gdb compiler/.libs/lt-valac
(gdb) run --pkg json-glib-1.0 tests/annotations/json.vala
(gdb) bt
 ```
Once I ran these commands, I was finally able to see the functions causing the crash to happen. 

 ```
123456#6  0x00007ffff7a1ef37 in vala_ccode_constant_construct_string (object_type=Python Exception <class 'gdb.error'>: value has been optimized out
, _name=0x5555563ef1c0 "anything") at /home/alley/Desktop/vala/ccode/valaccodeconstant.vala:41
#7  0x00007ffff7a1f9f7 in vala_ccode_constant_new_string (_name=0x5555563ef1c0 "anything") at /home/alley/Desktop/vala/ccode/valaccodeconstant.vala:40
#8  0x00007ffff7a10918 in vala_json_module_json_builder (self=0x55555558c810) at /home/alley/Desktop/vala/codegen/valajsonmodule.vala:292
#9  0x00007ffff7a0f07d in vala_json_module_generate_class_to_json (self=0x55555558c810, cl=0x555557199120) at /home/alley/Desktop/vala/codegen/valajsonmodule.vala:191
#10 0x00007ffff7a127f4 in vala_json_module_real_generate_class_init (base=0x55555558c810, cl=0x555557199120) at /home/alley/Desktop/vala/codegen/valajsonmodule.vala:410
 ```

This snippet of the backtrace shows that the function vala_json_module_json_builder () on line 292 was the actual crash culprit.
<br>

After I fixed the debug symbols, my git push decided not to work properly for a few days. So I was manually editing my changes on GitLab. My theory for git push not working is that Kwalletmanager had a problem, and so the credentials stopped working, which hanged the git push. Either way, I fixed it by switching my repo to SSH. I'll investigate why the HTTP side of git stopped working, and I'll fix it. 

# GUADEC 

This was my first-ever GUADEC event that I've ever watched. I watched it online, and I particularly found the lightning talks to be my favourite parts. They were all short, sweet, and to the point. It was also kind of comedic how fast people talked to fit everything they wanted to say in a short time span.
<br>

Some talks I particularly found interesting are:
<br>

1. The open source game called [Threadbare][Threadbare] by Endless Access. As someone who is a game programming graduate, it instantly caught my eye and had my attention. I'll definitely be checking it out and trying to contribute to it.

2. Carlos Garnacho's talk about GNOME on our TVs. The idea of GNOME expanding onto smart TVs opens up a whole new area of usability and user experience. It got me thinking about the specs of a regular TV set and how GNOME can adapt to and enhance that experience. The possibilities are exciting, and I’m curious to see how far this concept goes.
<br>

Overall, GUADEC made me feel more connected to the GNOME community even though I joined remotely. I'd love to have GAUDEC hosted in Toronto :)

[valagsignalmodule.vala]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/main/codegen/valagsignalmodule.vala?ref_type=heads
[valajsonmodule.vala]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/098c51eb28c99d4d9fa4786d84109782fe8cf2c3/codegen/valajsonmodule.vala
[Threadbare]: https://github.com/endlessm/threadbare
[json.vala]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/alley/json-glib-module/tests/annotations/json.vala?ref_type=heads
[valagobjectmodule.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/codegen/valagobjectmodule.vala?ref_type=heads

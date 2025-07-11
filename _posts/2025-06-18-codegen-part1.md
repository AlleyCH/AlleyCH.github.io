---
title: "Demystifying The Codegen Phase Part 1"
date: 2025-06-18T12:30:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Intro

I want to start off and say I'm really glad that my last blog was helpful to many wanting to understand Vala's compiler. I hope this blog will also be just as informative and helpful. I want to talk a little about the basics of the compiler again, but this time, catering to the codegen phase. The phase that I'm actually working on, but has the least information in the Vala Docs.

Last blog, I briefly mentioned the directories [codegen][codegen] and [ccode][ccode] being part of the codegen phase. This blog will be going more into depth about it. The codegen phase takes the AST and outputs the C code tree (ccode* objects), so that it can be generated to C code more easily, usually by GCC or another C compiler you installed. When dealing with this phase, it's really beneficial to know and understand at least a little bit of C.


# [ccode][ccode] Directory
* Many of the files in the ccode directory are derived from the class CCodeNode, [valaccodenode.vala][valaccodenode.vala].
* The files in this directory represent C Constructs. For example, the [valaccodefunction.vala][valaccodefunction.vala] file represents a C code function. Regular C functions have function names, parameters, return types, and bodies that add logic. Essentially, what this class specifically does, is provide the building blocks for building a function in C.

  <img src="https://logicmojo.com/assets/dist/new_pages/images/Cfunction3.png" width="200">
  
   ``` vala
     //...
    	writer.write_string (return_type);
		if (is_declaration) {
			writer.write_string (" ");
		} else {
			writer.write_newline ();
		}
		writer.write_string (name);
		writer.write_string (" (");
		int param_pos_begin = (is_declaration ? return_type.char_count () + 1 : 0 ) + name.char_count () + 2;

		bool has_args = (CCodeModifiers.PRINTF in modifiers || CCodeModifiers.SCANF in modifiers);
   //...
   ```
   
This code snippet is part of the ccodefunction file, and what it's doing is overriding the 'write' function that is originally from ccodenode. It's actually writing out the C function.

# [codegen][codegen] Directory
* The files in this directory are higher-level components responsible for taking the compiler’s internal representation, such as the AST and transforming it into the C code model ccode objects.
* Going back to the example of the ccodefunction, codegen will take a function node from the abstract syntax tree (AST), and will create a new ccodefunction object. It then fills this object with information like the return type, function name, parameters, and body, which are all derived from the AST. Then the CCodeFunction.write() (the code above) will generate and write out the C function.

  ``` vala
  //...
  private void add_get_property_function (Class cl) {
  		var get_prop = new CCodeFunction ("_vala_%s_get_property".printf (get_ccode_lower_case_name (cl, null)), "void");
  		get_prop.modifiers = CCodeModifiers.STATIC;
  		get_prop.add_parameter (new CCodeParameter ("object", "GObject *"));
  		get_prop.add_parameter (new CCodeParameter ("property_id", "guint"));
  		get_prop.add_parameter (new CCodeParameter ("value", "GValue *"));
  		get_prop.add_parameter (new CCodeParameter ("pspec", "GParamSpec *"));
  
  		push_function (get_prop);
  //...
  ```
  
This code snippet is from [valagobjectmodule.vala][valagobjectmodule.vala] and it's calling CCodeFunction (again from the [valaccodefunction.vala][valaccodefunction.vala]) and adding the parameters, which is calling [valaccodeparameter.vala][valaccodeparameter.vala]. What this would output is something that looks like this in C:

  ``` C
    void _vala_get_property (GObject *object, guint property_id, GValue *value, GParamSpec *pspec) {
       //... 
    }
  ```

# Why do all this?
Now you might ask why? Why separate codegen and ccode?

* We split things into codegen and ccode to keep the compiler organized, readable, and maintainable. It prevents us from having to constantly write C code representations from scratch all the time. 
* It also reinforces the idea of polymorphism and the ability that objects can behave differently depending on their subclass.
* And it lets us do hidden generation by adding new helper functions, temporary variables, or inlined optimizations after the AST and before the C code output.

# Jsonmodule
I'm happy to say that I am making a lot of progress with the JSON module I mentioned last blog. The [JSON module][valajsonmodule.vala] follows very closely other modules in the codegen, specifically like the [gtk module][valagtkmodule.vala] and the [gobject module][valagobjectmodule.vala]. It will be calling ccode functions to make ccode objects and creating helper methods so that the user doesn't need to manually override certain JSON methods.

[ccode]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/ccode?ref_type=heads
[codegen]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/codegen?ref_type=heads
[valaccodenode.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/ccode/valaccodenode.vala?ref_type=heads
[valagobjectmodule.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/codegen/valagobjectmodule.vala?ref_type=heads
[valaccodefunction.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/ccode/valaccodefunction.vala?ref_type=heads
[valagtkmodule.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/codegen/valagtkmodule.vala?ref_type=heads
[valagtkmodule.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/codegen/valagobjectmodule.vala?ref_type=heads
[valaccodeparameter.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/ccode/valaccodeparameter.vala?ref_type=heads
[valajsonmodule.vala]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/alley/json-glib-module/codegen/valajsonmodule.vala?ref_type=heads

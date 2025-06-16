---
title: "Demystifying The Codegen Phase Part 1"
date: 
categories:
  - 
tags:
  - Vala
---

# Intro

I want to start off and say I'm really glad that my last blog was helpful to many wanting to understand Vala's compiler. I hope this blog will also be just as informative and helpful. I want to talk a little about the basics of the compiler again, but this time, catering to the codegen phase. The phase that I'm actually working on, but has the least information in the Vala Docs.

In my past blog, I briefly mentioned the directories [codegen][codegen] and [ccode][ccode] being part of the codegen phase. This blog will be going more into depth about it. The codegen takes the AST and outputs the C code tree (ccode* objects), so that it can be generated to C code more easily, usually by GCC or another C compiler you installed. When dealing with this phase, it's really beneficial to know and understand at least a little bit of C.


# [ccode][ccode] Directory
* Many of the files in the ccode directory are derived from CCodeNode, [valaccodenode.vala][valaccodenode.vala].
The files in this directory are representing C Constructs. For example the [valaccodefunction.vala][valaccodefunction.vala] file repersents a C code function. Regular C functions have function names, parameters, return types, and Bodies that add logic. And what this class is are the building blocks for building a function in C.

  <img src="https://logicmojo.com/assets/dist/new_pages/images/Cfunction3.png" width="200">
  
   ``` vala
     //...
    	public override void write (CCodeWriter writer) {
    		writer.write_indent (line);
    		if (CCodeModifiers.INTERNAL in modifiers) {
    			writer.write_string (GNUC_INTERNAL);
    		} else if (is_declaration && CCodeModifiers.EXTERN in modifiers) {
    			writer.write_string ("VALA_EXTERN ");
    		}
    		if (!is_declaration && CCodeModifiers.NO_INLINE in modifiers) {
    			writer.write_string (GNUC_NO_INLINE);
    		}
    		if (CCodeModifiers.STATIC in modifiers) {
    			writer.write_string ("static ");
    		}
   //...
   ```
   
  
This code snipet is part of the ccodefunction file and what it's doing is overriding the 'write' function that is originally from ccodenode. It's actually writing out the the C code. 

# [codegen][codegen] Directory
* The files in this directory are higher-level components responsible for taking the compiler’s internal representation such as usually the AST and transforming it into the C code model ccode objects.
Going back to the example of the ccodefunction, codegen will take a function node from the abstract syntax tree (AST), and will create a new ccodefunction object. It then fills this object with information like the return type, function name, parameters, and body which are all derived from the AST. The the CCodeFunction.write() will generate the C code.

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
  
This code snippet is from [valagobjectmodule.vala][valagobjectmodule.vala] and it's calling CCodeFunction (again from the valaccodefunction.vala).  What this would out put is something that looks like this in C:

  ``` C
    void _vala_get_property (GObject *object, guint property_id, GValue *value, GParamSpec *pspec) {
        
    }
  ```

# Why do all this?

Now you might ask why? Why separtate codegen and ccode

* We split things into codegen and ccode to keep the compiler clean, readable, and maintainable. It prevents us from having to constantly write C code representations from scratch all the time. 
* It also reinforces the idea of polymorphism and the ability that objects can behave differently depending on their subclass.
* And it lets us do hidden generation by adding new helper functions, temporary variables, or inlined optimizations after the AST and before the C code.

# Jsonmodule
I'm happy to say that I am making a lot of progress with the JSON module I mentioned last blog. The jsonmodule will follow very closly other modules in the codegen. Which means that it's the part that will be calling ccode methods to be able to 

*********** ... Put some code I've done. 


[ccode]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/ccode?ref_type=heads
[codegen]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/codegen?ref_type=heads
[valaccodenode.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/ccode/valaccodenode.vala?ref_type=heads
[valagobjectmodule.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/codegen/valagobjectmodule.vala?ref_type=heads
[valaccodefunction.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/ccode/valaccodefunction.vala?ref_type=heads

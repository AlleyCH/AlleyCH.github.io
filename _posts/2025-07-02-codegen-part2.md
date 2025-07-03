---
title: "Demystifying The Codegen Phase Part 2"
date: 2025-07-02T3:30:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Intro
Hello again, I'm here to update my findings and knowledge about Vala. Last blog, I talked about the codegen phase, as intricate as it is, I'm finding some very helpful information that I want to share.

# Looking at The Outputted C Code
While doing the JSON module, I'm constantly looking at C code. Back and forth, back and forth, having more than 1 monitor is very helpful in times like these. 
<br>

At the beginning of GSoC I didn't know much of C, and that has definitely changed. I'm still not fluent in it, but I can finally read the code and understand it without too much brain power. For the [JsonModule][JsonModule] I'm creating, I first looked at how users can currently (de)serialize JSON. I went scouting json-glib examples since then, and for now, I will be using json-glib. In the future, however, I'll look at other ways in which we can have JSON more streamlined in Vala, whether that means growing away from json-glib or not.

Using the command 'valac -C yourfilename.vala', you'll be able to see the C code that Valac generates. If you were to look into it, you'd see a bunch of temporary variables and C functions. It can be a little overwhelming to see all this if you don't know C.
<br>

When writing JSON normally with minimal customization and without the JsonModule's support. You would be writing it like this:

   ``` vala
Json.Node node = Json.gobject_serialize (person);
Json.Generator gen = new Json.Generator ();
gen.set_root(node);
string result = gen.to_data (null);
print ("%s\n", result); 
  ```
  
This code is showing one way to serialize a GObject class using json-glib. 
<br></br>
The code below is a snippet of C code that Valac outputs for this example. Again, to be able to see this, you have to use the -C command when running your Vala code. 

   ``` c
static void
_vala_main (void)
{
		Person* person = NULL;
		Person* _tmp0_;
		JsonNode* node = NULL;
		JsonNode* _tmp1_;
		JsonGenerator* gen = NULL;
		JsonGenerator* _tmp2_;
		gchar* _result_ = NULL;
		gchar* _tmp3_;
		_tmp0_ = person_new ();
		person = _tmp0_;
		person_set_name (person, "Alley");
		person_set_age (person, 2);
		_tmp1_ = json_gobject_serialize ((GObject*) person);
		node = _tmp1_;
		_tmp2_ = json_generator_new ();
		gen = _tmp2_;
		json_generator_set_root (gen, node);
		_tmp3_ = json_generator_to_data (gen, NULL);
		_result_ = _tmp3_;
		g_print ("%s\n", _result_);
		_g_free0 (_result_);
		_g_object_unref0 (gen);
		__vala_JsonNode_free0 (node);
		_g_object_unref0 (person);
}
```

You can see many tempary variables denoted by the names ```__tmp*_```, but you can also see JsonNode being called, you can see Json's generator being called and setting root, and you can even see json gobject serialize. All of this was in our Vala code, and now it's all in the C code, having temporary variables containing them to be successfully compiled to C code.

# The jsonmodule
If you may recall the Codegen is the clash of Vala code, but also writing to C code. The steps I'm taking for the [JsonModule][JsonModule] are looking at the examples to (de)serialize then looking at how the example compiled to C. Since the whole purpose of my work is to write how the C should look like. I'm mainly going off of C's ```_vala_main``` function when determining which C code I should put into my module, but I'm also going off of what the Vala code the user put.

``` vala
// serializing gobject classes
	void generate_gclass_to_json (Class cl) {
		cfile.add_include ("json-glib/json-glib.h");

		var to_json_class = new CCodeFunction ("_json_%s_serialize_myclass".printf (get_ccode_lower_case_name (cl, null)), "void");
		to_json_class.add_parameter (new CCodeParameter ("gobject", "GObject *"));
		to_json_class.add_parameter (new CCodeParameter ("value", " GValue *"));
		to_json_class.add_parameter (new CCodeParameter ("pspec", "GParamSpec *"));
		
		//...

		var Json_gobject_serialize = new CCodeFunctionCall (new CCodeIdentifier ("json_gobject_serialize"));
		Json_gobject_serialize.add_argument (new CCodeIdentifier ("gobject"));

		// Json.Node node = Json.gobject_serialize (person); - vala code
		Json_gobject_serialize.add_argument (new CCodeIdentifier ("gobject"));
		var node_decl_right = new CCodeVariableDeclarator ("node", Json_gobject_serialize);
		var node_decl_left = new CCodeDeclaration ("JsonNode *");
		node_decl_left.add_declarator (node_decl_right);

		// Json.Generator gen = new Json.Generator (); - vala code
		var gen_decl_right = new CCodeVariableDeclarator ("generator", json_gen_new);
		var gen_decl_left = new CCodeDeclaration ("JsonGenerator *");
		gen_decl_left.add_declarator (gen_decl_right);

		// gen.set_root(node); - vala code
		var json_gen_set_root = new CCodeFunctionCall (new CCodeIdentifier ("json_generator_set_root"));
		json_gen_set_root.add_argument (new CCodeIdentifier ("generator"));
		json_gen_set_root.add_argument (new CCodeIdentifier ("node"));
		//...
```
The code snippet above is a work in progress method in the JsonModule that I created called 'generate_gclass_to_json' to generate serialization for GObject classes. I'm creating a C code function and passing parameters through it. I'm also filling the body with how the example code did the serializing in the first code snippet. Instead of the function calls being created in ```_vala_main``` (by the user), they'll have their own function that will instantly get created by the module instead.

``` c
static void _json_%s_serialize_myclass (GObject *gobject, GValue *value, GParamSpec *pspec)
{
	JsonNode *node = Json_gobject_serialize (gobject);
	JsonGenerator *generator = json_generator_new ();
	json_generator_set_root (generator, node);
	//...
}
```
Comparing the differences with the original Vala code and the compiled code (C code), it takes the Vala code shape, but it's written in C. 

[JsonModule]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/alley/json-glib-module/codegen/valajsonmodule.vala 

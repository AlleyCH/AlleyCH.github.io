---
title: "Demystifying The Codegen Phase Part 2"
date: 
categories:
  - 
tags:
  - Vala
---

# Intro
Hello again, I'm here to update on my findings and knowledge about Vala. Last blog, I talked about the codegen phase, as intricate as it is, I'm finding some very helpful information that I want to share.

# Looking at The Output C Code
While doing the JSON module, I'm constantly looking at C code. Back and forth, back and forth, having more than 1 monitor is very helpful in times like this. At the beginning of GSoC I didn't know much of C, and that has deffinitly changed. I'm not fluent in it, but I can finally read the code and someone can get an idea of it with out too much brain power. For the [JsonModule][JsonModule] I'm creating, I first looked at how users can currently (de)serialize JSON. I went scouting json-glib examples since, for now, I will be using json-glib in the codegen part. In the future, however, I'll look at other ways in which we can have JSON more streamlined in Vala.

Using the command 'valac -C yourfilename.vala', you'd be able to see the C code that Valac generates. If you were to look into it, you'd see a bunch of temp variables and C functions. It can be a little overwhelming to see if you don't know C. 

When writing JSON normally with minimal customization, without what my module suggests. You would be writing it like

   ``` vala
Json.Node node = Json.gobject_serialize (person);
Json.Generator gen = new Json.Generator ();
gen.set_root(node);
string result = gen.to_data (null);
print ("%s\n", result); 
  ```
  
This code is showing one way to serialize a GObject class

This code is the C code . 

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

You can see many temp variables, but you can also see JsonNode being called, you can see Json's generator being called, you can even see json gobject serialize. All of this was in your Vala code, and now it's all in the C code having temps equal them to be able to successfully compiler the C code.

*** Explain how the directories are working together to gen the C code

# The jsonmodule
The jsonModule 

# What I've learned so far
I've learned quite a lot, but I still have so much more to learn and understand.

I want to go over the stuff I've learned, having done this much up till now.


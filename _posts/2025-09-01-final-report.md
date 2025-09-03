---
title: "Final Report"
date: 2023-09-01T9:00:00-04:00
categories:
  - gnome
tags:
  - Vala
---

# Intro:
Hi everyone, it's the end of GSoc! I had a great experience throughout this whole process. I've learned so much. 
This is basically the 'final report' for GSoC, but not my final report in general for this project by a long shot. 
I still have so much more I want to do, but here is what I've done so far.

# Project:
JSON, YAML, and/or XML emitting and parsing integration into Vala's compiler.

# Mentor:
 I would like to thank [Lorenz Wildberg][Lorenz Wildberg] for being my mentor for this project, as well as the [Vala community][Vala community]. 

# Description:
 The main objective of this project is to integrate direct syntax support for parsing and emitting JSON, XML, and/or YAML formats in Vala. 
 This will cut back the boilerplate code, making it more user-friendly and efficient for developers working with these formatting languages. 

# What I've done
## Research
* I've done significant research in both JSON and YAML parsing and emitting in various languages like C#, Java, Rust and Python.
* Looked into how Vala currently handles JSON using JSON GLib classes and I then modelled the C code after the examples I collected.
* modelled the JSON module after other modules in the codegen. Specifically, mainly after Dbus, Gvariant, GObject, and GTK.

## Custom JSON Overrides and Attribute
* Created Vala syntax sugar specifically making a [JSON] attribute to do serialization.
* Built support for custom overrides as in mapping JSON keys to differently named fields/properties.
* Reduced boilerplate by generating C code behind the scenes automatically.

## Structs
* I've created both Vala functions to deserialize and serialize structs using JSON boxed functions.
* I created a Vala generate_struct_serialize_func function to create a C code function called "_%s_serialize_func" to serialize fields.
* I then created a Vala function generate_struct_to_json to create a C code function called "_json_%s_serialize_mystruct" to fully serialize the struct by using boxed serialize functions
<br>

* I created a Vala generate_struct_deserialize_func function to create a C code function called "_%s_deserialize_func" to deserialize fields.
* I then created a Vala function generate_struct_to_json to create a C code function called "_json_%s_deserialize_mystruct" to fully deserialize the struct by using boxed deserialized functions

## GObjects
* I've created both Vala functions to deserialize and serialize GObjects using json_gobject_serialize and JSONGenerator.
* I then created a Vala function generate_gclass_to_json to create a C code function called "_json_%s_serialize_gobject_myclass" to fully serialize GObjects.
<br>

* I created a Vala generate_gclass_from_json function to create a C code function called "_json_%s_deserialize_class" to deserialize fields.

## Non-GObjects
* I've done serializing of non GObjects using JSON GLib's builder functions.
* I then created a Vala function generate_class_to_json to create a C code function called "_json_%s_serialize_myclass" to fully serialize non objects that aren't
inheriting from Object or JSON.Serializable.


# Future Work
## Research
* Research still needs to be put into integrating XML and determining which library to use.
* The integration of YAML and other formatting languages other than JSON, YAML, or XML.
  
## Custom Overrides and Attributes
* I want to create more specialized attributes for JSON that only do serialization or deserialization. Such as [JsonDeserialize] and [JsonSerialize].
* [JSON] attribute needs to do both deserializing and serializing, and at the moment, the deserializing code has problems
* XML and YAML will follow very similar patterns with how attributes are concerned: [Yaml], [Xml], [Json].

# Links
* Here is the [JSON module][Jsonmodule] that has majority of the code.
* Here is the [merge request][merge request]

[Jsonmodule]: https://gitlab.gnome.org/AlleyChaggar/vala/-/blob/098c51eb28c99d4d9fa4786d84109782fe8cf2c3/codegen/valajsonmodule.vala
[Lorenz Wildberg]: https://mastodon.social/@lw64@chaos.social
[Vala community]: https://mastodon.social/@vala_lang
[merge request]:  https://gitlab.gnome.org/GNOME/vala/-/merge_requests/468 

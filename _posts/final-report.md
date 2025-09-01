---
title: "Final Report"
date: 
categories:
  - gnome
tags:
  - Vala
---

# Intro:
Hi everyone, it's the end of GSoc unfortonatly. I had a great experience throughtout this whole process. I've learned so much. 
This is basically the 'final report' for GSoC but not my final report in general for this project by a long shot. 
I still have so much more I want to do, but here is what i've done so far.

# Project:
JSON, YAML, and/or XML emitting and parsing integration into Vala's compiler.

# Mentor:
Lorenz

# Description:
 The main objective of this pproject is to integrate direct syntax support for parsing and emitting JSON, XML, and/or YAML formats in Vala. 
 This will cut back the boilerplate code making it more user-friendly and efficient for developers working with these formatting languages. 

# What I've done
## Research
* I've done significant research in both JSON and YAML parsing and emitting in various languages like C#, Java, Rust and Python.
* Looked into how Vala currently handles JSON using JSON GLib classes and I then moduled the C code after the examples.

## Custom JSON Overrides and Attribute
* Created Vala syntaxt sugar specifically making a [JSON] attribute to do serializing.
* Built support for custom overrides as in mapping JSON keys to differently named fields/properties.

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

## Non-Objects
* I've done serializing of non GObjects using JSON GLib's builder functions.
* I then created a Vala function generate_class_to_json to create a C code function called "_json_%s_serialize_myclass" to fully serialize non objects that aren't
inherenting from Object or JSON.Serializable.


# What needs to be done
* I want to create more specialized attributes that only do serializing or deserializing. 
* [JSON] attribute needs to do both deserializing and serializing and at the moment the deserializing code needs to ....
* 

# Links
* [Jsonmodule][Jsonmodule]


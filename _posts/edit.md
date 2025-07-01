# Intro
Hello again, I'm here to update on my findings and knowledge about Vala. Last blog, I talked about the codegen phase, as intricit as it is, I'm finding some very helpful information that I want to share.

# Looking at The Outputed C Code
While doing the JSON module I'm constantly looking at C code. Back and forth back anf forth, having more than 1 monitor is very helpful in times like this. In the beginning of GSoC I didn't know much of C, and that has deffinitly changed. I'm not fluent in it, but I can finally read the code and someone get an idea of it with out too much brain power. For the [JsonModule] I'm creating, I first looked at how users can currently (de)serialize JSON. I went scouting json-glib examples since, for now, I will be using json-glib in the codegen part. In the future however I'll look at other ways in which we can have JSON more streamlined in Vala.

Using the command 'valac -C yourfilename.vala' you'd be able to see the C code that Valac generates. If you were to look into it, you'd see a bunch of temp variables. It can be a little overwhelming to see if you don't really know C. 


'''
* Put the corresponding Vala code.

'''
* explain code

''' 
* put C codeeeeeee to prove point. 
'''

* explain code C

*** Explain how the directories are working together to gen the C code

# The jsonmodule
talk about the module and codegen



# What I've learned so far
I've learned quite a lot, but I still have so much more to learn and understand.

I want to go over the stuff I've learned, having done this much up till now.


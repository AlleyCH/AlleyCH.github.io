---
title: "Compiler Knowledge"
date: 2025-06-04T10:25:00-04:00
categories:
  - gnome
tags:
  - gnome
  - Vala
---

# Intro

I apologize that I'm a little late updating my blog, but over the past two weeks, I’ve been diving into Vala's compiler and exploring how JSON (de)serialization could be integrated. My mentor, Lorenz, and I agreed that focusing on JSON is a good beginning.

# Understanding the Vala Compiler

Learning the steps it takes to go from Vala code to C code is absolutely fascinating.

Vala's Compiler 101
* The first step in the compiler is the lexical analysis. This is handled by [valascanner.vala][valascanner.vala], where your Vala code gets tokenized, which breaks up your code into chunks called tokens that are easier for the compiler to understand.

 ``` vala
switch (begin[0]) {
		case 'f':
			if (matches (begin, "for")) return TokenType.FOR;
			break;
		case 'g':
			if (matches (begin, "get")) return TokenType.GET;
			break;
  ``` 
  The code above is a snippet of Vala's scanner, it's responsible for recognizing specific keywords like 'for' and 'get' and returning the appropriate token type.

* Next is syntax analysis and the creation of the abstract syntax tree (AST). In Vala, it's managed by [valaparser.vala][valaparser.vala], which checks if your code structure is correct, for example, if that pesky '}' is missing. 

  ``` vala
  inline bool expect (TokenType type) throws ParseError {
  	if (accept (type)) {
  		return true;
  	}
  
  	switch (type) {
  	case TokenType.CLOSE_BRACE:
  		safe_prev ();
  		report_parse_error (new ParseError.SYNTAX ("following block delimiter %s missing", type.to_string ()));
  		return true;
  	case TokenType.CLOSE_BRACKET:
  	case TokenType.CLOSE_PARENS:
  	case TokenType.SEMICOLON:
  		safe_prev ();
  		report_parse_error (new ParseError.SYNTAX ("following expression/statement delimiter %s missing", type.to_string ()));
  		return true;
  	default:
  		throw new ParseError.SYNTAX ("expected %s", type.to_string ());
  	}
  }
    ```
  This is a snippet of Vala's parser, it tries to accept a specific token type, like again that '}'. If '}' is there, it continues parsing. Else if not, it throws a syntax error.

* Then comes semantic analysis, the “meat and logic,” as I like to call it. This happens in [valasemanticanalyzer.vala][valasemanticanalyzer.vala], where the compiler checks if things make sense. Do the types match? Are you using the correct number of parameters?

  ``` vala
  public bool is_in_constructor () {
		unowned Symbol? sym = current_symbol;
		while (sym != null) {
			if (sym is Constructor) {
				return true;
			}
			sym = sym.parent_symbol;
		}
		return false;
	}
   ```
  This code is a snippet of Vala's semantic analyzer, which helps the compiler understand if the current code is a constructor. Starting from the current symbol, which represents where the compiler is in the code, it then moves through its parent symbols. If it finds a parent symbol that is a constructor, it returns true. Else if the parent symbol is null, it returns false.

* After that, the flow analysis phase, located in [valaflowanalyzer.vala][valaflowanalyzer.vala], analyzes the execution order of the code. It figures out how control flows through the program, which is useful for things like variable initialization and unreachable code.

  ``` vala
  public override void visit_lambda_expression (LambdaExpression le) {
  	var old_current_block = current_block;
  	var old_unreachable_reported = unreachable_reported;
  	var old_jump_stack = jump_stack;
  	mark_unreachable ();
  	jump_stack = new ArrayList<JumpTarget> ();
  
  	le.accept_children (this);
  
  	current_block = old_current_block;
  	unreachable_reported = old_unreachable_reported;
  	jump_stack = old_jump_stack;
  	}
  ``` 

  The snippet of Vala's flow analyzer ensures that control flow, like unreachable code or jump statements, is properly analyzed within the lambda expression.

* After all that, we now want to convert the Vala code into C code using a variety of Vala files in the directories [ccode][ccode] and [codegen][codegen].

All of these classes inherit from [valacodevisitor.vala][valacodevisitor.vala], which is basically the mother of classes that provides the `visit_*` methods that allow each phase in the compiler to walk the source code tree.

I know this brief isn't all of what there is to understand about the compiler, but it's a start. Also, let’s take a moment to appreciate everyone who has contributed to Vala’s compiler design, it’s truly an art 🎨


# The Coding Period Begins!!!

Now that GSoC's official coding period is here, I'm continuing my research on how to implement JSON support.

Right now, I'm still learning the codegen phase AKA the phase of converting vala into C, I’m exploring [json glib][json-glib] and starting to work on a valajsonmodule.vala in the code gen.

Another thing I want to work on is the Vala docs. The docs aren't bad, but I've realized the information is pretty limited the deeper you get into the compiler.

I’m excited that this is starting to slowly make sense, little by little.

[valascanner.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/vala/valascanner.vala?ref_type=heads
[valaparser.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/vala/valaparser.vala?ref_type=heads
[valaflowanalyzer.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/vala/valaflowanalyzer.vala?ref_type=heads
[valasemanticanalyzer.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/vala/valasemanticanalyzer.vala?ref_type=heads
[valacodevisitor.vala]: https://gitlab.gnome.org/GNOME/vala/-/blob/main/vala/valacodevisitor.vala?ref_type=heads
[json-glib]: https://gitlab.gnome.org/GNOME/json-glib
[ccode]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/ccode?ref_type=heads
[codegen]: https://gitlab.gnome.org/GNOME/vala/-/tree/main/codegen?ref_type=heads

---
layout: post
title:      "Hoisting (yo-ho mateys)"
date:       2017-12-01 21:47:21 +0000
permalink:  hoisting_yo-ho_mateys
---


Hoisting in Javascript refers to variable and function declarations to be pulled, or “hoisted”, to the top of their respective scope upon code execution. For example, if the following code were run line-by-line, top-to-bottom:

```
a = 2;
var a;
console.log(a);
```

The output would be `undefined`, because the variable is not declared until after the assignment was executed. Instead, 2 is logged. Why?

Consider another example:

```
console.log(a);
var a = 2;
```

A top-to-bottom execution of this code would expect a reference error, since the variable is not defined. Inferring from the first example instead, you might think 2 would be logged to the console. Instead, the actual console.log() output is `undefined`. What?

This happens because the javascript engine essentially runs your code twice, compiling the code before interpreting it. Part of this compilation associates all declarations with their appropriate scopes. In short, all variable and function declarations	are processed prior to any code being executed. Even if the declaration and assignment are in the same line of code, they are still functionally treated as separate statements by the engine, and executed at different times.

So our first example functionally looks like this:

```
var a;
a = 2;
console.log(a); // 2
```

And the second example looks like this:

```
var a;
console.log(a); // undefined
a = 2;
```

Only declarations are hoisted, in-scope. Assignments and executable logic are left in place. A function declaration includes the implied value of the function, thus a declared function can be invoked by an execution call on a line above the function declaration. This:

```
foo();
function foo() {
  console.log(a); // undefined
  var a = 2;
};
```

Is interpreted by the engine as:

```
function foo() {
  var a;  
  console.log(a); // undefined
  a = 2;
};
foo();
```

Important note: function expressions are NOT hoisted:

f```
oo(); //type error
var foo = function bar() {
 	     //code
	  };
```

Because `foo()` is attempting to invoke an undefined value, you get a type error. And even though `bar()` is a function declaration, it is not in the enclosing scope, so this:

```
foo(); //type error
bar(); //reference error
var foo = function bar() {
 	     //...
	  };
```

Is interpreted as:

```
var foo;
foo(); //type error
bar(); //reference error
foo = function() {
           var bar = … self …
	//...
	};
```

The JS engine declares functions before variables, so this:

```
foo(); //1
var foo;
function foo() {
  console.log(1);
};
foo = function() {
            console.log(2);
          };
```

Is interpreted as:

```
function foo(){
  console.log(1);
};
foo();
foo = function() {
            console.log(2);
          };
```

The `var foo` declaration is ignored by the engine as a duplicate declaration. However, multiple function declarations with the same name will overwrite the previous declaration, and should be avoided:

```
foo(); //3
function foo(){
  console.log(1);
};
function foo(){
  console.log(2);
};
function foo(){
  console.log(3);
};
```

To summarize: the javascript engine will hoist function and variable declarations to the top of their respective scopes, giving priority to function declarations. Assignment and executable logic remain in place, even if combined with a declaration. Function declarations includes the implied value of the function, and can thus be invoked by a function call in lines above the function declaration. Function expressions are not hoisted, and attempting to invoke them before they are assigned results in a type error by trying to invoke an undefined value.


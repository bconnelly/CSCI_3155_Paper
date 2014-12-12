**C++ Named Arguments**
**by Bryan Connelly, Johnlee Alvarez, Alix Jimenez, and Matthew Albrecht**  


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Document N4172 was proposed in October, 2014 by Ehnsan Akhgari and Botond Ballo of Mozilla.  As it currently stands, C++ associates function arguments with function parameters by position.  Their proposal would implement a mechanism that associates function arguments with function parameters by name.  According to the two authors, they believe that named arguments will increase readability of code and make it much easier to write.  Also, it would reduce the likelihood of error when calling functions.  Syntactically, named arguments would be declared by name : value.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Akhgari and Ballo state that currently programmers have to be careful when writing and reading function calls.  When reading a function, the only thing present at a call site is the values and the location relative to the other arguments.  In this case it can be unclear what each parameter is supposed to represent.  The only way to find what the parameters mean is to search for the function declaration or documentation to find what argument corresponds to what parameter.  Often times these function declarations are “far away” from the call site and commonly in other files such as header files.  However, if named arguments were used, it would be obvious at the call site of a function what each argument represents, assuming good argument names were used.  Take, for instance, this example of a function definition and function call:

    int mass(int height, int width, int depth, int density);
    int ObjectMass = mass(1, 2, 3, 4); 

Here it is not obvious what 1, 2, 3 and 4 are supposed to represent unless we look at the function declaration.  When we compare this to what a function call would look like with named arguments, the advantage becomes obvious:

    int ObjectMass = mass(height: 1, width: 2, depth: 3, density: 4);

We do not need to look at the function declaration to know that 1 represents height, 2 represents width, 3 represents depth and 4 represents density.  If this was consistently used it would massively improve the efficiency of reviewing and debugging code.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;When writing functions, one needs to be sure to write arguments in the order that parameters were declared in original function declaration.  This can create some problems, especially when there are many parameters of the same type.  For example, looking at the example we presented above, there are 4 arguments all of type int.  If the programmer meant to pass in the arguments in the order 1, 2, 3, 4 but swaps two of the parameters, it would be very hard to detect this mistake because all four are of type int and the compiler would not throw any kind of error or warning. An example of such a mistake is show below:

    int ObjectMass = mass(2, 1, 3, 4); 
	
With named arguments, this kind of mistake would be eliminated because not only would each argument be clearly labelled in the function call, but named arguments allow the programmer to pass in the arguments in any order. Here is an example of this function call with named arguments:

    int ObjectMass = mass(width: 2, height: 1, depth: 3, density: 4); 

As you can see, it doesn’t matter that the programmer forgot the order of the parameters when using named arguments.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Another case in which named parameters can be helpful is when dealing with boolean arguments.  

    enemySkills(100, true, “Lasers”, false, 12.4);

As you can see from the above example function call, the problem with passing a boolean when calling a function is that all you see is “true” or “false” which doesn’t tell you anything about how it will be used in the called function.  In a case where there are many booleans being passed it can get messy and hard to read.  

    enemySkills(isAgro: true,  isUltra: false,  health: 100,  skill: “Laser”,  range: 12.4);

With named arguments you can clearly see what is true and what is false and how they will be used in the called function.  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;A key feature to this proposal is that you don’t have to always have to name all arguments when calling a function.  The example below shows this:

    int ObjectMass = mass(1, 2, density: 4, depth: 3);

Clearly in the example above you can see that we have two arguments that aren’t named and two that are named.  Also notice how the two named arguments are allowed to be in a different order as we discussed earlier.  However, the one catch with this is that once you declare named arguments all the following arguments must be named as well.  This has its pros and cons.  It is helpful when you can remember a sequence of the first few arguments in the order they were declared.  And then after that you can name the rest as they come to mind.  This is where the downside lies.  You have to remember the rest of the arguments for this to be beneficial.  The other downside, to take a specific example, is if you only remember the first parameter and then you forget the second and then remember the third, you must use named arguments from the second parameter and on.  This is can be inefficient and make time writing code much longer.  Essentially, it would most likely be easier to just reference the function declaration to call the parameters properly.  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;There have been some conflicting views on how to implement the grammar of named arguments. This conflict is between using `name = value` and the documented discussed case, `name : value`. Many want the syntax `name = value` as in this forum (http://justinvh.blogspot.com/2012/10/c11-named-parameters-using-operator.html), however this would overload the `=` operator. Document N4172 proposes the syntax `name : value` because “there is precedent in other languages for using it to associate a key and a value (e.g. Python dictionaries, and because it does not create any ambiguity. `name=value` was considered, but rejected because `=` is an operator and thus there would be an ambiguity. A `:` only current appears at the beginning of a ctor-initializer, after an access-specifier, or in a labeled-statement, all contexts which are disjoint from expression-list, so there is no ambiguity”(http://www.open-std.org/). This relates implicitly to our lessons of ambiguity in class. In class we were taught how to get rid of ambiguity by writing a certain set of rules. This takes a commonly suggested format, i.e., `name = value`, and takes the ambiguity out of this grammar by changing the potentially overloaded `=` to a well known syntax `:`. This presents coders with a familiar grammar commonly used in other languages while taking away an ambiguous grammar problem.
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The main objection of this new syntax is the new feature caters to functions with many parameters which is bad programming style to begin with.  In the document they say “We agree that having functions with many parameters should be discouraged.  However, the reality is that many legacy APIs that programmers have to work with and will have to work with for a long time, have functions with many arguments, and making it easier to deal with such functions would solve a real problem and be materially useful.  More importantly, however, this feature has a lot of value for functions with few arguments, too.  Even for functions with few arguments, when reading a call site one has relatively little information about the roles of the arguments.  Having names of arguments present would make call sites more readable, regardless of the number of arguments”(http://www.open-std.org/). Another counter argument to this objection and a con of the proposed change is if there are many parameters then it will be hard to remember all of the named arguments anyway.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Another counterpoint to the introduction of this change was brought up by jhominal on Stack Exchange.  In many simple cases, the use of named arguments could overcomplicate function calls without making it any more clear what they do. Take the following example of a function that returns the smaller of two values passed into it, and two function calls, one with named arguments and one without:

    int min(int first, int second);
	
    int smallest = min(0, 1);
    int smallest = min(first: 0, second: 1)

Obviously in this case, it does not make it any more clear to used named arguments because the argument names do not clarify what the numbers represent. What the numbers represent is also obvious when considering the functionality of the function. However, because named arguments would be completely optional to use, this argument is irrelevant. Programmers in this case could decide not to use named arguments because it just makes it more verbose without clarifying anything.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Akhgari and Ballo took their proposal to Mozilla and c++ conferences. In the initial thread Milan Sreckovic brought up a case that is not covered by the change:

    class Base { 
  	virtual int f( int ba, char bb ); 
    }; 

    class Derived { 
      virtual int f( int da, char db );  // is this allowed and does it count like a base class function override? 
    }; 

    Derived d; 
    Base* b = &d; 

    // What is the validity of the four cases below? 

    b->f( ba=0, bb=‘c’ ); 
    b->f( da=0, db=‘c’ ); 
    d.f( ba=0, bb=‘c’ ); 
    d.f( da=0, db=‘c’ );

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mr. Ballo wrote back with these cases:
“The proposal does not address this explicitly. I think the following behaviour falls out naturally: 
  
    b->f( ba=0, bb=‘c’ );   // valid 
    b->f( da=0, db=‘c’ );   // invalid (1) 
    d.f( ba=0, bb=‘c’ );    // invalid (2) 
    d.f( da=0, db=‘c’ );    // valid 

since (1) name lookup and overload resolution is based on the static type (since they happen at compile time), and (2) when the static type is the derived type, the derived name hides the base name.“

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This proposal will be backwards compatible with older c++. This means less time wasted for programmers since they will not have to rewrite all legacy code.  The only things that will be affected are new call sites that use named arguments.
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;In conclusion, the proposed change of adding named arguments to C++ has its advantages and disadvantages. The implementation of this change would help eliminate semantic errors, greatly improve the readability of the code, and in many cases it would eliminate the need to look up the documentation of a function. However there are also complaints about the proposed addition of named arguments.  One being that it caters to bad programming styles where a function has a lot of parameters.  This is not the case because a programmer would have to remember all of the named arguments to all of those parameters, which is a con of the change. Another con of the change is if a coder writes in mixed named and positional arguments, then once a named argument is declared then all proceeding arguments must be named as well.  Partially named arguments comes with another con.  It only works if you forget a small number of the arguments.  Without knowing a majority of the arguments, you are almost guaranteed to have to refer to the function declaration which is time consuming and brings you back to the original reason why this change was proposed.  At the moment this proposal is still under review.


**Sources:**

http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4172.htm

http://programmers.stackexchange.com/questions/219593/why-do-many-languages-not-support-named-parameters

http://justinvh.blogspot.com/2012/10/c11-named-parameters-using-operator.html

http://weblogs.asp.net/scottgu/optional-parameters-and-named-arguments-in-c-4-and-a-cool-scenario-w-asp-net-mvc-2

https://groups.google.com/forum/#!topic/mozilla.dev.platform/2kLnr-ZPlzU


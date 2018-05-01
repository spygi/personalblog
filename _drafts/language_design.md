> Type enforcement can be static, catching potential errors at compile time, or dynamic, associating type information with values at run-time and consulting them as needed to detect imminent errors, or a combination of both.
[](https://en.wikipedia.org/wiki/Type_safety)

Static/Dynamic Typing is about when type information is acquired (Either at compile time or at runtime)

Untyped: no type checking.

Strong/Weak Typing: is about how strictly types are distinguished (e.g. whether the language tries to do an implicit coercion/conversion from strings to numbers).

https://stackoverflow.com/a/2351203/2259743


+ Static vs Dynamic typing: well defined. Has to do with when (compile vs runtime) the type system checks parameters. static means you need to define at initialization (compile time) what the variable holds and this can't change. Dynamic means types will evaluated on runtime and might throw runtime exceptions.
  + Static languages can be further be distinguished by type inference: Type inference is when you don't need to explicitly declare types as a dev e.g. both Go and Java are static but Go is type infered (Go wiki) and Java is not.
+ Strong vs Weak typing: not well defined, strong for most people means static. This has to do with how easy it is to work around the type system. C for example is weak because any pointer can be cast to any other pointer type.
Automatic/implicit conversions/promotions (like unboxing in Java or ) are sometimes mistaken as weak. val = "abc" + 123 in ruby/python results in runtime error

> Coercion is usually a symptom of weak typing. Conversion, on the other hand, creates a brand-new object of the appropriate type.

+ Duck typing is a "polymorphism" technique. Usually dynamic languages like Python are termed with this but static can also (e.g. Visitor pattern)


Examples
+ Java is static: variables have a declared type. But java is not infered. Java is kinda strongly typed because if you write `String s = "abc" + 123;` this creates a new object (conversion not coercion). Java
+ Python is dynamically typed: a variable can store any type. But strong: val = "abc" + 123 throws a type error.
+ Javascript is dynamically typed and very weak.

[Strong and weak](https://en.wikipedia.org/wiki/Strong_and_weak_typing)

Related: Type safety is closely linked to memory safety, a restriction on the ability to copy arbitrary bit patterns from one memory location to another

Compiled vs interpreted: an implementation of a language is orthogonal to its type system for example though it is the case that dynamic languages are usually interpreted https://softwareengineering.stackexchange.com/questions/88645/is-dynamic-language-always-interpreted

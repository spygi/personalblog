
## Final
- On classes: they can't be subclassed. Note that both abstract and concrete classes can be subclassed.
- On methods: they can't be overiden.
- On fields: they can't be modified once initialised.

## Access Identifiers
- Private (within class incl. nested), default/package, protected (outside package too by subclasses), public.
  - Top level (non-nested) classes/interfaces can be public or package only! [1]
- Note the usage of them in constructors eg: [why use a private constructor](https://stackoverflow.com/questions/2062560/what-is-the-use-of-making-constructor-private-in-a-class)?
  - A singleton
  - A utility class.
  - The constructor can only be accessed from static factory method inside the class itself

https://introcs.cs.princeton.edu/java/11precedence/


## Inheritance
- Difference of interface vs abstract class.

### Where to use what
- Common type? Abstract
- Common behavior? Interface
- Multiple inheritance is required? Interface
- Need private or protected identifiers? Abstract


## Override vs overloading

[Collections](http://www.javapractices.com/topic/TopicAction.do?Id=65)

## Strings
+ Strings are immutable in Java.
+ [Complexity of substring](https://stackoverflow.com/questions/4679746/time-complexity-of-javas-substring): used to be constant, after Java 7 is linear.


## Tips
- Character to int c-'0'
- Create array `new int[]{1,2,3}`, even 2d `new int[][]{{1,2}, {2,4}}`
- Array to Collection Arrays.asList(new Integer[]{1,2,3})
  - Careful it doesn't work with an int[]!
- Collection to stream
- Collections.unmodifiableList
- System.out.printf("A string: %s will appear with a new line %n", "Hello World")
- ... operator

https://www.geeksforgeeks.org/enum-in-java/

## Resources
- Effective Java

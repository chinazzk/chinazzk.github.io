---
title: 关于Python中类的学习
date: 2018-1-20 22:49:53
categories: Research
tags: 
   - python
mathjax: true
---
内容摘自《Building Skills in Python》 by Steven F. Lott   Begin with page259
<!--  more -->

##### CLASSES

Object-oriented programming permits us to organize our programs around the interactions of objects. **A class provides the definition of the structure and behavior of the objects; each object is an instance of a class. **Consequently, a typical program is a number of class definitions and a final main function. The main function **creates the objects** that will perform the job of the program.

##### 1.Semantics

**Define the semantics of objects and the classes which define their attributes (instance variables) and behaviors.**

An object is said to *encapsulate* a state of being and a set of behaviors; it is both data and processing. Each instance of a class has individual copies of attributes which are tightly coupled with the *class-wide operations*. Understanding objects by looking at four features.

- **Identity**  Each object is unique and is distinguishable from all other objects. In the real world, two otherwise identical coffee cups can be distiguished as distinct objects. For example, they occupy different locations on our desk. In the world of a computer’s memory, *objects could be identified by their address*, which would make them unique.
- **Classification** This is sometimes called Encapsulation(封装). Objects with the same attributes and behavior belong to a common class. Each individual object has unique attribute values. We saw this when we looked at the various collection classes. Two different *list* objects have the same general structure, and the same behavior. Both lists respond to append(), pop(), and all of the other methods of a list. However, each list object has a unique sequence of values.
- **Inheritance**  A class can inherit methods from a parent class, reusing(再利用) common features. A superclass is more general, a subclass overrides(重写 无视？) superclass features, and is more specific.
- **Polymorphism(多态多形)**  A general operation can have variant implementation(实现) methods，depending on the class of the object. We saw this when we noted that almost every class on Python has a + operation. Between two floating-point numbers the + operation adds the numbers, between two lists, however,the + operation concatenates the lists.

**Python’s Implementation.**  

A class is the Python-language definition of the features of individual objects: the names of the attributes and definitions of the operations. Python implements the general notion of attribute as a dictionary of instance variables for an object.(Python 将属性的认作是包含实例变量的字典) Python implements the general idea of an operation through a collection of methods or method functions of an object’s class. Note that all Python objects are instances of some class. Additionally, a class also constructs new object instances for us. Once we’ve defined the class, we can then use it as a kind of factory to create new objects.

**Class Definition.**  

- provide a distinct name to the class.
- list the superclasses from which a subclass inherits features. In Python 2, classes should explicitly be defined as subclasses of object. In Python 3, this will be the default. We have multiple inheritance available in Python.
- We provide method functions which define the operations for the class. We define the behavior of each object through its method functions.Note that the attributes of each object are created by an initialization method function (named `__init__()`) when the object is created.
- We can define attributes as part of the class definition. If we do, these will be class-level attributes, shared by all instances of the class.
- Python provides the required mechanism for unique identity. You can use the id() function to interrogate the unique identifier for each object. 


Technically, a class definition creates a new class object. This Python object contains the definitions of the method functions. Additionally, a class object can also own class-level variables; these are, in effect, attributes which are shared by each individual object of that class.

We can use this class object to create class instance objects. It’s the instances that do the real work of our programs. The class is simply a template or factory for creating the instance objects.

Python has a limited mechanism for making a distinction between the *defined interface* and the *private implementation* of a class. Python offers two simple techniques for separating interface from implementation.

- We can use a leading ‘_’ on an instance variable or method function name to make it more-or-less private to the class.
- We can use properties or descriptors to create more sophisticated protocols for accessing instance variables. We’ll wait until Attributes, Properties and Descriptors to cover these more advanced techniques. (不明白)

**An Object’s Lifecycle**  Each instance of every class has a lifecycle. The following is typical of most objects.

1. **Definition**  The class definition is read by the Python interpreter (or it is a built_in class). Class definitions are created by the class statement. Examples of built-in classes include `file, str, int,` etc.
2. **Construction**  An instance of the class is constructed: Python allocates(分配) the namespace for the object’s instance variables and associating the object with the class definition. The `__init__()` method is executed to initialize any attributes of the newly created instance.
3. **Access and Manipulation**  The instance’s methods are called (similar to function calls we covered in Functions), by client objects or the main script.  There is a considerable amount of collaboration (协作) among objects in most programs. Methods that report on the state of the object are sometimes called *accessors* (存取器);  methods that change the state of the object are sometimes called *mutators or manipulators* (操纵器).
4. **Garbage Collection(碎片回收)**  Eventually, there are no more references to this instance. Typically, the variable (with an object reference) was part of the body of a (function that finished), the namespace is dropped, and the variables no longer exist. Python detects this,  and removes the referenced object from memory, freeing up the storage for subsequent reuse. This freeing of memory is termed *garbage collection*, and happens automatically. 

##### 2.Class Definition: The class Statement

1. We create a class definition with a class statement. We provide the *class name*, the *parent classes*, and the *method function definitions*.

```python
class name ( parent ) :
     suite(套)
```

The `name` is the name of the class, and this name is used to create new objects that are instances of the class. Traditionally, class names are capitalized and class elements (variables and methods) are not capitalized.

The `parent` is the name of the parent class, from which this class can inherit attributes and operations. For simple classes, we define the parent as `object`. Failing to list `object` as a parent class is not a problem; using object as the superclass does make a few of the built-in functions a little easier to use. In Python 3.0, using object as a parent class will no longer be necessary. In Python 2.6, however, it is highly recommended.

**The `suite` is a series of function definitions, which define the class.  All of these function definitions must have a first positional argument, `self`, which Python uses to identify each object’s unique attribute values. The `suite` can also contain assignment statements which create instance variables and provide default values.**The suite typically begins with a comment string (often a triple-quoted string) that provides basic documentation on the class. This string becomes a special attribute, called `__doc__`. It is available via the `help()` function.

```python
import random
class Die(object):
"""Simulate a 6-sided die."""
def roll( self ):
	self.value= random.randint(1,6)
	return self.value
def getValue( self ):
	return self.value
```

1. We imported the random module to provide the random number generator.
2. We defined the simple class named `Die`, and claimed it as a subclass of `object`. The indented suite contains three elements.
   - The doc-string, which provides a simple definition of the real-world thing that this class represents. As with functions, the doc-string is retrieved with the `help()` function.
   - We defined a method function named `roll()`. This method function has the mandatory positional parameter, `self`, which is used to qualifty(限定子) the instance variables. The `self` variable is a namespace for all of the attributes and methods of this object.
   - We defined a method function named `getValue()`. This function will return the last value rolled.

When the `roll()` method of a `Die` object is executed, it sets that object’s instance variable, `self.value`, to a random value. Since the variable name, `value`, is qualified by the instance variable, `self`,  the variable is local to the specific instance of the object. If we omitted(省略) the `self` qualifier, Python would create a variable in the local namespace. The local namespace ceases to exist at the end of the method function execution, removing the local variables.

##### 3.Creating and Using Objects

Once we have a class definition, we can make objects which are instances of that class. We do this by evaluating the class as if it were a function:  for example, ‘`Die()`’. When we make one of these class calls, two things will happen.

- A new object is created. This object has a reference to its class definition.

- The object’s initializer method, `__init__()`, is called. We’ll look at how you define this method function in the next section.

Let's create two instances of our `Die`class.

```python
>>> d1= Die()
>>> d2= Die()
>>> d1.roll(), d2.roll()
(6, 5)
>>> d1.getValue(), d2.getValue()
(6, 5)
>>> d1, d2
(<__main__.Die object at 0x607bb0>, <__main__.Die object at 0x607b10>)
>>> d1.roll(), d2.roll()
(1, 3)
>>> d1.value, d2.value
(1, 3)

```

1. We use the Die class object to create two variables, `d1`, and `d2`;  both are new objects, instances of `Die`.
2. We evaluate the `roll()` method of `d1`; ; we also evaluate the `roll()` method of `d2`. Each of these calls sets the object’s `value` variable to a unique, random number. There’s a pretty good chance  (1 in 6) that both values might happen to be the same. If they are, simply call ‘`d1.roll()`’ and ‘`d2.roll()`’ again to get new values.
3. We evaluate the `getValue()` method of each object. The results aren’t too surprising, since the value attribute was set by the `roll()` method. This attribute will be changed the next time we call the `roll()` method.
4. We also ask for a representation of each object. If we provide a method named ` __str__()` in our class, that method is used; otherwise Python shows the memory address associated with the object. All we can see is that the numbers are different, indicating that these instances are distinct objects.

##### 4. Special Method Names

There are several special methods that are essential to the implementation of a class. Each of them has a name that begins and ends with double underscores. These method names are used implicitly by Python. we’ll look at a few special method names that are used heavily.

**`__init__(self, ...)`**

The `__init__()` method of a class is called by Python to initialize a newly-created object.Note that The `__init__()` method can accept parameters, but does not return anything.  It sets the internal state(内部状态) of the object.

**`__str__(self)`**

The `__str__()` method of a class is called whenever Python needs a string representation of an object. This is the method used by the `str()` built-in function. When printing an object, the `str()` is called implicitly to get the value that is printed.

**`__repr__(self )`**

The `__repr__()` method of a class is used when we want to see the details of an object’s values. This method is used by the `repr()` function.

**Initializing an Object with `__init__()`.**  When you create an object, Python will both create the object and also call the object’s `__init__()` method. This method function can create the object’s instance variables and perform any other one-time initialization. There are, typically, two kinds of instance variables that are created by the `__init__()` method:  variables based on parameters and variables that are independent of any parameters.

Here’s an example of a company description that might be suitable for evaluating stock performance. In this example, all of the instance variables (self.name, self.symbol, self.price) are based on parameters to the `__init__()` method.

```python
class Company( object ):
def __init__( self, name, symbol, stockPrice ):
	self.name= name
	self.symbol= symbol
	self.price= stockPrice
def valueOf( self, shares ):
	return shares * self.price
```

When we create an instance of Company, we use code like this.

```python
c1= Company( "General Electric", "GE", 30.125 )
```

This will provide three values to the parameters of `__init__()`.

**String value of an object with `__str__()`.**  The `__str__()` method function is called whenever an instance of a class needs to be converted to a string. Typically, this occus when use the `str()` function on an object. Also, when we reference object in a print statement, the `str()` function is evaluated. Consider this definition of the class `Card`.

```python
class Card( object ):
	def __init__( self, rank, suit ):
		self.rank= rank
		self.suit= suit
		self.points= rank
	def hard( self ):
		return self.points
	def soft( self ):
		return self.points

```

When we try to print an instance of the class, we get something like the following.

```python
>>> c = Card( 3, "D" )
>>> c
<__main__.Card object at 0x607fb0>
>>> str(c)
'<__main__.Card object at 0x607fb0>'
```

This is the default behavior for the `__str__()` method. We can, however, override this with a function that produces a more useful-looking result.

```python
def __str__( self ):
	return "%2d%s" % (self.rank, self.suit)
```

Adding this method function converts the current value of the die to a string and returns this. Now we get something much more useful.

```python
>>> d = Card( 4, "D" )
>>> d
<__main__.Card object at 0x607ed0>
>>> str(d)
' 4D'
>>> print d
4D
```

**Representation detail with `__repr__()`.**  While the `__str__()` method produces a human-readable string, we sometimes want the nitty-gritty details. The `__repr__()` method function is evaluated whenever an instance of a class must have its detailed representation shown. This is usually done in response to evaluating the `repr()` function. Examples include the following:

```python
>>> repr(c)
'<__main__.Card object at 0x607fb0>'
```

If we would like to produce a more useful result, we can override the `__repr__()` function. The objective is to produce a piece of Python programming that would reconstruct the original object.

```python
def __repr__( self ):
	return "Card(%d,%r)" % (self.rank,self.suit)
```

We use `__repr__()` to produce a clear definition of how to recreate the given object.

```python
>>> c = Card( 5, "D" )
>>> repr(c)
"Card(5,'D')"
```

**Special Attribute Names.** In addition to the special method names, each object has a number of  special attributes. 

We’ll look at just a few, including `__dict__`, `__class__` and `__doc__`.

`__dict__ ` The attribute variables of a class instance are kept in a special dictionary object named `__dict__`. As a consequence, when you say `self.attribute= value`, this has almost identical meaning to `self.__dict__['attribute']= value`. Combined with the % string formatting operation, this feature is handy for writing `__str__()` and `__repr__()` functions.

```python
def __str__( self ):
	return "%(rank)2s%(suit)s" % self.__dict__
def __repr__( self ):
	return "Card(%(rank)r,%(suit)r)" % self.__dict__
```

`__class__` This is the class to which the object belongs.

`__doc__` The doc_string from the class definition.
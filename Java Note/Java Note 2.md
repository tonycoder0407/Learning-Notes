# Java Note 2

Object is a kind of structure.

But primitive and reference, they are just somehow, values.

```java
public static void main(String[] args]){
	List list = new ArrayList();
	list.add("abc");  // list.add(Object e), means only an object can be added to a list
	list.add(new Object());
	list.add(100);    // boxing automatically
	System.out.println(list.size());     // output: 3
	System.out.println(list.get(2).getClass().getName()); // output: java.lang.Integer
}
```

int is a kind of primitive type but integer is a kind of reference type, means int can be converted to integer so that it can be treated as an object.

* byte Byte
* short Short
* int Integer
* long Long
* float Float
* double Double
* char Character           // Important
* boolean Boolean

```java
pubilc static void f(int i){

}
public static void main(String[] args){
		Integer I1 = 128;
		Integer I2= 128;
		System.out.println(I1 == I2);      // false, because I1 and I2 are not the same
    // But if I1 and I2 are between -128 and 127, it will be true. Because of they are in constant pool.
	// ----------------------------------------------------------------
    Integer I1 = new Integer(100);
	f(I1);  	// Correct, for unboxing is also automatically
}
```

Wrapper classes along with class String are immutable. Which means, any values from the objects of these classes CANNOT be changed.

Which means, if you run the code below:

```java
String[] s1 = {"abc","123",""};
String all = "";
for (String s:s1){
	all += s;
}
```

Then you will create a lot of Garbage objects. Because you are not changing this string but creating a new one.

```java
System.out.println(System.out.getClass().getName()); // output: java.io.PrintStream
int[] ints = new int[4];
System.out.println(ints.getClass().getName());         // output: [I 
```

Wrapper classes have an attribute called "TYPE", which means `Integer.TYPE == int.class` will return true. 

Long and Integer are all extended from class Number, which means int can be converted long but Integer cannot be converted to Long.

Father classes can not be converted into son classes only if you force convert them

Son classes can convert to father classes directly

BUT ONE SON WILL NOT BE A FATHER'S OTHER SONS!!!

```java
String s = "123";
System.out.println(Integer.parseInt(s)); // output: 123
```

Every wrapper class can provide a method named `parse*` (Like `parseInt()`,`parseDouble()`)to convert this object into this type.

`import java.lang.System;` or

`import java.lang.*;`          Not needed for compiler will automatically.          

`import java.lang.*;` will only import packages of this file but will not sub-packages of this.

For example,` import java.lang.* ` will not import ` java.lang.reflect.Method`;

You can also use ` java.awt.List l ` to make a difference from `import java.util.*; List l;`

`java.io.File f;` // This is called full qualified 

Objects created by new() will automatically placed into heap

Stack and heap are different for the different accessing method

One thread will maintain one stack so that there is only one heap but not only one stack

When calling `int i=100`, i will be stored into a frame of stack, one function or say one method will have one stack frame.

You can easily achieve overload by different inputs

But you can not simply override one function by returning

Calling which method with rewritten ones depends on the environment

Method in class always have a parameter `ClassName` this

Static method will not give the parameter of itself.

`Thread.sleep()` is a static method, which means the running thread will sleep

Also, static int i have only one, but int i can have many copies for each object of this class.

All class objects are in Method Area, static variables will be stored here along with class.

When you want to sleep one thread then write `Thread.sleep()` in this thread.

```java
class Person{                // singleton
    static Person person = new Person();
    private Person(){}
    pubilc static Person getPerson(){
        return person;
    }
}
public static void main(String[] args){
	Person p = person.getPerson();
}
```

Class object will never be collected, so static object of class will not never be collected

This is a stable GC Root, so remember to do p = null;

When analyzing memory leak problems, remember to check static members of the class

In UML, "-" stands for private and "+" stands for public

Inherit, using extends, is a kind of "is-a" relationship

Association, is a kind of "has-a" relationship, such like Teacher-Student （关联）

Aggregation, is a kind of "has-a" relationship, such like Car-Engine （聚合）

Composition, is a kind of "has-a" relationship, such like Body-Heart （组合）

Rely, is a kind of "depends-a" relationship

Constructor is used to initialize an object

```java
class Person{
    String pname = "abc";			// Specifying Initialization
    Person(){				// Constructor Initialization
        this.pname = "xyz";
    }
}
```

Specifying Initialization will be copied to every Constructor and be firstly executed.

You can call `this()` to call the overloaded constructor

Static Constructor:

```java
class Person{
    static int PersonCount;
    static{
        PersonCount=100;
    }
    static{
        PersonCount=110;
    }
}
```

Only one static constructor created

Problem remained: When will call the static constructor?
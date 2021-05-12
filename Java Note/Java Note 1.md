# Java Note 1

Java是C系列的 早起另一种为pascal
A to B to C to C++
面向过程 to 面向对象 有了cpp
混合语言
为了做一个类似嵌入式系统的语言 实现跨平台 产生了一个cpp based language 也就是Java
J2EE java 1.2 有一说一 我已经晕了

语法相对cpp简单一些

```java
System.out.println("Hello World");
```

System CLASS
out OBJECT
println METHOD
java1.8/jre/lib/rt.jar		using for runtime and updates are created here

```java
java.lang.System.out.println()
```

is equal to

```java
System.out.println()
```

* Portable
  * using *.java and *.class
    * .java means src code
    * .class means bytecode
* Robust
  * encourages error-free coding
  * Object-oriented
  * C++ is semi object oriented
    * We say everything is an object but some basic structures are not
* Interpreted
  * Bytecode can be interpreted on any platform by JVM
  * But C++ or C must rely on operating system
    * PID: Process ID
      * Process and Thread
      * One process can contain a lot of threads, which allows the operation system to easily manage and can run in parallel independently
        * Using jconsole.exe under Java1.8/bin/
    * Reference Handler  using this to handle things about reference
    * Finalizer  using for GC  Garbage Collection
  * rt.jar/java.util.concurrent: this package is about multi-thread
  * supports cross-platform through the use of Java bytecode
* High performance
  * Bytecodes are highly optimized
  * JVM can executed them much faster
* Distributed
  * Java was designed with the distributed environment
* Dynamic
* Memory Management
  * C++ Explicit 
  * Java Implicitly

Reference counter can be used for analyse whether an object should be collected as garbage
If an object's reference are set to be 0, it means this object is useless now, and GC collector will retrieve the space(Or say the RAM)
Things in heap are referenced from stack that are reachable, if you won't use them then set to None.
If a pair of objects(Maybe more) reference each other, they will all be collected as garbage.
ONLY GC ROOTS WILL GRAB THEM OUT OF COLLECTION.
UML: Unified Modeling Language
public: available everywhere
private: only in this class
protected: can be accessed by this class or its child

```java
public class  A{
							private class B{}

}
```

Top class can only use public(Or leave none, means only in this package) while inner class can use private or protected
in System.java:

```java
private System(){}
```

class system is more like a namespace, for Java can not create a function out of class define
Class {@code Object} is the root of the class hierarchy
Object.toString():

```java
public class Hello{
							private class person{
	}
				public static void main(String[] args){
							System.out.println(new Hello());         // line 1
}
}
```

System.out.println() requires String, so code from line 1 will call the toString() method of class Hello. However, there is no toString() method here, that's why it will call the father class Object's toString() method, which will return such things below:

```java
getClass().getName() + "@" + Integer.toHexString(hashCode())
```

Of course, you can use this:

```java
@Override
public String toString(){
		// return super.toString();       // This means reuse that toString of class Object
		return "Hello world";
}
```

hashCode()
For things like integer or other basic structure, using "==" is the same as using equals().
But for those are not basic structure, "==" means they have the same address or say they are just one object. Apparently, override equals() will be better, such as:

```java
public boolean equals(Object obj){
				return this==obj;            // Origin one
}
```

Try to override:
```java
@Override
public boolean equals(Person p){
			Person person = (Person) p;
			return (name.equals(person.name) && pid==person.pid);
}
```

Blank lines and space characters, white space is ignored by compiler.
Java is case sensitive. Code, CODE, code are not the same.
<IN COMMAND LINE>
--javac ***.java
--java ***.class
<OUTPUT>
Why public static void main(String[] args)?
args are options from cmd, powershell or something like this.
println(output) is the same as print(output,"\n")
Primitive 8 kinds
byte(1), short(2), int(4), long(8), float, double, char(2), boolean (all signed)
Reference  Infinity kinds
What is charset?
Charset means a checklist, we can use C3 C7(Hex code) to represent "们"
Unicode means utf-16, there is a problem that 2 bytes need an order, which one is the former one.
BMP Basic Multilingual Plane
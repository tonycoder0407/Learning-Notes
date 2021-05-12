# Java Note 6

Finalizer: Using F-Queue to do GC

F-Queue is a kind of data structure, stores the objects that have finalize()

By orderly calling finalize() method achieves cleanup

finalize() will only be called once during the lifetime of the object

```java
package com.tony;

public class Main {
    public static Main SaveMe =null;

    @Override
    protected void finalize() throws Throwable {
        SaveMe = this;
        System.out.println("In finalize........");
        super.finalize();
    }

    public static void main(String[] args) throws InterruptedException {
        SaveMe = new Main();
        SaveMe = null;
        System.gc();
        Thread.sleep(500);
        if (SaveMe ==null){
            System.out.println("I am dead......");
        }else {
            System.out.println("I am Still alive...");
        }


        SaveMe = null;
        System.gc();
        Thread.sleep(500);
        if (SaveMe ==null){
            System.out.println("I am dead......");
        }else {
            System.out.println("I am Still alive...");
        }
    }
}
```

output:

```
In finalize........
I am Still alive...
I am dead......
```

Why?

As we talked about this, finalize() will only call once, that's why when you do 

```java
SaveMe = this;
```

"SaveMe" will be remodified as an object in use but when you secondly wanna do this but failed.

Also, when you do something like

```java
while(true){
    ......
}
```

You will find out that this method will make GC stop.

So in order to avoid such thing happen, finalize() method will automatically exit after running for a certain time.

And of course, this part of code will never run again.

We can't achieve multiple inheritance in java

Then how do we do multiple inheritance?

Using Interface

Making the class inherit his super class and implements an interface

But:

```java
class A{
	void f(){
		return;
	};
}
class B extends A implements C{
	void f(){
		return;
	};
}
Interface C{
	void f();
}
```

There will have one problem

How to resolve the conflict?

We use an operator called "instanceof"

When you create a son class object

You will certainly call father's constructor

So you can cast a son to a father for you really have one

But when you try to cast a father to son

If the father is created by son

That's ok.

But if not?

Then you will need to force cast and be responsible for the result

```java
class Person{
    Person(int i){
        
    }
}
class Emp extends Person{
  	// Error, for you can't direct call a father's constructor
    // But you can add a Person() constructor in super class
    // Emp(){  
    // }
    Emp(){
        // Must be here, for you need to complete super class constuctor before you do ANYTHING ELSE
        super(1);
    }
}
```

You can use abstract class to avoid creating object of this class

This means you can't directly create this object but you can achieve by its son classes.

```java
abstract class Shape{
    Shape(){
        
    }
}
class Circle extends Shape{
    
}
class Rectangle extends Shape{
    
}
```

This means you need to do something like:

```java
Shape s = new Circle();
```

And also, you may use final to avoid this class being inherited

```java
final class A{
    
}
// Error: can't inherit from A
class B extends A{
    
}
```

Abstract methods only exist in abstract classes

So son class of abstract class must implement the method or be an abstract class

```java
abstract class Shape{
    Shape(){

    }
    // design pattern
    // template method
    abstract void f();
    void g(){
        f();
    }
}
```

It works, and it works well!

Why?

Imagine this code:

```java
Shape s = new Circle();
s.g();
```

Circle Object can be created

So Circle is not an abstract class

And Circle will of course have @Override f()!

Amazing!

This is what we call template method.

```java
abstract class Shape{

}
interface ShapeInterface{

}
interface SubInterface extends ShapeInterface{

}
interface Sub2 extends SubInterface,ShapeInterface{
    
}
class Circle extends Shape implements ShapeInterface, SubInterface{
    
}
```

Interface can have multiple extends

Class can have multiple implements

```java
void f(ShapeInterface shapeInterface){
    
}
interface ShapeInterface{

}
```

This means you need to use an object that its class achieves this interface

SOLID

O: Open-Close

Polymorphism

Enables you to "Program in general" rather than "Program in specific"

Polymorphism enables you to write programs that process objects that share the same superclass as if they're all objects of superclass. Simplifies programming.

We can't make per class method private in son class if it is public

Means we can't limit the access level

For running environment cannot make sure whether this method can be accessed

But when it talks about exception

Only fewer ones can be accepted

For running environment, it will throw super class method's exception

If son class have more kinds of exception, it won't be able to catch

Polymorphism is only about methods but not attributes

@Override is a kind annotation, means making sure father class method is rewritten

private and final methods will be directly bind by the compiler

Not all the time we want to use Polymorphism

If we want to achieve Polymorphism

* Inheritance
* Override
* PIG

To shut down or turn off Polymorphism, we use

* final class 

  * ```java
    public final class String{}
    ```

    

* final method

  * ```java
    public final native Class<?> getClass(); 
    ```

    

Polymorphism Based on Interface is more popular than Polymorphism Base on Class

For you can have more freedom like multiple inheritance
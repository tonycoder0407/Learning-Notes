# Java Note 9

Some Swing components

* JLabel

* JButton

* JRadioButton

* JTextField

* JTextArea

* JScrollPane

  * JScrollPane(Component View, int vsbPolicy, int hsbPolicy)

  * ```java
    JTextField jTextField;
    JScrollPane jScrollPane = new JScrollPane(jTextField,ScrollPaneConstants.VERTICAL_SCROLLBAR_ALWAYS,ScrollPaneConstants.HORIZONTAL_SCROLLBAR_ALWAYS);
    ```

* JList

Chapter 17

Files, Streams, Object Serialization

(I/O Operations)

We use Stream to do input and output operations

We then got InputStream and OutputStream

Or we can have ByteStream and CharacterStream

Combine the two categories then we have four specific kinds of stream

We have a string "我们学习Java"

This string has 8 characters and 12 bytes

And CharacterStream can only handle text

But you can use ByteStream to handle any input

java.io.InputStream => Input Byte Stream

java.io.OutputStream => Output Byte Stream

java.io.Reader => Input Character Stream

java.io.Writer => Output Character Stream

java.io.InputStreamReader => Convert InputStream to Reader

Why not readLine() ?

Because you can not define "\n" in Byte Stream and you can't provide specific buffer for such line

You won't know how long it will be and without buffer

(一拳就把你栈内存打咕遮了 我劝 年轻人耗子尾汁 好好反思 以后不要再有这样的小聪明啊)

Design Pattern -- Decorate Mode

* Component
  * Concrete Components 
  * Decorators
    * Concrete Decorators

```java
package com.tony;



public class Main{
    public static void main(String[] args) {
        Vehicle v = new CargoVehicle(new GasolineVehicle(new LandVehicle()));
        v.run();
    }

}
abstract class Vehicle{
    abstract public void run();
}
class LandVehicle extends Vehicle{

    @Override
    public void run() {
        System.out.println("Running on the land.....");
    }
}
class VehicleDecorator extends Vehicle{
    private final Vehicle v;

    public VehicleDecorator(Vehicle v) {
        this.v = v;
    }

    @Override
    public void run() {
        v.run();
    }
}
class GasolineVehicle extends VehicleDecorator{

    public GasolineVehicle(Vehicle v) {
        super(v);
    }
    @Override
    public void run() {
        System.out.println("Fill in the tank....");
        super.run();
    }
}

class CargoVehicle extends VehicleDecorator{

    public CargoVehicle(Vehicle v) {
        super(v);
    }
    @Override
    public void run() {
        System.out.println("Get all the cargo....");
        super.run();
    }
}
```

A demo for this design pattern

Now we use more objects to avoid creating more classes

To create a Cargo Vehicle running on the land and spend gasoline, now we create 3 objects but have 3 classes(6 classes if each level has two)

Or we may create 8 classes to create one object (Assume 2 choice each level)

Using this pattern, we can combine a lot of decorate classes but not combine and do some 2^n work.
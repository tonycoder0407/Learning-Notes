# Java Note 8

Chapter 11 Exception Handling

In order to make our code robust, we would do Exception handling

Traditional ones use throws throws throws and make exception handling code mixed with program code

That's why we use SEH: try -- catch -- finally

Under class Object we have a class Throwable

And we got Error and Exception

Error won't be handled by program, like running out of memory

Exception can be handled by program

Under Exception we have Runtime Exception

Runtime Exception and Error are unchecked, which means compiler won't check if you have throwed them

Other Exceptions are checked, you need to announce the Exception it may throw after the function like

```java
public static void f() throws Exception{ // If you use public static void f() then the compiler will give a warning
    throw new Exception();
}
```

Also, Exceptions like NullPointerException won't be checked or you can't write code233333

```java
public static void f(int a){
        try{
            System.out.println(a);
        }catch(Exception e){
            e.printStackTrace();
        }
    }
```

You will need to catch every variable's pointer.....

And now we introduce an important constructor

```java
/**
     * Constructs a new exception with the specified detail message and
     * cause.  <p>Note that the detail message associated with
     * {@code cause} is <i>not</i> automatically incorporated in
     * this exception's detail message.
     *
     * @param  message the detail message (which is saved for later retrieval
     *         by the {@link #getMessage()} method).
     * @param  cause the cause (which is saved for later retrieval by the
     *         {@link #getCause()} method).  (A <tt>null</tt> value is
     *         permitted, and indicates that the cause is nonexistent or
     *         unknown.)
     * @since  1.4
     */
    public Exception(String message, Throwable cause) {
        super(message, cause);
    }
```

Means we can now use this to show how the Exception was caused

printStackTrace() method is from Throwable

```java
public synchronized Throwable fillInStackTrace()
```

This method will refresh the snapshot

Always when we open a package we may find some interface, classes and exceptions

Son classes must have the same number of or less kinds of exceptions

Sometimes you will see such exception

```java
public class Main {
    public Main() throws Exception{
        
    };
}
class A extends Main{
    public A() throws Exception{
        
    }
}
```

Seems good!

But if we do something like this

```java
public class Main {
    public Main() throws Exception{

    };
}
class A extends Main{
      public A() { 
        try{
            super();
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

Is there a problem?

Yes!!!!

Because "Call to 'super()' must be first statement in constructor body"

****

Chapter 14

Graphical User Interface ----- GUI（桂老板nb！！）

Two kinds

AWT Abstract Window Toolkit (Almost Gone)

Swing

Why AWT dies?

> This poor design choice makes it hard for programmers who embrace Java's "write once, run everywhere" creed, because AWT does not guarantee how similar their applications will be on various platforms. An AWT application may perform well on windows, but it can hardly be used on Macintosh, or vice versa. In the 1990s, there was a joke among programmers: the true tenet of Java is "write once, test everywhere". One possible reason for this terrible situation is that AWT only took a month from concept generation to implementation.

​																											------From https://zh.wikipedia.org/wiki/AWT

Components and widgets are rendered by Operating System, that makes AWT can't have those Windows, Mac OS or Linux don't have, and always they don't render it the same way

Swing will render using JVM, this will have the all the same experience

```java
import javax.swing.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class Main {
    public static void main(String[] args) {
        JFrame f = new JFrame("Swing1");
        JLabel jLabel = new JLabel("Hello");
        f.add(jLabel);
        f.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                System.exit(0);
            }
        });
        f.setSize(200,200);
        f.setVisible(true);
    }

}
```

using Swing

```java
import java.awt.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class awt {
    public static void main(String[] args) {
        Frame f = new Frame("awt");
        Label jLabel = new Label("Hello");
        f.add(jLabel);
        f.addWindowListener(new WindowAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                System.exit(0);
            }
        });
        f.setSize(200,200);
        f.setVisible(true);
    }
}
```

using AWT

****

Here we add a look and feel example

```java
package com.tony;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class PlafTest {
    public static void main(String[] args) {
        EventQueue.invokeLater(new Runnable() {
            @Override
            public void run() {
                PlafFrame plafFrame = new PlafFrame();
                plafFrame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
                plafFrame.setSize(300,300);
                plafFrame.setVisible(true);

            }
        });    }
}
class PlafFrame extends JFrame{
    JPanel buttonPanel = new JPanel();
    public PlafFrame(){
        setTitle("PlafTest");

        UIManager.LookAndFeelInfo[] infos =  UIManager.getInstalledLookAndFeels();
        for (UIManager.LookAndFeelInfo info : infos){
            makeButton(info.getName(),info.getClassName());
        }
        add(buttonPanel);
    }

    private void makeButton(String name, String className) {
        JButton jButton = new JButton(name);
        buttonPanel.add(jButton);
        jButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                try{
                    UIManager.setLookAndFeel(className);
                    SwingUtilities.updateComponentTreeUI(PlafFrame.this);
                }catch (Exception ex){
                    ex.printStackTrace();
                }
            }
        });
    }
}
```

This shows how Swing use look and feel to achieve the style of components

In this demo there are only 5 buttons (Under Windows 10)

But we can introduce more

********

Things about layout and event model

Layout means size and location. Like responsive design, layout shouldn't be the same

We would use layout manager to manage this

Manage what? Container components

Non-container components won't need this for you wouldn't use them

Existing default layout manager for containers

**FlowLayout** for Panel and **BorderLayout** for Frame

Only when you reform the window will you know what layout it is

FlowLayout

Keep align in center and automatically change line

BorderLayout

Need to announce the position in add() method, like

```java
add(new JButton("Button"), BorderLayout.EAST);
```

The difference is EAST,WEST,NORTH,SOUTH will only change one direction, EAST and WEST change y direction, SOUTH and NORTH will change x direction

Default will be set to CENTER and change both

If you want to add more than one thing to CENTER then add another container

What is an event?

Something triggered by users

High-level events: with specific meaning

Low-level events: with no specific meaning

Event is an object, extends from AWTEvent ==> EventObject

What is event model?

A model of event modifies callback actions and spread directions

COR Chain of Responsibility

Role Analysis of COR

* Client
* Handler
* ConcreteHandler

Variants of COR

* Chain
* Looped Chain
* Tree

In AWT1.0 Event Model

JavaScript Event Model

Like the code below

```html
<body><div> EVENT </div></body>
```

* Event Capturing   --\<body> knows first
* Event Bubbling    -- \<div> itself knows first

Class Observable

@Deprecated(since="9")

MVC Model View Controller

Observer is used to observe Observable objects

```java
public interface Observer{
    void update(Observable o, Objcet arg);
}
```

This update method means binding the observer and object

method addActionListener will do a binding process
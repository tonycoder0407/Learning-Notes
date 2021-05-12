# Java Note 10

Chapter 17

Files, Streams and Object Serialization

method `skip()`

`public long skip(long n) throws IOException`

We can't do seek but we can do read and skip

Create Stream => I/O => Close Stream

```java
package com.tony;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.io.InputStream;

public class Demo {
    public static void main(String[] args) throws IOException {
        System.out.println("Hello");
        InputStream inputStream;
        BufferedReader bufferedReader=
                new BufferedReader(new FileReader("1.txt"));

        System.out.println(bufferedReader.readLine());

        bufferedReader.close();
    }
}
```

Basic use

```java
package com.tony;


import java.io.*;


public class SkipCharacterOOutStream extends FilterOutputStream {
    /**
     * Creates an output stream filter built on top of the specified
     * underlying output stream.
     *
     * @param out the underlying output stream to be assigned to
     *            the field <tt>this.out</tt> for later use, or
     *            <code>null</code> if this instance is to be
     *            created without an underlying stream.
     */
    public SkipCharacterOOutStream(OutputStream out) {
        super(out);
    }

    @Override
    public void write(int b) throws IOException {
        if (b != 'O' && b != 'o') {
            super.write(b);
        }
    }

    public static void main(String[] args) throws IOException {
        byte[] bytes = new byte[1024];
        int content = System.in.read(bytes);
        SkipCharacterOOutStream skipCharacterOOutStream =
                new SkipCharacterOOutStream(System.out);
        skipCharacterOOutStream.write(bytes);
        skipCharacterOOutStream.close();
    }
}

```

Personalized use

```java
	/**
     * Sets the file-pointer offset, measured from the beginning of this
     * file, at which the next read or write occurs.  The offset may be
     * set beyond the end of the file. Setting the offset beyond the end
     * of the file does not change the file length.  The file length will
     * change only by writing after the offset has been set beyond the end
     * of the file.
     *
     * @param      pos   the offset position, measured in bytes from the
     *                   beginning of the file, at which to set the file
     *                   pointer.
     * @exception  IOException  if {@code pos} is less than
     *                          {@code 0} or if an I/O error occurs.
     */
public void seek(long pos) throws IOException {
    if (pos < 0) {
        throw new IOException("Negative seek offset");
    } else {
        seek0(pos);
    }
}
```

`public class RandomAccessFile implements DataOutput, DataInput, Closeable`

This class makes us able to randomly access a file

What is serialization and deserialization?

```java
package java.io;

/**
 * Serializability of a class is enabled by the class implementing the
 * java.io.Serializable interface. Classes that do not implement this
 * interface will not have any of their state serialized or
 * deserialized.  All subtypes of a serializable class are themselves
 * serializable.  The serialization interface has no methods or fields
 * and serves only to identify the semantics of being serializable. <p>
 *
 * To allow subtypes of non-serializable classes to be serialized, the
 * subtype may assume responsibility for saving and restoring the
 * state of the supertype's public, protected, and (if accessible)
 * package fields.  The subtype may assume this responsibility only if
 * the class it extends has an accessible no-arg constructor to
 * initialize the class's state.  It is an error to declare a class
 * Serializable if this is not the case.  The error will be detected at
 * runtime. <p>
 *
 * During deserialization, the fields of non-serializable classes will
 * be initialized using the public or protected no-arg constructor of
 * the class.  A no-arg constructor must be accessible to the subclass
 * that is serializable.  The fields of serializable subclasses will
 * be restored from the stream. <p>
 *
 * When traversing a graph, an object may be encountered that does not
 * support the Serializable interface. In this case the
 * NotSerializableException will be thrown and will identify the class
 * of the non-serializable object. <p>
 *
 * Classes that require special handling during the serialization and
 * deserialization process must implement special methods with these exact
 * signatures:
 *
 * <PRE>
 * private void writeObject(java.io.ObjectOutputStream out)
 *     throws IOException
 * private void readObject(java.io.ObjectInputStream in)
 *     throws IOException, ClassNotFoundException;
 * private void readObjectNoData()
 *     throws ObjectStreamException;
 * </PRE>
 *
 * <p>The writeObject method is responsible for writing the state of the
 * object for its particular class so that the corresponding
 * readObject method can restore it.  The default mechanism for saving
 * the Object's fields can be invoked by calling
 * out.defaultWriteObject. The method does not need to concern
 * itself with the state belonging to its superclasses or subclasses.
 * State is saved by writing the individual fields to the
 * ObjectOutputStream using the writeObject method or by using the
 * methods for primitive data types supported by DataOutput.
 *
 * <p>The readObject method is responsible for reading from the stream and
 * restoring the classes fields. It may call in.defaultReadObject to invoke
 * the default mechanism for restoring the object's non-static and
 * non-transient fields.  The defaultReadObject method uses information in
 * the stream to assign the fields of the object saved in the stream with the
 * correspondingly named fields in the current object.  This handles the case
 * when the class has evolved to add new fields. The method does not need to
 * concern itself with the state belonging to its superclasses or subclasses.
 * State is saved by writing the individual fields to the
 * ObjectOutputStream using the writeObject method or by using the
 * methods for primitive data types supported by DataOutput.
 *
 * <p>The readObjectNoData method is responsible for initializing the state of
 * the object for its particular class in the event that the serialization
 * stream does not list the given class as a superclass of the object being
 * deserialized.  This may occur in cases where the receiving party uses a
 * different version of the deserialized instance's class than the sending
 * party, and the receiver's version extends classes that are not extended by
 * the sender's version.  This may also occur if the serialization stream has
 * been tampered; hence, readObjectNoData is useful for initializing
 * deserialized objects properly despite a "hostile" or incomplete source
 * stream.
 *
 * <p>Serializable classes that need to designate an alternative object to be
 * used when writing an object to the stream should implement this
 * special method with the exact signature:
 *
 * <PRE>
 * ANY-ACCESS-MODIFIER Object writeReplace() throws ObjectStreamException;
 * </PRE><p>
 *
 * This writeReplace method is invoked by serialization if the method
 * exists and it would be accessible from a method defined within the
 * class of the object being serialized. Thus, the method can have private,
 * protected and package-private access. Subclass access to this method
 * follows java accessibility rules. <p>
 *
 * Classes that need to designate a replacement when an instance of it
 * is read from the stream should implement this special method with the
 * exact signature.
 *
 * <PRE>
 * ANY-ACCESS-MODIFIER Object readResolve() throws ObjectStreamException;
 * </PRE><p>
 *
 * This readResolve method follows the same invocation rules and
 * accessibility rules as writeReplace.<p>
 *
 * The serialization runtime associates with each serializable class a version
 * number, called a serialVersionUID, which is used during deserialization to
 * verify that the sender and receiver of a serialized object have loaded
 * classes for that object that are compatible with respect to serialization.
 * If the receiver has loaded a class for the object that has a different
 * serialVersionUID than that of the corresponding sender's class, then
 * deserialization will result in an {@link InvalidClassException}.  A
 * serializable class can declare its own serialVersionUID explicitly by
 * declaring a field named <code>"serialVersionUID"</code> that must be static,
 * final, and of type <code>long</code>:
 *
 * <PRE>
 * ANY-ACCESS-MODIFIER static final long serialVersionUID = 42L;
 * </PRE>
 *
 * If a serializable class does not explicitly declare a serialVersionUID, then
 * the serialization runtime will calculate a default serialVersionUID value
 * for that class based on various aspects of the class, as described in the
 * Java(TM) Object Serialization Specification.  However, it is <em>strongly
 * recommended</em> that all serializable classes explicitly declare
 * serialVersionUID values, since the default serialVersionUID computation is
 * highly sensitive to class details that may vary depending on compiler
 * implementations, and can thus result in unexpected
 * <code>InvalidClassException</code>s during deserialization.  Therefore, to
 * guarantee a consistent serialVersionUID value across different java compiler
 * implementations, a serializable class must declare an explicit
 * serialVersionUID value.  It is also strongly advised that explicit
 * serialVersionUID declarations use the <code>private</code> modifier where
 * possible, since such declarations apply only to the immediately declaring
 * class--serialVersionUID fields are not useful as inherited members. Array
 * classes cannot declare an explicit serialVersionUID, so they always have
 * the default computed value, but the requirement for matching
 * serialVersionUID values is waived for array classes.
 *
 * @author  unascribed
 * @see java.io.ObjectOutputStream
 * @see java.io.ObjectInputStream
 * @see java.io.ObjectOutput
 * @see java.io.ObjectInput
 * @see java.io.Externalizable
 * @since   JDK1.1
 */
public interface Serializable {
}
```

This interface has no specific method but simply announcement

In the same process but different thread, JVM shares location info and references are always available 

Using IPC(Inter Process Communication) we can share the object between processes(Different JVM in the same OS)

But if not on the same machine?

We can use serialization to convert the object into byte stream and using TCP/IP or something like that to share the object

`private static final long seriaVersionUID` is used for checking version of class definition

If we put it to something like "1L", this is called a default one

When put to default, we won't check whether the two number are the same

If we use generated one, then different version will be warned and refuse to continue

Serialization is deeply inside, any object related to this one will be serialized(Like cloned)

Serialization demo:

```java
package com.tony;

import java.io.*;

public class test {
    static Person p1 = new Person("abc",1);
    static Person p2 = new Person("def",2);
    static Person p3;
    static Person p4;
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectOutputStream oos =
                new ObjectOutputStream(new FileOutputStream("test.txt"));
        oos.writeObject(p1);
        oos.writeObject(p2);
        oos.close();

        ObjectInputStream ois =
                new ObjectInputStream(new FileInputStream("test.txt"));
        p3 = (Person) ois.readObject();
        p4 = (Person) ois.readObject();
        System.out.println(p3);
        System.out.println(p4);

    }

}
class Person implements Serializable {
    String pname;
    int pid;

    public Person(String pname, int pid) {
        this.pname = pname;
        this.pid = pid;
    }

    @Override
    public String toString() {
        return "Person{" +
                "pname='" + pname + '\'' +
                ", pid=" + pid +
                '}';
    }
}
```

`BytesArrayInputStream` is used to do I/O for the bytes array in RAM

Some method in class File

* `isFile()`
* `isDirectory()`
* `exists()`
* `canRead()`
* `canWrite()`
* `delete()`
* `createNewFile()`
* ....

***

Chapter 26

Multithreading

Java Concurrent Programming

Threads in JVM may not match threads in the OS

JVM achieves a good mapping between this two

Significantly harder to debug than single thread program

Threads are divided into two kinds

* worker thread
* daemon thread

If all the worker threads are finished, JVM will start to exit

Daemon Threads may not finish what it should do

```java
package com.tony;

public class hello {
    public static void main(String[] args) {
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                f();
            }
        });
        t.setDaemon(true);
        t.start();
        f();
        System.out.println("Main finished........");
    }

    public static void f() {
        for (int i=0;i<300; i++){
            System.out.print(Thread.currentThread().getName());
            System.out.println(" "+i);
        }
    }
}
```

Thread t may not print all the 300 numbers

```java
 /**
     * Waits for this thread to die.
     *
     * <p> An invocation of this method behaves in exactly the same
     * way as the invocation
     *
     * <blockquote>
     * {@linkplain #join(long) join}{@code (0)}
     * </blockquote>
     *
     * @throws  InterruptedException
     *          if any thread has interrupted the current thread. The
     *          <i>interrupted status</i> of the current thread is
     *          cleared when this exception is thrown.
     */
    public final void join() throws InterruptedException {
        join(0);
    }
```

Important method `join()`

Can we call the thread again after it dies?

No.

Method `start()` doesn't directly start the method, it makes the thread ready to run and the scheduling program will decide whether it should start

`System.out.println(Runtime.getRuntime().availableProcessors());`	


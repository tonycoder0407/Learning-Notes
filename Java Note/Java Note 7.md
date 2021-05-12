# Java Note 7

Interface:

* single method
  * Comparable
* multi method
  * Comparator
* marked interface
  * Serializable
  * Cloneable

Interface contains constants and methods

Something like:

```Java
interface I1{
    int size=0;
    void f();
}
```

If a class achieves an interface, you need to achieve all the methods or this class would be an abstract one

The integer "size" would be announced as a final one automatically

```java
public interface Comparable<T>{
	public int compareTo(T o);
}
public interface Comparator<T>{
    int compare(T o1, T o2);
    boolean equals(Object obj);
}
public interface Serializable{
}
public interface Cloneable{
}
```

Modified code:

```java
public class Main {
    public static void main(String[] args) throws CloneNotSupportedException{
        Person p1 = new Person(1,"abc", new Address("123","XinHuaRoad"));
        Person p2 = (Person) p1.clone();

        System.out.println(p1);
        System.out.println(p2);
        System.out.println("========================");
        p2.setAddrName("GuangFuTun");
        p2.setAddrNo("234");
        System.out.println(p1);
        System.out.println(p2);
        System.out.println("========================");
        p2.setAddrName("GuangBaLu");
        p2.setAddrNo("345");
        System.out.println(p1);
        System.out.println(p2);
        System.out.println("========================");

    }
}
class Person implements Cloneable{
    private int pid;
    private String pname;
    private Address address;
// 	  Old method
//    protected Object clone() throws CloneNotSupportedException {
//        return super.clone();
//    }
    protected Object clone() throws CloneNotSupportedException{
        Person p = (Person) super.clone();
        Address address = (Address) this.getAddress().clone();
        p.setAddress(address);
        return p;
    }
    public void setAddrNo(String addrNo){
        address.setStreetNo(addrNo);
    }

    public  void setAddrName(String addrName){
        address.setStreetName(addrName);
    }

    public Person(int pid, String pname, Address address) {
        this.pid = pid;
        this.pname = pname;
        this.address = address;
    }

    public int getPid() {
        return pid;
    }

    public void setPid(int pid) {
        this.pid = pid;
    }

    public String getPname() {
        return pname;
    }

    public void setPname(String pname) {
        this.pname = pname;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "Person{" +
                "pid=" + pid +
                ", pname='" + pname + '\'' +
                ", address=" + address +
                '}';
    }
}
class Address implements Cloneable{
    private String streetNo;
    private String streetName;

    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
    public Address(String streetNo, String streetName) {
        this.streetNo = streetNo;
        this.streetName = streetName;
    }

    public String getStreetNo() {
        return streetNo;
    }

    public void setStreetNo(String streetNo) {
        this.streetNo = streetNo;
    }

    public String getStreetName() {
        return streetName;
    }

    public void setStreetName(String streetName) {
        this.streetName = streetName;
    }

    @Override
    public String toString() {
        return "Address{" +
                "streetNo='" + streetNo + '\'' +
                ", streetName='" + streetName + '\'' +
                '}';
    }
}
```



output:

```
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
========================
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
Person{pid=1, pname='abc', address=Address{streetNo='234', streetName='GuangFuTun'}}
========================
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
Person{pid=1, pname='abc', address=Address{streetNo='345', streetName='GuangBaLu'}}
========================
```

If not modified, then output:

```'
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
Person{pid=1, pname='abc', address=Address{streetNo='123', streetName='XinHuaRoad'}}
========================
Person{pid=1, pname='abc', address=Address{streetNo='234', streetName='GuangFuTun'}}
Person{pid=1, pname='abc', address=Address{streetNo='234', streetName='GuangFuTun'}}
========================
Person{pid=1, pname='abc', address=Address{streetNo='345', streetName='GuangBaLu'}}
Person{pid=1, pname='abc', address=Address{streetNo='345', streetName='GuangBaLu'}}
========================
```

Immutable class objects won't have effect on clone

After clone they still point at the same one but you can change by "new" an object

Primitive ones are stored in their own heap space and can easily change

But others like our own class objects may be not

For we may need to deep clone what is in class Address, you will change both when you change one

Why Cloneable have no method?

You need to achieve clone recursively and stops at the point where your should logically stop

Actually, if we use interface as this:

```java
interface I2{
    void f();
    static void g(){};
    default void t(){};
}
```

Seems java still has multi inheritance 23333333

We now introduce:

``` java 
@FunctionalInterface
interface I2{
    void f();
    static void g(){};
    default void t(){};
}
```

This is ok, actually

But we need to realize that this interface can have only one and must have one abstract method

So you can use lambda:

```java
Runnable runnable = new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello");
        }
    };
Runnable runnable2 = () -> System.out.println("Hello");
```

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

Take a look as the code below:

```java
public int readSth(){
        // step 1 open a file
        // step 2 read the first line
        // step 3 convert the 1st line into a num
        // step 4 return the num
        
        // -------------------------------------------
        int num = 0;
        // step 1
        if (openFile()==0){
            // step 2
            if (readLine()==0){
                // step 3
                if (convert()==0){
                    // step 4
                    return num;
                }
            }
        }else{
            // -------- Error and Exception --------------
        }
        return 0;
    }

    private int readLine() {
        return 0;
    }

    private int convert() {
        return 0;
    }

    private int openFile() {
        return 0;
    }
```

Just show the structure

You see, we may say this code sucks

Now we introduce:

Structured Exception Handling Mechanism

try ---- catch

We can now use Exception object rather than return to judge where exception or error occurs

```java
package com.tony;


public class Main {
    public static void main(String[] args) {
        a();
    }

    private static void a() {
        b();
    }

    private static void b() {
        c();
    }

    private static void c() {
        try {
            d();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void d() throws Exception {
        throw new Exception("This is my first exception");
    }

}
```

output:

```
java.lang.Exception: This is my first exception
	at com.tony.Main.d(Main.java:26)
	at com.tony.Main.c(Main.java:19)
	at com.tony.Main.b(Main.java:14)
	at com.tony.Main.a(Main.java:10)
	at com.tony.Main.main(Main.java:6)

Process finished with exit code 0
```

You will see stack trace and identify where the code stops

And.....

```java
 private static void d()  {
        try {
            throw new Exception("This is my first exception");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

Who will throw exception by left hand and catch it by right hand?

```java
public static void main(String[] args) throws Exception {
        a();
        System.out.println("Main: here");
    }
```

Will we see "Main: here"?

output:

```
Exception in thread "main" java.lang.Exception: This is my first exception
	at com.tony.Main.d(Main.java:23)
	at com.tony.Main.c(Main.java:19)
	at com.tony.Main.b(Main.java:15)
	at com.tony.Main.a(Main.java:11)
	at com.tony.Main.main(Main.java:6)
```

This exception occurs and JVM catches it, print such output

Our exception won't print

```
Exception in thread "main" 
```

Exception handler handles exception in a reverse order than we call them

And we should not use JVM to handle exception rather than we do

Handle exception better as soon as possible

But how soon?

Who the hell will throw exception by left hand and catch it by right hand?

For example, our university wants to collect some information

Our university calls "Collect Info"

And our School continues to run

Our Department continues

Our Grade continues

Our Class continues

And student brief in

But if one student gives wrong info

An exception occurs.

Can the student handle this?

If not, can the class?

One level by one level, the first level which can handle this exception should handle this.

If this level can't then throw it higher 23333333333

If multi exception, you may catch some and catch some

Also, you can simply use Exception to catch all the exception or a father class of two kinds of exception in order to catch them all

Exception is snapshot

Wherever you catch it, the snapshot is always the snapshot, it won't change

And also you can call e.fillInStackTrace() to retake a snapshot

Codes in try will be blocked out if Exception occurs

And catch also has order

If one Exception is caught by 1st catch then 2nd catch won't run

Using try-catch and after catching or throwing, left code still runs

Now introduce finally

try - catch - finally

try -- exception -- catch or throw -- finally

This means finally should do some cleanup

```java
try(Socket s=new Socket()){
    System.out.println(s);
}
```

This code can avoid using catch or finally, for Socket is Closeable

Meaning you can regard it as having a finally

SecurityException extends RuntimeException

RuntimeException can throw without announcing

Why?

NullPointerException extends RuntimeException

If you want to code such things with try-catch:

```java
private static void d(String s){
    System.out.println(s.length);
}
```

WTF?

Of course I know there has a chance that s maybe null

But if I need to use try-catch even if I simply call a reference??????

Game Over.

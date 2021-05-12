# Java Note 12

Managing Java threads

* `Thread.sleep`, `TimeUnit.xxx.sleep`
* yield, sleep
* Deprecated
  * stop
  * suspend
  * resume
* Catch Exceptions thrown from run()
  * Catch before exiting and save
  * `setDefaultUncaughtExceptionHandler`
    * Must wrap checked exceptions as these cannot be thrown from run
* ThreadLocal

```java
package com.tony;

public class ThreadLocalDemo {
    static ThreadLocal<Integer> threadLocal = new ThreadLocal<>();

    public static void main(String[] args) {
        threadLocal.set(100);
        Thread t = new Thread( ()->{
            threadLocal.set(1000);
            f();
        });
        f();
        t.start();
    }

    public static void f(){
        System.out.println(Thread.currentThread().getName()+": "+threadLocal.get());
    }
}
```

This is a ThreadLocal demo =>  More info [here](https://www.jianshu.com/p/3c5d7f09dfbd)

```java
/**
 * This class provides thread-local variables.  These variables differ from
 * their normal counterparts in that each thread that accesses one (via its
 * {@code get} or {@code set} method) has its own, independently initialized
 * copy of the variable.  {@code ThreadLocal} instances are typically private
 * static fields in classes that wish to associate state with a thread (e.g.,
 * a user ID or Transaction ID).
 */
```

Thread Safety In Java

Using *volatile* keyword with variables to make every thread read the date from memory, not read from thread cache

```java
package java.util.concurrent.atomic;
```

In this package we have some utility classes to...

Intrinsic / Explicit locking (JDK1.5)

```java
package java.util.concurrent.locks;
/*
lock(); void
lockInterruptibly(); void
tryLock(); boolean
tryLock(long, TimeUnit); boolean
unlock(); boolean
newCondition(); Condition
*/
```

Think about the code below

```java
/* with two synchronized blocks */
class TwoBlocks{
    final Object lock = new Object();
    synchronized void f(){
        synchronized (lock){
            for (int i = 0; i < 10; i++){
                System.out.println(i);
            }
            synchronized (lock){
                for (int i = 0; i < 10; i++){
                    System.out.println(i);
                }
            }
        }
    }

    public static void main(String[] args) {
        TwoBlocks twoBlocks = new TwoBlocks();
        twoBlocks.f();
    }
}
```



Reentrant lock

ppt staffs

Compare and Swap Algorithm

ppt staffs

Optimistic Lock

ppt staffs

Lock And Condition

ppt staffs

ReadWriteLock and ReentrantReadWriteLock

ppt staffs => ConcurrentHashMap

CyclicBarrier and CountDownLatch

ppt staffs


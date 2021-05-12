# Java Note 11

Concurrent contains parallel

Switch back and forth among tasks on the same processor

Run parallel on multiple processors (Parallel)

Both means concurrent

Keyword `volatile` means this variable is "Easy to change", like the code below

```java
package com.tony;

public class SomeTask implements Runnable{
    private volatile boolean running;
    @Override
    public void run() {
        running = true;
        while (running){
            // ......
        }
        doCleanup();
    }
    private void doCleanup(){
        // ....
    }
    public void setRunning(boolean Running){
        this.running = Running;
    }
}
```

This disables the simply of code by the compiler, for it won't think `while(running)` means `while(true)`

About exception handling, child thread won't give main thread to handle

When you call `Thread.run()` you will find that it won't create a new thread

Method `run()` will not throw exception => Use `RuntimeException` to avoid this

Also, we have a method => `setUncaughtExceptionHandler()` to complete such job

Runnable have only one method void run, makes it unable to pass value

java.lang.Thread

Bare Thread(Thread that created on demand by the user)

When one thread have exceptions throw to JVM, JVM won't directly exit, it will continue to finish others

Using Executor to create and manage threads rather than directly create one

=> We will have a thread pool

java.util.concurrent

class Executor

```java
public interface Executor {

    /**
     * Executes the given command at some time in the future.  The command
     * may execute in a new thread, in a pooled thread, or in the calling
     * thread, at the discretion of the {@code Executor} implementation.
     *
     * @param command the runnable task
     * @throws RejectedExecutionException if this task cannot be
     * accepted for execution
     * @throws NullPointerException if command is null
     */
    void execute(Runnable command);
}
```

Recommend not to new a thread object but use a executor to do this

Thread in the thread pool can be used over again

If the thread pool is not closed then the pool will wait for next instruction

In `ExecutorService` we have such methods `shutdown()`, `shutdownNow()` ....

`shutdown()` simply means no more submission but wait for the old threads to exit

`synchronized` => Only one thread can access this method one time

synchronized block can be used to flexibly handle the problem, more codes can be added to the method

Every Java object can be regarded as a lock

This means synchronized locked the whole object => `synchronized(this)` and any method of the object can be accessed.

Also, you can use `synchronized(this.getClass())` to lock any object of the class

Java Synchronization provides data integrity on the cost of performance

Java Synchronization could result in deadlocks

Synchronize and mutual exclusive

When you are trying to get some resource, make sure you have one to require

And when you try to release some, it doesn't matter for you can always do that

The main status of a thread:

* Runnable
* Running
* Blocked
  * wait()
  * synchronized
  * sleep() or join()

What is the difference?

wait() method will release the lock it has and put it into the waiting pool then go over the lock pool

sleep() or join() method will not release it lock and put it into the blocked pool

synchronized will put the thread in the lock pool if there is a conflict

Also, because you have already been able to do mutual exclusive work and `wait()` will release it
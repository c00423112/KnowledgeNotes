### 线程生命周期

![image-20201102085120226](E:\Github\KnowledgeNotes\picture\image-20201102085120226.png)

1. **新建状态（New）：**当线程对象对创建后，即进入了新建状态，如：Thread t = new MyThread();

2. **可执行（Runnable）：**当调用线程对象的start()方法（t.start();）时，线程从new状态到RUNNABLE状态。操作系统向JVM隐藏了ready状态和运行中的状态，所以只能看到可执行状态。

   ![image-20201102133023354](E:\Github\KnowledgeNotes\picture\image-20201102133023354.png)

3. **阻塞状态（Blocked）：**当一个线程想要执行一个不能马上完成的任务（即需要等待其他的任务完成），线程会从可执行的状态转换到阻塞状态。比如，当一个线程发起输入输出请求，操作系统会阻塞这个人线程，直到IO操作完成了以后，在那一刻，线程从阻塞状态切换回了可执行状态，所以它可以回复执行。一个阻塞的线程不可以使用处理器，即使有处理器可用。

4. **等待(wating)**：运行状态中的线程执行wait()方法，使本线程进入到等待阻塞状态；

5. **定时等待(timed waiting):**如果线程要等待其他线程执行任务并且提供可选的等待时间参数，处于可执行状态的线程可以切换到超时等待的状态。你可以通过调用`sleep(long millis)`方法或者`wait(long millis)`方法来切换线程到定时等待的状态。

6. **终止(terminated):**当一个线程完成了自己的任务或者因为错误或者被强制终止，它会进入终止态（有的时候也叫做死亡态）。



### 线程创建

#### 1、继承Thread类

1）定义 Thread 类的子类，并重写该类的 run 方法，该 run 方法的方法体就代表了线程要完成的任务。因此把 run() 方法称为执行体。

2）创建 Thread 子类的实例，即创建了线程对象。

3）调用线程对象的 start() 方法来启动该线程。

```Java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Hello world!");
    }

    public static void main(String[] args) {
        Thread thread = new MyThread();
        thread.start();
    }
}
```



#### 2、实现Runnable接口

1）定义 runnable 接口的实现类，并重写该接口的 run() 方法，该 run() 方法的方法体同样是该线程的线程执行体。

2)  创建 Runnable 实现类的实例，并依此实例作为 Thread 的 target 来创建 Thread 对象，该 Thread 对象才是真正的线程对象。

3) 调用线程对象的 start() 方法来启动该线程。

```Java
public class MyThread2 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " : " + i);
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            new Thread(new MyThread2(), "Thread-" + i).start();
        }
    }
}
```



#### 3、实现Callable接口通过FutureTask来创建线程

1) 创建 Callable 接口的实现类，并实现 call() 方法，该 call() 方法将作为线程执行体，并且有返回值。

2) 创建 Callable 实现类的实例，使用 FutureTask 类来包装 Callable 对象，该 FutureTask 对象封装了该 Callable 对象的 call() 方法的返回值。

3) 使用 FutureTask 对象作为 Thread 对象的 target 创建并启动新线程。

4) 调用 FutureTask 对象的 get() 方法来获得子线程执行结束后的返回值。

```Java
public class MyThread3 implements Callable<String> {
    @Override
    public String call() throws Exception {
        Thread.sleep(1000);
        System.out.println("current thread is " + Thread.currentThread());
        return "Hello";
    }

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        System.out.println("Start");
        Future<String> future = executorService.submit(new MyThread3());
        try {
            String result = future.get();
            System.out.println("future get result is " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        System.out.println("end");
        executorService.shutdown();
    }
}
```



#### 4、Runnable与Callable区别

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}

@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

- Callable执行任务后可以返回值，Runnable的任务没有返回值
- Runnable的执行体是run()，Callable执行方法是call()
- Callable可以抛出异常，Runnable方法不可以
- Callable任务可以拿到Future对象，表示异步计算的结果
- Runnable可以提交给Thread直接启动一个线程来执行，Callable提交给ExecuteService来执行。



### 线程中断

线程中断有3个重要的方法：

thread.interrupt()：给目标线程发送中断信号，目标线程中断标记置为true，在线程内部的interrupt flag标识，如果一个线程调用了interrupt方法，flag会被设置。但是如果当前线程正处于阻塞状态时，线程将会中断阻塞，并且会抛出InterruptedException异常，并且flag会被清除。

thread.isInterrupted()：返回目标线程的中断标记

thread.interrupted()：返回目标线程的中断标记，并将其置为false，它和isInterrupted()方法有个区别就是该方法会直接清除掉该线程的interrupt标识。

```java
public class MyThread4 extends Thread {
    boolean stop = false;

    public static void main(String[] args) throws InterruptedException {
        MyThread4 myThread4 = new MyThread4();
        myThread4.start();
        Thread.sleep(3000);
        myThread4.interrupt();
        Thread.sleep(3000);
        System.out.println("end！");
    }

    @Override
    public void run() {
        while (!stop) {
            System.out.println("Thread is running");

            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
                if (Thread.currentThread().isInterrupted()) { // 判断是否中断， sleep抛出异常后，被置为false
                    break;
                }
            }
        }
        System.out.println("Thread is stop!");
    }
}
```

```markdown
Thread is running

java.lang.InterruptedException: sleep interrupted
	at java.lang.Thread.sleep(Native Method)
	at Day06.MyThread4.run(MyThread4.java:21)
Thread is running
Thread is running
end！
Thread is running
Thread is running
```

运行后，还会持续进行，原因是sleep方法，当线程被中断时，会抛出中断异常，并且中断符号为会被清除()！









【参考资料】

[Java Thread Life Cycle and Thread States](https://howtodoinjava.com/java/multi-threading/java-thread-life-cycle-and-thread-states/)

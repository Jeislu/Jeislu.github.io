---
title: Java线程的基本方法
date: 2020-12-13 21:29:29
tags:
 - Java
 - 零碎知识点
---

## Java线程的基本方法

### Obeject类

- `void notify()`：唤醒在此对象监视器上等待的单个线程，就是唤醒同一个对象锁上面在等待的线程。
  - 如果有多个线程，则唤醒的线程是随机的。
  - 唤醒的线程不一定会立即执行，它会和其他线程一起争夺CPU。
  - 如果没有持有该对象的锁，则会抛出异常：`IllegaMonitorSateException`；
- `void notifyAll()`：唤醒在此对象监视器上等待的**所有**线程
- `void wait(long timeout)`：在其他线程调用此对象的`notify()` 、`notifyALL`或超过指定的时间(timeout)之前，线程将一直等待
  - 如果timeout为0，则在被唤醒之前一直等待
  - wait不会唤醒其他wait的线程
  - wait会释放对象锁，所以应该放在同步代码块中或者同步方法中
  - wait可能会造成虚假唤醒(文末有介绍)的现象，所以wait应该总是放在wait循环中
- `void wait(long timeout，int nanos)`：和一个参数的wait一样功能，不过提供更小的时间控制，nanos是毫微秒，所以该方法的实际等待时间为1000000*timeout+nanos(毫微秒)
- `void wait()`：相当于wait(0)

### Thread类

- `void start()`：是线程获得运行所需要的资源，进行就绪状态，等待CPU分配资源，若CPU分配资源，则调用该线程的run()方法
- `void run()`：如果当前线程重写了Runnable接口的run方法，则调用此方法。程序直接调用run不是创建一个新的线程，而是把run方法当成一个普通的方法来执行。
- `static void sleep(long millis)`：使当成线程睡眠millis毫秒，注意，释放CPU资源，但是不释放锁！
- `static void sleep(long millis，int nanos)`：提供更精确的时间控制，和两个参数的wait方法一样。
- `void join()`：等待该线程死亡。例如在一个线程里面调用另一个线程的join方法，则该线程会等待被调用线程死亡后执行。该方法会释放CPU资源和锁(执行对象的锁)。join方法内部是使用wait实现的。
- `void join(long millis)`：限制等待死亡的时间，最多等待millis毫秒。
- `void join(long millis，int nanos)`：提供更精确的时间控制。
- `static void yield()`：释放cpu资源，让相同或更高的优先级的线程有执行的机会。注意，这里是机会，释放了不一定就让其他线程执行，也有可能再次让当前线程执行，yield不会释放对象锁。
- `void interrupt()`：改变中断状态，它不会中断一个正在运行的线程。这一方法实际完成的是，给受阻塞的线程发出一个中断信号，这样受阻线程就得以退出阻塞的状态。
  - 如果线程Object.wait, Thread.join和Thread.sleep三种方法之一阻塞，此时调用该线程的interrupt()方法，那么该线程将抛出一个 InterruptedException中断异常（该线程必须事先预备好处理此异常），从而提早地终结被阻塞状态。如果线程没有被阻塞，这时调用 interrupt()将不起作用，直到执行到wait(),sleep(),join()时,才马上会抛出 InterruptedException

## 虚假唤醒

在等待中的多个线程，遇到一个资源被释放，同时被唤醒，但是此时只有一个资源，如果wait是放在if中的，这时候多个资源被错误唤醒，错误访问数据。因此，wait需要放在while循环中，唤醒以后还是得进行资源的判断。
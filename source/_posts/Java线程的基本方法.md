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



## 虚假唤醒


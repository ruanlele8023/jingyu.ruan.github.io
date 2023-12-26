---
layout: post
title: 并发相关理论知识
categories: [Concurrency]
description: 并发需要理解的相关知识点
keywords: Java, Concurrency
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


- 并发问题的根源
  - 原子性 ：
  
    原因：操作系统增加了进程、线程以及分时复用CPU, 进而均衡 CPU 于 IO 设备的速度差异性。
  
    定义： 即一个操作或者多个操作要么全部执行并且执行的过程不会被任何因素打断
  - 可见性：
    原因： CPU 增加了缓存，以均衡于内存的差异性
  - 有序性
    定义： 程序执行的顺序按照代码的先后顺序执行
    - 导致的原因： 
      1. 编译器优化
      2. 指令集并行重排
      3. 内存系统的重排


- 线程安全的 5 种类型
  
  1. 不可变 （immutable） 
  > 与 FP 中的 immutable 相似，减少修改保证线程安全 
  2. 绝对线程安全
  3. 相对线程安全
  4. 线程兼容
  5. 线程对立


- JAVA 解决方法
  
  - 语言层面：
  
    volatile(可见性), final, synchronized
  

  -  JVM 层面: Happens-before 先行发生原则
     - Single Thread rule: 在一个线程内，在程序前面的操作先行于后面的操作
     - Monitor Lock rule: 一个 unlock 操作先行于后面对一个锁的 lock 操作 (锁的重入)
     - Volatile Variable rule: 对一个 volatile 变量的写操作先行于发生于后面的对这个变量的读操作
     - Thread Start rule: Thread 对象的 start() 方法调用先行于发生于后面的对这个变量的读操作
     - Thread Join rule: Thread 对象的结束先行于发生 join() 方法返回
     - Thread Interruption rule (?): 对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 interrupted() 方法检测到是否有中断发生
     - Finalizer rule: 一个对象的初始化完成（构造函数执行结束）先行发生于它的finalize() 方法的开始
     - Transitivity: 如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那么操作 A 先行发生于操作 C。
  

  - 线程安全实现方法
    
     - 互斥同步
      synchronized, reentrantLock
     - 非阻塞同步
      CAS (ABA 问题的引入 atomicStampedReference), atomicXXXX
     - 无同步方案
      栈封闭， Thread Local, 可重入代码


  - 线程使用方式
    1. Runable (无返回值)
    2. Callable (有返回值)
    3. 继承 Thread (缺点 不支持多重继承，继承整个 Thread 开销太大)
  

  - Synchronized 和 ReentrantLock 区别
    
    - 锁的实现： synchronized 是 JVM 层面， ReentrantLock 是 JDK 层面
    - 性能：大致相同， 1.6 版本 synchronized 引入偏向锁和轻量锁
    - 等待可中断： ReentrantLock 可中断， synchronized 不可
    - 公平锁： ReentrantLock 可以为公平，非公平。 默认为非公平， Synchronized 为非公平
    - 锁绑定多个条件： ReentrantLock 可以 AQS 的特性 ， Synchronized 不可以。


  - JVM 中的锁的优化
    
    - 锁粗化（Lock coarsening）: 减少必不可少的紧连在一起的unlock, lock 操作，将多个连续的锁扩展成一个范围更大的锁
    - 锁消除 （Lock Elimination）: 通过运行时JIT 编译器 逃逸分析， 减少不必要的锁
    - 轻量级锁 （Lightweight Lock）
    - 偏向锁 （Biased Locking）
    - 适应性自旋 (Adaptive Spinning)
      
      - 旋锁早在JDK1.4 中就引入了，只是当时默认时关闭的。在JDK 1.6后默认为开启状态。
      - 默认为10次 - XX：PreBlockSpin
      - DK 1.6 之后引入自适应自旋锁: 如果在同一个锁对象上，自旋等待刚刚成功获取过锁，并且持有锁的线程正在运行中，那么JVM会认为该锁自旋获取到锁的可能性很大，会自动增加等待时间
      

参考链接：

- <https://pdai.tech/md/java/thread/java-thread-x-theorty.html>
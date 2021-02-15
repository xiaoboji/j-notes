06-Java并发编程
---
1. 为什么会有多线程

硬件发展的太快了，最近20年的CPU主频上不去了，就需要找一堆机器干同一个活，这个就是分布式的根本来源

多核+分布式时代

多CPU核心意味着同时操作系统有更多的并行计算资源可以使用，操作系统以线程作为基本的调度单元。

单线程是最好处理不过的，线程越多，管理复杂度越高。

2. 线程和进程的区别
- 传统的回答
    * 进程是运行中的程序，线程是进程的内部的一个执行序列
    * 进程是资源分配的单元，线程是执行单元
    * 进程间切换代价大，线程间切换代价小
    * 进程拥有资源多，线程拥有资源少
    * 多个线程共享进程的资源
- 在linux系统中，一切皆文件，线程和进程其实没有明显的区别，都对应的是一个文件路径，进程可以有多个子进程。此时多个子进程可以绑定到一个端口上。

3. Java里创建线程的过程(要映射到系统线程)

Thread.start()--JavaThread--OSthread--Stack--TLAB--启动--Thread#run()--终结

run()就是一个普通的方法，只有执行了start()方法之后，才开始执行线程。

4. java多线程

- 创建线程的两个办法 
    * new Thread()
    * new Runnable()
- 守护线程 = 守护线程：为所有非守护线程提供服务的线程；换句话说，任何一个守护线程都是整个JVM中所有非守护线程的保姆
```
// 因为设置为守护线程，start()的时候发现没有用户线程，守护线程就直接退出了，不会执行上面的run()方法
public static void main(String[] args) {
    Runnable task =
        new Runnable() {
          @Override
          public void run() {
            try {
              Thread.sleep(2000);
            } catch (InterruptedException e) {
              e.printStackTrace();
            }
            Thread t = Thread.currentThread();
            System.out.println("当前线程：" + t.getName());
          }
        };
    Thread thread = new Thread(task);
    thread.setName("bobook-thread-1");
    thread.setDaemon(true);
    thread.start();
  }
```
- Runnable接口和Thread类
    * Thread实现了Runnable接口
    
- 线程状态

   ![avatar](https://github.com/xiaoboji/j-notes/tree/main/Pic/Java-000/线程状态.jpg)
   
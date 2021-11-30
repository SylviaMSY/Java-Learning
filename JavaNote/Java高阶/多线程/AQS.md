

## AQS

Java并发编程工具包——java.util.concurrent.lock当中大多数同步器实现都围绕共同的基础行为，比如等待队列、条件队列、独占获取、共享获取等，而这个行为的抽象 就是基于AbstractQueuedSynchronizer简称AQS，AQS定义了一套多线程访问共享资源 的同步器框架，是一个依赖状态(state)的同步器。

同步器具有共同的基础行为：

- 等待队列
- 条件队列
- 独占获取
- 共享获取

### ReentrantLock

> ReentrantLock是一种基于AQS框架的应用实现，是JDK中的一种线程并发访问的同步 手段，它的功能类似于synchronized是一种互斥锁，可以保证线程安全。而且它具有比 synchronized更多的特性，比如它支持手动加锁与解锁，支持加锁的公平性

**ReentrantLock实现公平与非公平性:**

> 在ReentrantLock内部定义了一个Sync的内部类，该类继承AbstractQueuedSynchronized，对该抽象类的部分方法做了实现；并且还定义了两个子类：
>
> 1、FairSync 公平锁的实现 
>
> 2、NonfairSync 非公平锁的实现
>
> 这两个类都继承自Sync，也就是间接继承了AbstractQueuedSynchronized，所以这一个 ReentrantLock同时具备公平与非公平特性。 
>
> 主要涉及的设计模式：模板模式-子类根据需要做具体业务实现

``` java
 //使用ReentrantLock进行同步
ReentrantLock lock = new ReentrantLock(false);//false为非公平锁，true为公平锁
lock.lock() //加锁
lock.unlock() //解锁
```

### lock锁原理

``` java
RentrantLock lock = new ReentrantLock(false);//false非公平锁
//3个线程：t0、t1、t2
lock.lock()
	while(true){
        if(cas加锁成功){//compare and swap 交换与比较算法，保证抢锁的原子性，只能有一个抢锁成功
         break;
        }
        //Thread.yeild() //让出CPU使用权
        //Thread.sleep(1)
        //HashSet,LinkedQueue都可以，建议queue，满足FIFO
        HashSet.add(Thread)
        linkedQueue.put(Thread)
        LockSupport.park();//阻塞当前线程
    }	
//t0获取锁
//xxx业务逻辑
lock.unlock()
Thread t= HashSet.get();
Thread t= linkedQueue.take();
LockSupport.unpark(t);//唤醒线程
```

**lock锁原理三个核心：**

- 自旋：while锁循环
- locksSupport：park()、unpark()，类似wait、notify
- CAS算法：compare and swape（加锁）
- queue队列：用来保存thread引用



在idea中查看类uml图：ctrl+shift+alt+u

Diagram->show Diagram

show implementations

<img src="C:\Users\MSY\AppData\Roaming\Typora\typora-user-images\image-20210915111008138.png" alt="image-20210915111008138" style="zoom: 67%;" />


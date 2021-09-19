# JUC

# 前三章

1. JMM的作用
   * java线程的通信由JMM控制，JMM的主要目的是定义程序中各个变量的访问规则。变量包括实例字段、静态字段。
   * 基本原则：只要不改变程序的执行结果，编译器和处理器怎么优化都可以。
   * JMM规定所有的变量都存储在主内存，每条线程有自己的工作内存。
2. as-if-serial
3. happens-before
4. **synchronized锁升级过程**（无锁状态、偏向锁、轻量级锁、重量级锁）synchronized的锁是存在Java对象头里面的Mark Word里面
   * 线程A在进入同步代码块前，先检查Mark Word中的线程ID是否与当前线程ID一致，如果一致，则无需进行加锁操作
   * 如果不一致，再检查是否为偏向锁，如果不是，则自旋等待锁释放
   * 如果是，再检查线程是否存在（偏向锁不会主动释放锁），如果不在，则设置线程ID为线程A的ID，此时依然是偏向锁
   * 如果还在，则暂停该线程，同时将锁标识位设置为00即轻量级锁，（将MarkWord复制到该线程的栈帧中，并将MarkWord设置为栈中锁记录）。线程A自旋等待锁释放。
   * 如果自旋次数到了该线程还没有释放锁，或者该线程还在执行，线程A还在自旋等待，这时又有一个线程B来竞争这个锁对象，那么轻量级锁回膨胀为重量级锁。重量级锁回把除了拥有锁的线程都阻塞，防止CPU空转。
   * 如果该线程释放锁，则回唤醒所有阻塞线程，重新竞争锁。
5. Lock和synchronized的区别
   * Lock是juc中的一个接口，synchronized是一个关键字
   * Lock可以响应中断，而synchronized却不可以
   * 当出现异常时，Lock不会自动释放锁，需要在finally里面显示释放，而synchronized能自动释放锁
6. 条件判断的时候，应该用while，再进行wait，否则会出现虚假唤醒

# 第4章 JAVA并发基础



1.  为什么要使用多线程
   * 更多的处理器核心
   * 更快的响应时间
   * 更好的编程模型

2. 线程优先级（设置优先级可能没有生效，操作系统可以不用理会java设置的优先级）
   * IO较多的线程优先级， 设置的较高的优先级
   * 偏重计算的线程则设置较低的优先级
3. java线程的状态
   * NEW 初始状态
   * RUNNABEL： 运行状态（包括操作系统中的就绪状态和运行状态）
   * BLOCKED：阻塞状态
   * WAITING： 等待状态
   * TIME_WAITING：超时等待状态
   * TERMINATED： 终止状态

4. 线程间的通信
   * volatile和synchronized
   * 等待和唤醒wait与notify（notifyAll）
   * 管道输入、输出流
   * Thread.joint使用
     * 当前线程A等待Thread线程终止之后才从Thread.join()返回
   * ThreadLocal使用
     * 以ThreadLocal对象为键、任意对象为值的存储结构

# 第五章 JAVA中的锁

1. 队列同步器**AbstractQueueSynchronizer**

   * 概念：用来构建锁或者其他同步组件的基础框架，它使用一个**int成员变量**表示同步状态，通过内置的**FIFO队列**来完成资源获取线程的排队工作

   * 三个方法

     ``` java
     	int getState();
     
     ​	void setState();
     
     ​   boolean compareAndSetState(int expect, int update) 
     ```

   * 同步器的实现

     * 同步队列

       1. 一个FIFO双向队列

       2. 节点Node的内容(等待队列和同步队列共用)

          ```java
          int waitStatus; // 等待状态 包括CANCELLLED, SIGNAL, CONDITION, PROPAGATE, INITIAL
          Node prev;
          Node next;
          Node nextWaiter;
          Thread thread;
          ```

          

     * 独占式同步状态获取与释放

     * 共享式同步状态的获取与释放

     * 超时获取同步状态

2.  **重入锁ReentrantLock**

   * 该锁能够支持一个线程对资源的重复加锁
   * 锁公平：先对锁进行获取请求的一定先被满足，那么这个锁就是公平的锁，否则式不公平的
   * 默认式非公平锁（减少线程切换的次数，提高吞吐量）

3. **读写锁ReentrantReadWriteLock**

   ```java
   Lock readLock();
   Lock writeLock();
   ```

   * 读写锁状态设计
     * 高16位为读状态，低16位为写状态（S >>> 16, S & 0x0000FFFF)

4. **LockSupport**

   共享资源为1，许可证

   ```java
   void park();// 阻塞当前线程
   void unpark(Thread thread); // 唤醒阻塞状态的线程
   ```

5. Condition接口

   * 获取一个Condition必须通过Lock的Condition() 方法

   ```java
   Lock lock = new ReentrantLock();
   Condition condition = lock.newCondition();
   ...
   lock.lock();
   try{
       condition.await();
   }finally{
       lock.unlock();
   }
   
   ....
   lock.lock();
   try{
       condition.signal();
   }finally{
       lock.unlock();
   }
   ```

   在Object的监视器模型上，一个对象拥有一个同步队列和等待队列，而并发包中的Lock拥有一个同步队列和多个等待队列

# 第6章 JAVA并发容器和框架



1. **ConcurrentHashMap**

   * ConcurrentHashMap式线程安全且高效的HashMap。
   * 为什么要使用ConcurrentHashMap
     1. 线程不安全的HashMap在并发执行put，会导致Entry链表形成环形数据结构
     2. 效率低下的HashTable
     3. ConcurrentHashMap的**锁分段技术**可以有效提升并发访问率

   * 结构： segment数组和HashEntry数组
2. **ConcurrentLinkedQueue**

   * 入队列的尾指针不一定指向尾部
3. **Java中的阻塞队列**

   * 定义：支持两个附加操作的队列。（插入和移除操作）
     * 支持阻塞的插入方法：当队列满时，队列会阻塞插入元素的线程，直到队列不满
     * 支持阻塞的移除方法：当队列为空时，获取元素的线程会等待队列变为非空
   * **DelayQueue**
     * 支持延迟获取元素的无界阻塞队列，队列使用PriorityQueue来实现.
     * 队列中的元素必须实现delayed接口，在创建元素时可以指定多久才能从队列中获取当前元素
     * 应用场景
       * 缓存系统设计。保存缓存元素的有效期
       * 定时任务调度
4. **Fork/Join 框架**
   * Java 7 提供的一个用于并行执行任务的框架，是一个把大任务分割成若干小任务，最终汇总每一个小任务后得到大任务结果的框架。
   * **工作窃取算法**
     * 某个线程从其他队列里窃取任务来执行。



# 第7章 Java中的13个原子操作类

基本类型、数组、引用、属性

1. **原子更新基本类型类**

   * AtomicBoolean

   * **AtomicInteger**

     * ```java
       int addAndGet(itn delta); // 输入的值和AtomicInteger.value相加，并返回
       boolean compareAndSet(itn expect, int update); // 输入的数值等于预期值，则以原子的方式给该值设置为输入的值
       int getAndIncrement(); // 原子的方式加1，返回自增前的值
       int getAndSet(int newValue); // 以原子的方式设置,并返回旧值
       ```

   * AtomicLong

2. **原子更新数组**

   * **AtomicIntegerArray**  原子更新整型数组的元素

     * ```java
       int addAndGet(int i, int delta); //以原子的方式，对下标i的元素相加
       boolean compareAndSet(int i, int expect, int update);
       ```

   * AtomicLongArray

   * AtomicRereferenceArray

3. **原子更新引用类型**
   
   * AtomicReference
   * AtomicReferenceFieldUpdater
   * AtomicMarkableReference
4. **原子更新字段类**
   
   * AtomicIntegerFieldUpdater：原子更新整型类型的字段的更新器
   * AtomicLongFieldUpdater: 原子更新长整型的更新器
   * AtomicStampedReference：原子更新带有版本好的引用类型
     * 静态的newUpdater()
     * 更新类的属性必须使用public volatile





# 第8章 Java 中的并发工具类

1. **等待多线程完成的CountDownLatch**

   * 允许一个或多个线程等待其他线程完成操作

     ```java
     static CounDownLatch cdl = new CountDownLatch(2);//等待2个线程执行完
     cdl.countDown(); // N 减一
     cdl.await(); // N不是1时，一致阻塞
     ```

2. **同步屏障 CyclicBarrier**

   * 让一组线程到达一个屏障（同步点）时被阻塞，直到最后一个线程到达屏障时，所有被屏障拦截的线程i继续运行。

   * ```java
     CyclicBarrier cb = new CyclicBarrier(in parties);//参数表示屏障拦截的线程数
     //每个线程调用
     cb.await();///表示到达屏障
     
     CyclicBarrier cb = new CyclicBarrier(in parties， Runnable barrierAction); //开门时，优先执行barrierAction
     ```

   * **CyclicBarrier 和 CountDownLatch的区别**

     * CountDownLatch的计数器只能使用一次，而CyclicBarrier可以调用reset() 方法重置

3. **控制并发线程数的Semaphore**

   * Semaphore （信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。

   * ```java
     Semaphore semaphore = new Semaphore(10); //只能同时并发10个线程,10个许可证
     
     semaphore.acquire();
     semaphore.release();
     ```

4. **线程间交换数据的Exchanger**
   
   * Exchanger(交换者) 用于线程间协作，数据交换。提供一个同步点，在同步点，两个线程可以交换彼此的数据。
   * 应用场景：遗传算法、校对工作





# 第9章 Java中的线程池

* **优势**
  * 降低资源消耗
  * 提高响应速度
  * 提高线程的可管理性

1. **线程池的实现原理**

   * **当提交一个新任务到线程池，线程池执行流程为**

     * 线程池判断核心线程池里的线程是否都在执行任务。若不是，则创建一个新的工作线程来执行任务。如果核心线程池里的任务都在执行，则进入下一个流程。
     * 线程池判断工作队列是否已满。若工作队列没满，则将新任务存储在队列中，否则进入下一个流程。
     * 判断线程池的线程是否都处于工作状态（最大线程数）。如果没有，则创建一个新的工作线程来执行任务。否则执行饱和策略。

   * **线程池的使用**

     * 创建 ThreadPoolExecutor()

       ```java
       new ThreadPoolExecutor(corePoolSize, maximumPoolsize, keepAliveTime, millisencods,runnableTaskQueue, handler);
       // corePoolSize 线程池的核心线程数。当新任务到达时，先判断活动线程数是否到达线程池的核心数，若没有，就算核心线程里面的线程是空闲的，也要创建新的线程。
       /*
        runnableTaskQueue (任务队列) ：ArrayBlockingQueue、LinkedBlockingQueue(Exectuors.newFiexdThreadPool()), SynchronousQueue(Exectors.newCachedThreadPool()),PriorityQueue
        maximumPoolsize: 最大线程数。如果使用了无界的任务队列，则这个参数没有效果
        handler：饱和策略： AbortPolicy:直接抛出异常，CallerRunsPolicy:只用调用者所在的线程执行任务，DiscardOldestPolicy:丢弃队列里面前面的一个任务，并执行当前任务，DiscardPolicy：不处理，丢弃
        其余参数是用来创建线程的：
        	keepAliveTime：线程池的工作线程空闲后，保持存活时间 millisencods： 存活时间单位
        */
       ```

     * 向线程池提交任务

       * execute() 提交不需要返回值的任务

         ```java
         threadPool.excute(new Runnable()...)
         ```

       * submit() 提交需要返回值的任务

         ```java
         Future<Object> future = executor.submit(hasReturnValueTask);
         Object s = future.get(); //返回结果
         ```

       * 关闭线程

         shutdown或shutdownNow

         ​	shutdown将线程的状态设置SHUTDOWN状态，若不可中断，任务可能无法终止

         ​	shutdownNow状态设置为stop，尝试停止所有的正在执行或者暂停的线程。

     * 合理地配置线程池

       * 根据任务的特性
         * CPU密集型任务应配置尽可能小的线程，N_cpu +1 
         * IO密集型任务，2 * N_cpu
       * 建议使用有界队列

       
       
       

# 第10章 Executor框架

1. 两级调度

   * 应用程序通过Executor框架控制上层调度；而下层调度有操作系统内核控制，下层的调度不受应用程序的控制
   * Java线程被一对一映射为本地操作系统线程

2. 组成结构：

   * 任务：被执行任务需要实现的接口：Runnable（不会返回结果）和Callable接口（可以返回结果）
   * 任务的执行：Executor接口或ExecutorService接口。实现类有ThreadPoolExecutor和ScheduledThreadPoolExecutor
   * 异步计算结果。包含 Future接口和实现Future接口的FutureTask类

3. 使用流程

   * 创建任务Callable对象
   * 将Callable对象提交给ThreadPoolExecutor或ScheduledThreadPoolExecutor.
   * submit() 返回一个 FutureTask对象
   * FutureTask.get()得到异步结果

   






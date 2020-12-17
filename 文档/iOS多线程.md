#iOS多线程

[TOC]

- iOS四种多线程:**Pthread, NSThread, GCD, NSOpreation**

##iOS四种多线程方案:**Pthread, NSThread, GCD, NSOpreation**

###Pthead

- **pthread是跨平台的多线程API,需要程序员自己管理线程的生命周期**

###NSthread

- **NSThread是面向对象的多线程实现方案,需要程序员自己管理线程的生命周期。**
- 在开发过程中，常常使用**[NSThread currentThread]**来显示进程的信息.

###GCD

- **GCD是苹果专门更好利用多核的线程方案,自动管理生命周期.**

####GCD的任务和队列

- **任务**:指执行的操作,线程中执行的那段代码,GCD放在Block中,执行任务分为:**同步执行**和**异步执行**,主要区别是:**是否等待队列的任务执行结束,是否具备开启新线程的能力**
 - **同步执行(sync)**:等待上一个任务执行完成,才能继续执行下一个任务,且不具备开启新线程的能力.
 - **异步执行(async)**:添加到队列中不用等待,就可以执行任务,且具备开启新线程的能力.
- **队列(Dispatch Queue)**:指执行任务的等待队列,队列是一种采用FIFO原则的特殊线性表,主要有**串行队列**和**并行队列**两种,主要区别是**执行顺序不同,以及开启线程数不同**
 - **串行队列(Serial Dispatch Queue)**:每次只有一个任务被执行,只有一个线程.
 - **并发队列(Concurrent Dispatch Queue)**:可以让多个任务并发执行,可以开启多个线程(并发队列只有在异步方法下才有效).

#####队列的创建/获取方法

- 使用**dispatch_queue_create**创建队列,两个参数:
 - 第一个参数是队列的唯一标识符,用于DEBUG,可为空
 - 第二参数识别是串行对垒还是并发队列,DISPATCH_QUEUE_SERIAL表示串行队列,DISPATCH_QUEUE_CONCURRENT表示并发队列.
```OC
// 串行队列的创建方法
dispatch_queue_t queue = dispatch_queue_create("net.bujige.testQueue", DISPATCH_QUEUE_SERIAL);
```
- 对于串行队列,默认提供了主队列,使用**dispatch_get_main_queue()**来获取.
- 对于并发队列,默认提供了全局并发队列,使用**dispatch_get_global_queue**来获取,有两个参数,一个参数表示队列优先级,一般使用**DISPATCH_QUEUE_PRIOPRITY_DEFAULT**,第二个参数没用,用0即可.

#####任务的创建方法

- GCD提供了同步执行任务的创建方法:**dipatch_sync**和异步执行任务创建方法**dispatch_async**.


####GCD的其它方法

#####1.GCD栅栏方法:dispatch_barrier_async

- **dispatch_barrier_async**是对同一个队列的各个任务而设置的一个结点，方法会等待前面的并发队列执行完毕,才会继续执行后序的方法.
- 作用：实现高效率的数据库访问和文件访问（同步），避免数据竞争（特定到某点之后来单独处理）

#####2.GCD延时执行方法:dispatch_after

- **dispatch_after**不是指定时间之后才开始执行,而是在指定时间之后将任务追加到主队列中,这个时间并不是绝对准确的.

#####3.GCD一次性代码:dispatch_once

- **dispatch_once**常用于单例,在程序运行过程中只被执行一次,多线程环境下也能保证线程安全.

#####4.GCD快速迭代方法:dispatch_apply

- **dispatch_apply**是按照指定的次数将指定的任务追加到指定的队列中,并等待全部队列执行结束,类似于for循环的功能.

#####5.GCD队列组:dispatch_group

- **dispatch_group_t**创建队列组,通过**dispatch_group_async**把任务放到队列组中,或者使用**dispatch_group_enter(任务+1)**、**dispatch_group_leave(任务-1)**组合实现**dispatch_group_async**
- 使用**dispatch_group_notify**等待队列组任务执行完成,回到指定线程执行任务;也可以使用**dispatch_group_wait**阻塞线程完成在继续向下执行

#####6.GCD信号量:dispatch_semaphore

- 信号量是指持有计数的信号,在小于0是等待,在等于0或大于0时,计数-1且不等待执行,提供的三个方法:
 - **dispatch_semaphore_create**:创建一个Semaphore并初始化信号的总量
 - **dispatch_semaphore_singal**:发送一个信号量,让信号量+1
 - **dispatch_semaphore_wait**:使信号量-1,信号总量小于0时就会一直等待(阻塞线程)
- Dispatch Semaphore主要作用:
 - 保持线程同步,将异步执行任务转换为同步执行任务
 - 保证线程安全,为线程枷锁

###NSOperation和NSOperationQueue

- **面向对象的多线程方案，不需要自己管理线程的生命周期,可以方便的取消一个操作的执行,可以使用kvo观察操作执行状态的更改:isExecuteing/isFinished/isCancelled.**
- **操作指的是线程中执行的那段代码,使用NSOperation子类NSInvocationOperation、NSBlockOperation，或者自定义子类来封装操作**
- **操作队列是指存放操作的队列，进入就需状态的开始顺序执行，可以设置最大并发操作数(1为串行,大于1为并行)**

- 添加依赖和移除依赖

```OC
- (void)addDependency:(NSOperation *)op
- (void)removeDependency:(NSOperation *)op
- 
```

- 可以使用**queuePriority**属性设置优先级,优先级不能取代依赖关系
- (void)setSuspended:(BOOL)b; 可设置操作的暂停和恢复，YES 代表暂停队列，NO 代表恢复队列,而isCancelled是不可以在恢复的取消

##线程之间的通信

- **线程之间的通信,比方说在子线程执行耗时的操作,结束后再回到主线程刷新UI,就需要线程之间的通信**.

###方案一:NSThread的performSelector:方法

```oc
//回到主线程执行:
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait;

// 在指定线程上执行操作
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait modes:(NSArray *)array NS_AVAILABLE(10_5, 2_0);
// 在当前线程上执行操作，调用 NSObject 的 performSelector:相关方法
- (id)performSelector:(SEL)aSelector;
```

###方案二:GCD的获取主线程或者全局队列

- 比方说获取全局队列dispatch_get_global_queue执行耗时任务,在通过dispatch_get_main_queue方法回到主队类刷新UI.



**[iOS多线程：『pthread、NSThread』详尽总结](https://juejin.im/post/6844903556009443335)**
**[iOS多线程：『GCD』详尽总结](https://juejin.im/post/6844903566398717960#heading-18)**

###方案三:NSOperation获取主队列操作

- 在耗时操作放在其他线程,在通过mainQueue方法回到主线程刷新UI

##线程安全

- **多个线程同时运行时,执行结果确定,且可重复性执行,那么我们就认为是线程安全的**
- iOS实现线程线程安全可以给线程枷锁,加锁的机制有:@synchronized、 NSLock、NSRecursiveLock、NSCondition、NSConditionLock、pthread_mutex、dispatch_semaphore、OSSpinLock、atomic(property)等方式

##iOS线程池

- NSThread是程序员自己处理线程的生命周期,而GCD和NSOperation则是丢给线程池去管理线程的生命周期.
- 可以通过dispatch_semaphore_t去实现线程池指定线程数量的方法

**[dispath_barrier_async和dispatch_group](http://vin-zhou.github.io/2017/10/17/%E8%AF%B4%E8%AF%B4dispath_barrier_async%E5%92%8Cdispatch_group/)**
